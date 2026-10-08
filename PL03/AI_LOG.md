# AI_LOG — PL03: classificação de fraturas ósseas

## Objetivo do registo

Este ficheiro documenta o apoio de inteligência artificial (IA) utilizado durante a organização e compreensão do trabalho PL03. A IA foi usada como ferramenta de apoio; a execução do código, os resultados experimentais e as decisões finais foram conferidos no notebook e nos materiais do trabalho.

## Utilizações da IA

| Etapa | Utilização da IA | Verificação realizada | Resultado no trabalho |
|---|---|---|---|
| Compreensão dos conceitos | Foram solicitadas explicações sobre pesos, CNN, baseline, treino, validação, teste, épocas, accuracy, precision, recall, F1 e matriz de confusão. | As explicações foram relacionadas às células e às métricas efetivamente usadas no notebook. | Serviram para apoiar a compreensão e a interpretação do procedimento; não produziram resultados experimentais. |
| Organização do notebook | Foi solicitada ajuda para estruturar o fluxo do PL03, incluindo a baseline da aula 2, a extração de características e o fine-tuning. | A organização foi comparada com o enunciado do PL03 e com o código do notebook. | O fluxo separa treino, validação e teste e deixa a avaliação no teste para o final. |
| Transfer learning | Foi solicitada explicação sobre EfficientNetB0, ResNet50, ConvNeXtTiny, base congelada e fine-tuning. | As arquiteturas e os pré-processamentos foram conferidos por `count_params()`, `input_shape` no Colab e documentação oficial do Keras. | EfficientNetB0 foi usada no treino. ResNet50 e ConvNeXtTiny foram comparadas em parâmetros, entrada e pré-processamento, sem serem treinadas. |
| Pré-processamento das arquiteturas | A IA ajudou a interpretar as diferenças de pré-processamento entre as três arquiteturas. | EfficientNetB0 e ConvNeXtTiny foram verificadas como modelos com pré-processamento incluído e entrada de pixels entre 0 e 255. Para ResNet50, foi verificado o uso de `keras.applications.resnet.preprocess_input`, com conversão RGB para BGR e centragem pelos canais do ImageNet. | A tabela comparativa do notebook regista os valores e as diferenças verificadas. |
| Métricas e seleção | Foi solicitada orientação para compreender accuracy, F1 macro e a escolha do modelo. | As métricas foram calculadas no notebook. A seleção foi baseada na validação, monitorizando `val_loss`; o conjunto de teste ficou reservado para a avaliação final. | A IA não escolheu o modelo com base nos resultados do teste. |
| Documentação | Foi solicitada ajuda para preparar o README e este registo de IA. | As descrições metodológicas foram confrontadas com o notebook e com os ficheiros de resultados disponíveis. | O README resume o conjunto de dados, o protocolo, as métricas e as limitações. Este ficheiro regista como a IA foi utilizada. |

## Verificações técnicas registadas

A comparação das arquiteturas foi feita com entrada `(224, 224, 3)` e `include_top=False`. A saída observada no Colab indicou:

- EfficientNetB0: 4.049.571 parâmetros.
- ResNet50: 23.587.712 parâmetros.
- ConvNeXtTiny: 27.820.128 parâmetros.

A contagem de parâmetros descreve o tamanho das arquiteturas, mas não demonstra qual delas terá melhor desempenho no dataset. Apenas EfficientNetB0 foi usada nas etapas de extração de características e fine-tuning do notebook.

## Limitações e responsabilidade

As explicações e sugestões da IA podem conter erros ou omissões. Por isso, afirmações técnicas foram verificadas no código executado ou na documentação oficial do Keras. Os valores de treino e avaliação reportados no trabalho devem corresponder às saídas geradas pelo notebook; não foram estimados pela IA. A avaliação no conjunto de teste foi mantida separada da seleção do modelo.
