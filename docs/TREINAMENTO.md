# Treinamento

O notebook [`../Dragon_Fruit_Full_Code.ipynb`](../Dragon_Fruit_Full_Code.ipynb)
reaproveita o fluxo do projeto Dragon Fruit Application, adaptado para as dez
classes pt-BR. Ele carrega pesos pré-treinados ImageNet na MobileNetV3-Small,
treina e seleciona o checkpoint pela perda de validação, calcula métricas no
conjunto de teste e exporta ONNX.

## ROCm e desempenho

O treinamento foi executado na AMD Radeon RX 7800 XT (`gfx1101`) com PyTorch
`2.14.0+rocm7.14` e HIP `7.14.60850`. O dispositivo de treino é sempre
`cuda:0` no PyTorch ROCm; o notebook interrompe a execução quando HIP ou a GPU
não estão disponíveis. A CPU só decodifica cada imagem uma vez. Resize,
armazenamento do conjunto preparado, amostragens, aumentos, normalização,
modelo, loss e gradientes são feitos na GPU.

Os lotes foram comparados antes do treino. O lote 32 foi escolhido: cerca de
466 imagens/s no ensaio de lotes, com uso de aproximadamente 0,79 GiB de VRAM.
O carregador serial usado como referência processava cerca de 14,5 imagens/s.
Uma amostra sustentada de uso do GPU registrou cerca de 475 imagens/s durante
31 segundos, com utilização de GPU em 100% e pico alocado de 0,67 GiB. Os
ensaios são medições desta máquina e não garantem a mesma velocidade em outro
host.

## Divisão e métricas

As imagens derivadas, rotações e duplicatas foram agrupadas antes da divisão;
25 grupos com rótulos conflitantes foram descartados. A divisão resultante tem
517 grupos de treino, 112 de validação e 109 de teste. Veja as métricas completas
em [`../artifacts/pitaya/metricas.json`](../artifacts/pitaya/metricas.json).

| Divisão | Acurácia | Acurácia balanceada | Macro F1 |
| --- | ---: | ---: | ---: |
| Validação | 87,50% | 76,99% | 79,03% |
| Teste | 90,83% | 87,20% | 86,09% |

O suporte de algumas classes é pequeno: Mancha branca e Mancha preta têm duas
imagens cada no teste, e várias doenças têm de quatro a cinco. Interprete as
métricas dessas classes com essa incerteza em mente.

O treinamento de retomada chegou ao critério de parada antecipada sem superar
a perda de validação do melhor checkpoint já existente. Por isso esse
checkpoint foi preservado; as métricas foram recalculadas e o ONNX foi exportado
novamente a partir dele. O ONNX recebe `float32 [1, 3, 224, 224]`, retorna dez
logits e apresentou diferença máxima de `8,106e-6` em relação ao checkpoint
PyTorch para a mesma entrada.

## Repetir o treinamento

Use Linux com `/dev/kfd`, driver ROCm funcional e uma instalação compatível de
PyTorch ROCm e TorchVision. Instale os pacotes listados em
[`../requirements-treino.txt`](../requirements-treino.txt) no mesmo ambiente.
Abra o notebook a partir desta pasta. O notebook identifica aqui os dados e
grava os resultados em `artifacts/pitaya/`. Os pesos ImageNet da TorchVision
podem precisar ser baixados na primeira execução.
