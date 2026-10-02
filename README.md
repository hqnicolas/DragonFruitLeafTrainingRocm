# Classificador de cladódios de pitaia

Repositório com os dados usados, o notebook de treinamento ROCm e os artefatos
do classificador de imagens de cladódios (chamados de “folhas” nas pastas de
origem). O modelo é uma MobileNetV3-Small com dez saídas: nove doenças e a
classe **Saudável**.

## Resultado do treinamento

- GPU: AMD Radeon RX 7800 XT, ROCm/HIP 7.14; treino executado em `cuda:0` por
  meio do PyTorch ROCm. O notebook falha se a GPU ROCm não estiver disponível.
- Divisão sem vazamento de imagens relacionadas: 517 grupos de treino, 112 de
  validação e 109 de teste.
- Teste: acurácia **90,83%**, acurácia balanceada **87,20%**, macro F1
  **86,09%**.
- O checkpoint, o ONNX, o mapa pt-BR e as métricas estão em
  [`artifacts/pitaya/`](artifacts/pitaya/).

O relatório detalhado e os limites dos resultados estão em
[`docs/TREINAMENTO.md`](docs/TREINAMENTO.md). Para inferência e implantação,
consulte [`docs/USO.md`](docs/USO.md). Os dados e sua organização estão
descritos em [`docs/DADOS.md`](docs/DADOS.md). As versões ROCm usadas estão em
[`docs/AMBIENTE-TREINO.md`](docs/AMBIENTE-TREINO.md).

## Conteúdo

- `Dragon_Fruit_Full_Code.ipynb`: notebook de treino, avaliação e exportação.
- `Folha/` e `Dragon Fruit (Pitahaya)/Original Images/`: imagens de entrada
  consideradas pelo notebook, sem as imagens sintéticas `aug_*`.
- `artifacts/pitaya/classificador_pitaia.pth`: checkpoint PyTorch.
- `artifacts/pitaya/classificador_pitaia.onnx`: modelo de inferência ONNX.
- `artifacts/pitaya/classes_pt-BR.json`: ordem das saídas e pré-processamento.
- `artifacts/pitaya/metricas.json`: métricas por divisão e por classe.
- `deployment/classificador-folha/`: fonte do serviço de inferência enviado ao
  projeto Pitaya.
- `deployment/pitaya-classificador-folha-v1.tar.xz`: pacote portátil para
  implantação do serviço.

## Classes na ordem dos logits

1. Antracnose
2. Cancro do caule
3. Mancha branca
4. Mancha-marrom
5. Mancha preta
6. Podridão da raiz
7. Podridão do caule
8. Podridão mole
9. Queima dos ramos
10. Saudável

`Brown Stem Spot` foi consolidada em `Mancha-marrom`, e `Gray Blight` em
`Queima dos ramos`, conforme a taxonomia aprovada para este classificador.
Essa consolidação de rótulos não confirma equivalência etiológica.
