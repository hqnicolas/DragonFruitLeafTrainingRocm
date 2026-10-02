# Dados e proveniência

O notebook usa duas fontes locais copiadas para este repositório:

- `Folha/`: as dez pastas correspondentes às classes do conjunto em português;
  imagens com prefixo `aug_` foram excluídas, pois são aumentos pré-gerados.
- `Dragon Fruit (Pitahaya)/Original Images/`: as seis pastas originais em
  inglês. `Converted Images/` e as anotações de caixas não são usadas.

Foram copiados 941 arquivos de imagem de `Folha/` e 724 de `Original Images/`.
O notebook encontra os dois caminhos na raiz e faz a seleção por extensão e
rótulo. Nomes-base com rotação e conteúdo SHA-256 idêntico são unidos no mesmo
grupo. Grupos com mais de um rótulo são excluídos antes da divisão.

As fontes externas incluídas no planejamento foram o repositório
[Dragon Fruit Application And Code](https://github.com/nguyen-phan-duc-minh/Dragon_Fruit_Application_And_Code)
e o conjunto [Dragon Fruit Diseases](https://data.mendeley.com/datasets/v3brsrm2f7/1).
Esta cópia contém somente as imagens efetivamente consideradas pelas duas
raízes acima, além do notebook e dos artefatos gerados. As pastas de dados foram
preservadas para possibilitar a inspeção local e a repetição do notebook.

A consolidação `Brown Stem Spot` → `Mancha-marrom` e `Gray Blight` → `Queima dos
ramos` é uma decisão de taxonomia deste modelo; não afirma equivalência
etiológica.
