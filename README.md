# Classificação de fraturas em radiografias

Projeto académico de Deep Learning Aplicado — P1.

Nesta etapa, construímos uma CNN treinada de raiz como baseline e avaliámos o efeito de adicionar Dropout, mantendo o mesmo protocolo de treino e validação.

## Dataset

[Bone Break Classification — Kaggle](https://www.kaggle.com/datasets/pkdarabi/bone-break-classification-image-dataset)

O ZIP continha 1 129 imagens distribuídas por 10 classes.

Após a auditoria, foram excluídas 14 imagens de grupos com rótulos conflitantes e removidas 70 cópias excedentes. Restaram 1 045 imagens.

| Classe | Imagens após limpeza |
| --- | ---: |
| Avulsion fracture | 120 |
| Comminuted fracture | 130 |
| Fracture Dislocation | 147 |
| Greenstick fracture | 117 |
| Hairline Fracture | 109 |
| Impacted fracture | 74 |
| Longitudinal fracture | 71 |
| Oblique fracture | 80 |
| Pathological fracture | 123 |
| Spiral Fracture | 74 |
| **Total** | **1 045** |

Não foi identificado um campo de doente, local ou sessão que permitisse dividir os dados por grupo.

## Divisão dos dados

Foi realizada uma divisão estratificada por classe, com seed 42 e proporções aproximadas de 70/15/15.

| Conjunto | Imagens |
| --- | ---: |
| Treino | 731 |
| Validação | 157 |
| Teste | 157 |

A divisão foi guardada inicialmente em `divisao_p1.csv` e preservada em `data/splits.csv` para o Protocolo 2.

As verificações apresentaram zero interseções de hashes MD5 entre conjuntos e ausência de caminhos repetidos. Isto confirma ausência de duplicados exatos detetados, mas não garante ausência de imagens quase duplicadas ou do mesmo doente.

## Pré-processamento

- Redimensionamento das imagens para 180 × 180 pixels.
- Carregamento com três canais.
- Normalização dos pixels pela camada `Rescaling(1/255)`.
- Lotes de 32 imagens.
- Baralhamento apenas do conjunto de treino.

## Modelo e treino

A CNN baseline tem quatro blocos de convolução e max pooling, com 32, 64, 128 e 256 filtros, seguidos de `GlobalAveragePooling2D` e uma camada de classificação com 10 saídas e ativação softmax.

O modelo tem 390 986 parâmetros treináveis. Foi escolhido como referência para estudar a aprendizagem de uma CNN de raiz num dataset pequeno.

Configuração:

- Keras 3.13.2 com backend TensorFlow.
- Seed 42.
- Otimizador Adam com taxa de aprendizagem de 0,001.
- Função de perda `sparse_categorical_crossentropy`.
- Métrica accuracy.
- Treino durante 30 épocas.
- Modelo guardado na época com menor loss de validação.

## Experiência com Dropout

Foi treinada uma segunda CNN, acrescentando apenas Dropout de 0,3 antes da camada final.

Mantivemos a divisão dos dados, seed, dimensões das imagens, batch, otimizador, taxa de aprendizagem e número de épocas.

## Resultados de validação

Os valores abaixo correspondem à época com menor loss de validação de cada modelo.

| Modelo | Época selecionada | Loss de validação | Accuracy de treino | Accuracy de validação |
| --- | ---: | ---: | ---: | ---: |
| CNN baseline | 17 | 2,1856 | 21,20% | 21,02% |
| CNN com Dropout de 0,3 | 25 | 2,1731 | 23,39% | 21,02% |

O classificador que prevê sempre a classe maioritária do treino obteve 14,01% de accuracy na validação.

O Dropout reduziu ligeiramente a loss de validação, mas não melhorou a accuracy na época selecionada. Uma única execução por modelo não permite concluir que essa redução seja consistente.

As curvas indicam aprendizagem limitada, com sinais de sobreajuste nas épocas finais da baseline.

## Limitações

- A limpeza não garante que todos os rótulos restantes estejam corretos.
- Não foi possível assegurar separação por doente.
- A ausência de duplicados exatos não exclui imagens quase duplicadas.
- Os resultados foram obtidos com uma única seed.
- O conjunto de teste foi consultado numa experiência anterior, antes da etapa prevista pelo protocolo. Essa limitação está registada no `AI_LOG.md`. Nas experiências do Protocolo 2, utilizámos apenas treino e validação.
- Os resultados não demonstram adequação para diagnóstico clínico.

## Ficheiros do projeto

- `BoneDataset.ipynb`: notebook do projeto.
- `divisao_p1.csv`: divisão original guardada.
- `data/splits.csv`: divisão preservada para o Protocolo 2.
- `data/caracterizacao_classes.csv`: contagens, percentagens e dimensões medianas por classe.
- `AI_LOG.md`: propostas da IA, verificações, correções e limitações.
- `resultados_pl02/`: históricos, gráficos, comparação dos modelos e checkpoints.

As imagens do dataset devem ser obtidas na fonte indicada acima. Para reproduzir a experiência, é necessário preservar os caminhos e a divisão guardada dos dados.
