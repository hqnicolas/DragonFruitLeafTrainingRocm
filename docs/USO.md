# Uso do classificador

O modelo publicado para inferência está em
[`../artifacts/pitaya/classificador_pitaia.onnx`](../artifacts/pitaya/classificador_pitaia.onnx).
O mapa [`classes_pt-BR.json`](../artifacts/pitaya/classes_pt-BR.json) associa
cada índice de saída ao rótulo correto. A imagem é convertida para RGB,
redimensionada para 224 × 224 e normalizada com média e desvio padrão ImageNet.
O modelo retorna logits; a ferramenta aplica softmax e devolve probabilidades.

## Executar o serviço

O pacote independente está em `deployment/classificador-folha/` e também foi
copiado para `Pitaya/deploy/classificador-folha/`. Ele usa ONNX Runtime com
provedor CPU para manter a inferência leve e portátil; o treinamento do modelo
permanece ROCm e não tem fallback para CPU.

No projeto Pitaya, construa e inicie o serviço:

```bash
docker build -t pitaya-classificador-folha ./deploy/classificador-folha
docker run --rm -p 8080:8080 pitaya-classificador-folha
```

Verifique a disponibilidade:

```bash
curl http://localhost:8080/healthz
```

Envie os bytes da imagem diretamente no corpo da requisição:

```bash
curl -X POST http://localhost:8080/classificar \
  -H 'Content-Type: image/jpeg' \
  --data-binary @folha.jpg
```

A resposta inclui a classe mais provável, sua confiança e as três maiores
probabilidades. O serviço aceita imagens JPEG, PNG, WebP, BMP e TIFF de até
20 MiB. O código HTTP `422` indica imagem inválida, `413` indica arquivo acima
do limite e `415` indica tipo de conteúdo não aceito.

## Arquivo para implantação

O pacote compactado está em
[`../deployment/pitaya-classificador-folha-v1.tar.xz`](../deployment/pitaya-classificador-folha-v1.tar.xz)
e também foi copiado para `Pitaya/deploy/`. Dentro de `Pitaya/`, extraia com:

```bash
tar -xJf deploy/pitaya-classificador-folha-v1.tar.xz -C deploy
```

Isso cria `deploy/classificador-folha/`. O arquivo contém o serviço, o modelo
ONNX e o mapa de classes. O checkpoint `.pth` e as imagens do treinamento ficam
neste repositório e não entram no pacote de implantação.

O serviço HTTP está preparado para ser chamado pelo backend do Pitaya. O chat
atual ainda precisa receber/encaminhar a imagem e chamar este endpoint para
apresentar a análise diretamente na conversa.

## Interpretar o resultado

As probabilidades descrevem a escolha do modelo entre as dez classes treinadas;
não são diagnóstico agronômico nem estimativa calibrada de severidade. Fotos
com boa iluminação, foco e o cladódio ocupando a imagem tendem a ser mais úteis.
Consulte as métricas por classe em `artifacts/pitaya/metricas.json`, sobretudo
para classes com pouco suporte no teste.
