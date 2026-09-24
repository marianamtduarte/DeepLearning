## Classificação de fraturas em radiografias

Projeto académico de classificação de imagens em 10 classes de fraturas ósseas.

## Dataset

[Bone Break Classification — Roboflow](https://universe.roboflow.com/curso-rphcb/bone-break-classification)

O ZIP continha 1 129 imagens. Após a análise de imagens repetidas e rótulos conflitantes, foram selecionadas 1 045 imagens. A divisão estratificada, guardada em `divisao_p1.csv`, contém 731 imagens de treino, 157 de validação e 157 de teste.

Não foi identificado um campo de doente para fazer a divisão por doente.

## Método

As imagens foram redimensionadas para 224 × 224 pixels. Foram comparados dois modelos em Keras 3: uma CNN treinada de raiz e uma MobileNetV2 pré-treinada, com a base congelada e uma nova camada de classificação. Os pesos foram escolhidos pela menor perda de validação.

## Resultados no teste

| Modelo | Accuracy |
| --- | ---: |
| CNN de raiz | 12,74% |
| MobileNetV2 | 33,76% |

A MobileNetV2 obteve F1 macro de 0,293. O desempenho variou entre classes; nenhuma das 11 imagens de teste de *Longitudinal fracture* foi corretamente identificada.

## Limitações

Foram encontrados duplicados e rótulos conflitantes no dataset original. A limpeza realizada não garante a correção de todos os rótulos nem substitui uma divisão por doente. Os resultados não permitem utilizar o modelo para diagnóstico clínico.

## Reprodutibilidade

- `divisao_p1.csv`: divisão fixa dos dados.
- `AI_LOG.md`: propostas da IA, verificações e correções.
- Notebook: **adicionar aqui o ficheiro `.ipynb` quando for enviado ao repositório**.

As imagens do dataset não são incluídas neste repositório; devem ser obtidas na fonte indicada acima.
