# Registo de utilização de IA — P1

Projeto: classificação de imagens de fraturas ósseas (*Bone Break Classification*).

Este registo documenta propostas da IA, execução pelo grupo e verificação dos resultados observados.

## 1. Dataset e divisão

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? | O que estava errado ou em falta | Como verificámos |
| --- | --- | --- | --- |
| Ler o ZIP e contar imagens e classes antes de construir os conjuntos. | Sim. | O ZIP trazia pastas `Train` e `Test`, mas essa divisão original não garantia ausência de imagens repetidas entre conjuntos. | Contagem no Colab: 1 129 imagens, 10 classes; os caminhos continham o padrão `classe/Train` ou `classe/Test`. |
| Verificar duplicados exatos e pares visualmente semelhantes, incluindo entre as pastas originais. | Sim, como auditoria exploratória. | Hash de ficheiro sozinho não identificou imagens visualmente quase iguais. Uma distância pequena entre imagens também não prova, por si só, que sejam a mesma imagem ou pertençam ao mesmo doente. | Foram encontrados 3 grupos de cópias exatas. A inspeção visual revelou radiografias muito próximas entre `Train` e `Test` e exemplos semelhantes com rótulos diferentes. |
| Agrupar imagens semelhantes, excluir grupos com rótulos conflitantes e conservar uma imagem dos restantes grupos repetidos. | Executada com ressalvas. | Não foi possível verificar IDs de doente ou sessão nem validar clinicamente todos os rótulos. O procedimento deteta apenas semelhanças segundo os critérios adotados. | O script reportou 6 grupos com conflito, contendo 14 imagens, e 68 grupos repetidos com a mesma classe, com 70 cópias excedentes. Restaram 1 045 imagens utilizáveis em 10 classes. |
| Dividir os dados em treino, validação e teste estratificados, nas proporções aproximadas de 70/15/15, e guardar a divisão em `divisao_p1.csv`. | Sim. | A divisão foi feita a partir das imagens selecionadas, independentemente das pastas originais. Não houve divisão por doente, pois não foi identificado esse campo. | Contagens: treino 731, validação 157, teste 157; soma 1 045. Após reiniciar o Colab, o CSV foi relido: zero imagens em falta e zero divergências entre rótulo e pasta. |
| Carregar lotes e mostrar nove imagens rotuladas. | Sim. | A visualização confirma a correspondência com os rótulos das pastas, não a correção clínica do diagnóstico. | Nove imagens mostradas com rótulos; formato de lote `(32, 224, 224, 3)` e 10 classes no Protocolo 1. |

## 2. Ambiente e modelos do Protocolo 1

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? | O que estava errado ou em falta | Como verificámos |
| --- | --- | --- | --- |
| Confirmar Keras 3, backend TensorFlow e GPU. | Sim, após ativar GPU no Colab. | Numa execução anterior, `nvidia-smi` não estava disponível; a presença de Keras não implica GPU ativa. | Colab apresentou Keras 3.13.2, backend TensorFlow e GPU Tesla T4 no `nvidia-smi`. |
| Treinar uma CNN pequena de raiz como referência, com `EarlyStopping` monitorizado por `val_loss`. | Sim; desempenho baixo. | A maior `val_accuracy` observada não corresponde necessariamente aos pesos restaurados. | `summary()`: 102 154 parâmetros treináveis e 10 saídas. Em 15 épocas, menor `val_loss` de 2,1330 na época 10; maior `val_accuracy` observada de 21,0%. No teste: accuracy de 12,74% e loss de 2,2965. |
| Criar MobileNetV2 pré-treinada com base congelada, adaptar a escala dos pixels ao pré-processamento esperado e treinar a camada de 10 saídas. | Sim; melhor desempenho que a CNN nesta experiência. | A diferença entre accuracy de treino e de validação sugere sobreajuste. Os pesos foram escolhidos pela menor `val_loss`, não pela maior accuracy. | `summary()`: 2 270 794 parâmetros, dos quais 12 810 treináveis. Em 11 épocas, menor `val_loss` de 2,0298 na época 8; `val_accuracy` nessa época de 34,4%; maior `val_accuracy` observada de 35,0%. No teste: loss de 1,8992 e accuracy de 33,76%. |
| Guardar os modelos treinados. | Sim. | Ficheiros em `/content` podem perder-se quando a sessão termina; é necessário guardar cópias fora da sessão. | O Colab confirmou `Modelos guardados.` após criar `cnn_base.keras` e `mobilenetv2_transfer.keras`. Foi fornecido código para descarregar os modelos e o CSV num ZIP. A confirmação do backup deve ser feita localmente. |

