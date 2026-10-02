# Ambiente registrado

Ambiente da execução usada para produzir os artefatos deste repositório:

- Sistema: Linux x86_64, kernel `7.0.0-38-generic`.
- GPU: AMD Radeon RX 7800 XT, arquitetura `gfx1101` (identificada pelo
  PyTorch ROCm); 16 GiB de VRAM.
- ROCm/HIP: `7.14.60850`.
- Python: `3.14.4`.
- PyTorch: `2.14.0+rocm7.14`.
- TorchVision: `0.29.0+rocm7.14`.
- Pillow: `12.3.0`; NumPy: `2.5.3`; scikit-learn: `1.9.1`;
  tqdm: `4.70.1`; ONNX: `1.23.1`; ONNX Runtime: `1.30.0`.
- Lote selecionado: 32; imagem de entrada: 224 × 224.

`rocm-smi` estava instalado e acessível no host. Um ensaio isolado de carga,
repetindo uma imagem real na GPU por 30,98 s sem alterar os artefatos,
coletou 31 amostras: utilização reportada de 100%, potência média de 132,1 W
e uso de VRAM em torno de 12%. Essa leitura é histórica do ensaio; uma leitura
posterior com a GPU ociosa não foi usada como resultado de treinamento.

As versões exatas de PyTorch e TorchVision são específicas do build ROCm e
devem ser instaladas conforme a versão de driver/ROCm do host, não pelo índice
PyPI padrão. Os demais pacotes usados pelo notebook aparecem em
`requirements-treino.txt`.
