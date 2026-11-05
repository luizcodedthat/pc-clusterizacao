# Clusterização de imagens de glaucoma (HYGD)

Agrupamento **não supervisionado** de 737 imagens de fundo de olho do Hillel Yaffe Glaucoma Dataset (HYGD), comparando três formas de representar cada imagem:

1. **Tradicional:** 37 estatísticas calculadas nos pixels (cor, histogramas, nitidez, iluminação).
2. **Deep Learning:** embeddings de 1024 dimensões da última camada escondida da MobileNetV3-Small, pré-treinada no ImageNet e congelada.
3. **Híbrida:** as duas representações juntas, com o mesmo peso.

Os rótulos (`GON+`/`GON-`), o `Quality Score`, o ID do paciente, o formato e a largura das imagens **não entram** no K-Means. Eles são usados só depois, para interpretar os clusters.

## Principais resultados

- As imagens formam um contínuo, não grupos bem separados (silhouette baixa, sem cotovelo).
- A abordagem **Tradicional** agrupa por brilho e nitidez, sem relação com o diagnóstico (NMI ≈ 0,01).
- A abordagem **Deep Learning** forma um grupo com quase todos os `GON-` e dois grupos de `GON+` separados por qualidade (NMI 0,37). O resultado é robusto a recortes dos dados e à troca de rede.
- O diagnóstico está misturado com a origem das imagens (o ID do paciente separa as classes, os 11 PNG são todos `GON-`, a largura difere entre classes). Por isso não é possível afirmar que os clusters captam a doença: eles podem refletir a origem e a qualidade das fotos.

## Estrutura

| Caminho | Conteúdo |
|---|---|
| `analise-exploratoria.ipynb` | Auditoria do dataset: rótulos, pacientes, qualidade, formatos, cópias idênticas, brilho e nitidez. Gera `outputs/auditoria_duplicatas.csv`. |
| `clusterizacao.ipynb` | Notebook completo: embeddings, características, PCA, K-Means, estabilidade, interpretação, t-SNE, comparação, sensibilidade e roteador por centroides. |
| `clusterizacao-apresentacao.ipynb` | Versão resumida para apresentação (~15 minutos). |
| `data/` | Dataset HYGD (imagens, `Labels.csv`, licença e checksums). |
| `outputs/` | Arquivos gerados pelos notebooks (auditoria, embeddings, características). |

## Como executar

Requer [uv](https://docs.astral.sh/uv/) e Python 3.14.

```bash
uv sync
```

Execute os notebooks a partir da raiz do projeto, nesta ordem:

1. `analise-exploratoria.ipynb` (gera a auditoria de cópias usada pelos outros dois);
2. `clusterizacao.ipynb`;
3. `clusterizacao-apresentacao.ipynb` (opcional; reaproveita os arquivos de `outputs/` gerados pelo notebook completo).

Todo o processamento roda na CPU com semente 42, para resultados reproduzíveis.

## Dataset e licença

O HYGD é distribuído pelo PhysioNet sob a licença **Open Data Commons Attribution (ODC-By)**; veja `data/LICENSE.txt`. Citação:

> Abramovich O, Pizem H, Fhima J, Berkowitz E, Gofrit B, Meisel M, et al. (2025). Hillel Yaffe Glaucoma Dataset (HYGD) (version 1.0.0). PhysioNet.

O `Quality Score` vem do FundusQ-Net:

> Abramovich O, Pizem H, Eijgen JV, Oren I, Melamed J, Stalmans I, et al. FundusQ-Net: A regression quality assessment deep learning algorithm for fundus images quality grading. Comput Methods Programs Biomed. 2023;239:107522.