## 3. Avaliação no teste e limitação do protocolo

A seleção dos pesos usou treino e validação. No Protocolo 1, o conjunto de teste foi consultado para avaliar os dois modelos. Essa avaliação ocorreu antes da etapa prevista no Protocolo 2; por isso, o teste já não pode ser considerado intocado.

Mantivemos a divisão original e não voltámos a consultar o teste nas experiências do Protocolo 2.

### Resultados observados no Protocolo 1

A MobileNetV2 alcançou **33,76% de accuracy** em 157 imagens de teste, contra **12,74%** da CNN de raiz.

O relatório da MobileNetV2 apresentou:

- F1 macro: 0,293.
- F1 ponderado: 0,307.
- Recall de *Longitudinal fracture*: 0/11.
- Recall de *Avulsion fracture*: 14/18, aproximadamente 0,778.
- Recall de *Greenstick fracture*: 12/17, aproximadamente 0,706.

A matriz de confusão e o relatório por classe foram gerados no notebook.

### Limitações

- Os rótulos originais apresentaram conflitos para imagens semelhantes. A limpeza reduziu o problema identificado, mas não demonstra que todos os restantes rótulos sejam corretos.
- Não foi identificado um campo de doente que permitisse assegurar uma divisão por doente.
- A verificação de imagens semelhantes não substitui a separação por doente.
- O teste contém apenas 11–22 imagens por classe, pelo que as métricas por classe têm grande incerteza.
- O teste já foi consultado no Protocolo 1; qualquer avaliação posterior deve declarar essa consulta anterior.
- Os resultados descrevem este dataset e estas experiências. Não demonstram adequação para diagnóstico clínico.

## 4. Protocolo 2 — Verificação dos dados

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? | O que estava errado ou em falta | Como verificámos |
| --- | --- | --- | --- |
| Confirmar ausência de caminhos repetidos e verificar interseções de hashes MD5 entre conjuntos. | Sim, para duplicados exatos. | MD5 não identifica todas as imagens quase duplicadas nem permite verificar identidade de doente. | Interseções de hashes iguais a zero entre treino/validação, treino/teste e validação/teste; ausência de caminhos repetidos. |
| Caracterizar o dataset por classe, quantidade, percentagem e dimensões medianas. | Sim. | As dimensões variam entre imagens; a mediana não representa todas as resoluções existentes. | Foram calculadas as contagens, percentagens e medianas de largura e altura por classe, guardadas em `data/caracterizacao_classes.csv`. |
| Guardar a divisão existente em `data/splits.csv`. | Sim. | Não foi criada uma nova divisão. Foi preservada a atribuição anterior de cada imagem ao respetivo conjunto. | Mantiveram-se 731 imagens de treino, 157 de validação e 157 de teste. A maior diferença observada entre a percentagem de uma classe num conjunto e no total foi de aproximadamente 0,64 pontos percentuais. |
| Preparar imagens de 180 × 180 pixels, com três canais e batch de 32. | Sim. | O redimensionamento uniformiza o tamanho, mas não recupera detalhes ausentes nas imagens originais. | Formato do lote `(32, 180, 180, 3)`, 10 classes e pixels na escala de 0 a 255 antes da camada de normalização. |

A ausência de interseções de MD5 confirma que não foram encontrados ficheiros exatamente iguais entre conjuntos. Não garante ausência de imagens quase duplicadas nem separação por doente.

## 5. Protocolo 2 — CNN baseline

### Proposta e execução

Construímos uma CNN treinada de raiz com:

- Entrada de 180 × 180 pixels e três canais.
- Normalização dos pixels com `Rescaling(1/255)`.
- Quatro blocos de convolução e max pooling, com 32, 64, 128 e 256 filtros.
- `GlobalAveragePooling2D`.
- Camada final com 10 saídas e ativação softmax.

Configuração do treino:

- Seed: 42.
- Batch: 32.
- Épocas: 30.
- Otimizador: Adam.
- Taxa de aprendizagem: 0,001.
- Função de perda: `sparse_categorical_crossentropy`.
- Métrica: accuracy.
- Seleção do modelo: menor `val_loss`.

### Como verificámos

O resumo do modelo apresentou **390 986 parâmetros treináveis** e 10 saídas.

Guardámos o histórico das 30 épocas e o modelo correspondente à menor loss de validação.

