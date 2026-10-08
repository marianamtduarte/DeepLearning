# PL03 — Classificação de fraturas ósseas

## Objetivo

Este trabalho compara uma CNN treinada do zero com duas etapas de *transfer learning* para classificar imagens em 10 tipos de fratura óssea: extração de características com EfficientNetB0 congelada e *fine-tuning* das camadas finais dessa rede.

## Dados e divisão

O dataset original continha 1.129 imagens. Após a inspeção de duplicados, imagens semelhantes e conflitos de rótulo, foram mantidas 1.045 imagens. A divisão estratificada foi preservada com *seed* 42:

- Treino: 731 imagens.
- Validação: 157 imagens.
- Teste: 157 imagens.

O notebook confere caminhos, rótulos, ficheiros em falta e hashes entre os conjuntos. As interseções verificadas entre treino, validação e teste são zero. Não havia identificadores de pacientes para verificar a separação por pessoa.

## Modelos e procedimento

A CNN baseline da aula 2 foi treinada do zero. Para a EfficientNetB0, foram realizadas duas etapas: primeiro, a base pré-treinada ficou congelada enquanto se treinava uma nova cabeça classificadora; depois, algumas camadas finais foram descongeladas e ajustadas com *learning rate* menor.

O aumento de dados foi aplicado durante o treino. Os modelos usaram *EarlyStopping*, *ModelCheckpoint* e TensorBoard. O notebook apresenta as curvas de treino e validação, os *checkpoints* e uma tabela comparativa com parâmetros treináveis, melhor época, métricas e tempo de treino.

## Métricas e seleção

A *accuracy* representa a proporção de previsões corretas. O F1 macro calcula o F1 de cada classe e dá o mesmo peso a todas, sendo útil neste conjunto, em que as classes têm quantidades diferentes de imagens. O relatório por classe e a matriz de confusão ajudam a identificar acertos e confusões entre tipos de fratura.

A escolha do modelo foi feita usando a validação, com monitorização da menor `val_loss`. O conjunto de teste foi reservado para a avaliação final após essa escolha. As métricas de validação e teste, as curvas e a matriz de confusão estão apresentadas nas saídas do notebook.

## Verificação das arquiteturas

Foram verificados `count_params()` e `input_shape` para EfficientNetB0, ResNet50 e ConvNeXtTiny, com entrada de 224 × 224 × 3. A EfficientNetB0 tem 4.049.571 parâmetros, a ResNet50 tem 23.587.712 e a ConvNeXtTiny tem 27.820.128. A contagem de parâmetros indica o tamanho relativo das redes, mas não determina qual terá melhor desempenho.

O pré-processamento também foi conferido na documentação do Keras. A EfficientNetB0 e a ConvNeXtTiny recebem pixels entre 0 e 255 com pré-processamento incluído no modelo. A ResNet50 requer `keras.applications.resnet.preprocess_input`, que converte RGB para BGR e centra os canais conforme as médias do ImageNet, sem redimensionar a escala.

## Limitações

O dataset é relativamente pequeno e as classes têm frequências diferentes. As imagens também variam em resolução, contraste e enquadramento. Além disso, não foi possível verificar a separação por paciente. Por isso, os resultados são apresentados como um exercício académico de classificação e não como validação para uso clínico.