| Indicador                                                                              |  Resultado |
| -------------------------------------------------------------------------------------- | ---------: |
| Época com menor `val_loss`                                                             |     **19** |
| Menor `val_loss`                                                                       | **2,1802** |
| Accuracy de treino nessa época                                                         | **22,85%** |
| Accuracy de validação nessa época                                                      | **22,29%** |
| Precisão de validação nessa época                                                      | **66,67%** |
| Recall de validação nessa época                                                        |  **1,27%** |
| F1-Score de validação nessa época                                                      | **15,52%** |
| Maior accuracy de validação observada                                                  | **26,75%** |
| Accuracy na validação do classificador que prevê sempre a classe maioritária do treino | **14,01%** |


A classe maioritária do treino foi *Fracture Dislocation*. Prever sempre essa classe acertaria 22 das 157 imagens de validação.

### Diagnóstico

A CNN apresentou aprendizagem limitada: tanto a accuracy de treino como a de validação permaneceram baixas.

A loss de treino diminuiu, enquanto a loss de validação oscilou e aumentou em parte das épocas finais, sugerindo sinais de sobreajuste nessa fase.

Na época selecionada pela menor val_loss, a accuracy de validação superou o classificador maioritário em aproximadamente 7,01 pontos percentuais.

## 6. Protocolo 2 — Comparação da CNN com e sem Dropout

### Propostas da IA

A IA sugeriu três alterações:

1. Adicionar Dropout antes da camada de classificação.
2. Aplicar pequenas rotações às imagens de treino.
3. Reduzir o número de filtros da CNN.

Foi aplicada apenas a primeira alteração: **Dropout de 0,3 antes da camada final**.

A hipótese era que esta regularização poderia reduzir o sobreajuste. Não havia garantia de melhoria, sobretudo porque a CNN baseline já apresentava baixa accuracy de treino.

### Como verificámos

Treinámos os dois modelos durante 30 épocas, mantendo:

- A mesma divisão dos dados.
- Seed 42.
- Imagens de 180 × 180 pixels.
- Batch de 32.
- O mesmo otimizador e taxa de aprendizagem.
- A mesma arquitetura, exceto pela inclusão do Dropout.

A comparação utilizou a época com menor loss de validação de cada modelo.

| Modelo      | Época de menor `val_loss` | Menor `val_loss` | Accuracy treino nessa época | Accuracy validação nessa época |
| ----------- | ------------------------: | ---------------: | --------------------------: | -----------------------------: |
| Baseline    |                    **19** |       **2,1802** |                  **22,85%** |                     **22,29%** |
| Dropout 0,3 |                    **20** |       **2,1709** |                  **21,07%** |                     **22,93%** |


### Funcionou?

O código executou corretamente.

O Dropout reduziu ligeiramente a menor loss de validação, de 2,1802 para 2,1709 e aumentou a accuracy de validação na época selecionada em aproximadamente 0,64 pontos percentuais.

Além disso, na época selecionada, a diferença entre a accuracy de treino e validação passou de 0,56 p.p. no Baseline para 1,86 p.p. no Dropout. As curvas continuam a apresentar oscilações na validação, pelo que a aprendizagem permanece limitada.

A maior accuracy de validação observada foi 26,75% no Baseline e 24,20% no Dropout 0,3.

Esta experiência, realizada com uma única seed, não permite concluir que o Dropout produza uma melhoria consistente.

A accuracy de treino do modelo com Dropout é calculada durante o treino, com Dropout ativo; esse detalhe deve ser considerado ao comparar as métricas de treino entre os modelos.

### Evidências

- `historico_baseline.csv`: histórico do treino da CNN baseline.
- `historico_dropout.csv`: histórico do treino da CNN com Dropout.
- `curvas_baseline.png`: curvas da CNN baseline.
- `comparacao_curvas.png`: comparação das curvas dos dois modelos.
- `comparacao_modelos.csv`: tabela dos resultados.
- `cnn_baseline_melhor.keras`: modelo baseline guardado na época com menor val_loss.
- `cnn_dropout_melhor.keras`: modelo com Dropout guardado na época com menor val_loss.

Nesta experiência do Protocolo 2, não voltámos a avaliar o conjunto de teste.

## 7. Verificação e continuidade

- Guardar o notebook e os ficheiros de resultados fora da sessão temporária do Colab.
- Conservar a divisão dos dados sem alterar os conjuntos.
- Escolher configurações com base no treino e na validação.
- Declarar a consulta anterior ao teste em qualquer relatório posterior.
- Incluir no relatório contagens, critérios de limpeza, versões do ambiente, arquitetura, parâmetros de treino, resultados e limitações.
- Acrescentar ao registo novas propostas, falhas, correções e evidências quando houver novas experiências.
- Não atribuir à IA execução ou resultados que não tenham sido observados.
