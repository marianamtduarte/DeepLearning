# Registo de utilização de IA — P1

Projeto: classificação de imagens de fraturas ósseas (*Bone Break Classification*). Este registo documenta propostas da IA, execução pelo grupo e verificação observada. Datas: 23–24/09/2026.

## 1. Dataset e divisão

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? | O que estava errado ou em falta | Como verificámos |
| --- | --- | --- | --- |
| Ler o ZIP e contar imagens e classes antes de construir os conjuntos. | Sim. | O ZIP trazia pastas `Train` e `Test`, mas essa divisão original não garantia ausência de imagens repetidas entre conjuntos. | Contagem no Colab: 1 129 imagens, 10 classes; os caminhos continham o padrão `classe/Train` ou `classe/Test`. |
| Verificar duplicados exatos e pares visualmente semelhantes, incluindo entre as pastas originais. | Sim, como auditoria exploratória. | Hash de ficheiro sozinho não identificou imagens visualmente quase iguais. Uma distância pequena entre imagens também não prova, por si só, identidade clínica. | Foram encontrados 3 grupos de cópias exatas. A inspeção visual de pares semelhantes revelou radiografias muito próximas entre `Train` e `Test` e exemplos com rótulos diferentes. |
| Agrupar duplicados semelhantes, excluir grupos com rótulos conflitantes e conservar uma imagem dos restantes grupos repetidos. | Executada com ressalvas. | Não foi possível verificar IDs de doente/sessão nem validar clinicamente todos os rótulos. O procedimento deteta apenas semelhanças segundo os critérios adotados. | O script reportou 6 grupos com conflito (14 imagens) e 68 grupos repetidos com a mesma classe (70 cópias excedentes). Restaram 1 045 imagens utilizáveis em 10 classes. |
| Dividir os dados em treino, validação e teste estratificados, guardar os caminhos e conjuntos em `divisao_p1.csv`. | Sim. | A divisão foi feita a partir das imagens selecionadas, independentemente das pastas `Train`/`Test` originais. Não houve estratificação por doente, pois não foi identificado esse campo. | Contagens: treino 731, validação 157, teste 157; soma 1 045. Após reiniciar o Colab, o CSV foi relido: 1 045 registos, zero imagens em falta e zero divergências entre rótulo e pasta. |
| Carregar lotes e mostrar nove imagens rotuladas. | Sim. | A visualização confirma a correspondência com os rótulos das pastas, não a correção clínica do diagnóstico. | Nove radiografias mostradas com rótulo; formato de lote `(32, 224, 224, 3)` e 10 classes. |

## 2. Ambiente e modelos

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? | O que estava errado ou em falta | Como verificámos |
| --- | --- | --- | --- |
| Confirmar Keras 3, backend TensorFlow e GPU. | Sim, após ativar GPU no Colab. | Numa execução anterior, `nvidia-smi` não estava disponível; a presença de Keras não implica GPU ativa. | Colab apresentou Keras 3.13.2, backend TensorFlow e GPU Tesla T4 no `nvidia-smi`. |
| Treinar uma CNN pequena de raiz como referência, com `EarlyStopping` monitorizado por `val_loss`. | Sim; desempenho baixo. | A maior `val_accuracy` observada não corresponde necessariamente aos pesos restaurados. | `summary()`: 102 154 parâmetros treináveis e 10 saídas. Em 15 épocas, menor `val_loss` 2,1330 na época 10; maior `val_accuracy` observada 21,0% nas épocas 12 e 14. O teste deu 12,74% de accuracy e loss 2,2965. |
| Criar MobileNetV2 pré-treinada com base congelada, converter os pixels de `[0, 1]` para a escala esperada pelo pré-processamento do modelo e treinar a camada de 10 saídas. | Sim; melhor que a CNN na validação e no teste. | A diferença entre accuracy de treino e de validação sugere sobreajuste. Os pesos finais foram escolhidos pela menor `val_loss`, não pela maior accuracy. | `summary()`: 2 270 794 parâmetros, dos quais 12 810 treináveis. Em 11 épocas, menor `val_loss` 2,0298 na época 8; `val_accuracy` nessa época 34,4%; maior `val_accuracy` observada 35,0%. No teste: loss 1,8992 e accuracy 33,76%. |
| Guardar os modelos treinados. | Sim. | Ficheiros em `/content` podem perder-se quando a sessão termina; é preciso descarregar os modelos, o CSV e guardar o notebook. | O Colab confirmou `Modelos guardados.` após criar `cnn_base.keras` e `mobilenetv2_transfer.keras`. Código fornecido para descarregar ambos os modelos e o CSV num ZIP. Confirmar localmente que o ZIP e o notebook foram efetivamente guardados. |

## 3. Avaliação no teste

A seleção dos pesos usou apenas treino e validação; o teste fixo foi usado depois para reportar os resultados finais dos dois modelos. A MobileNetV2 alcançou **33,76% de accuracy** em 157 imagens, contra **12,74%** da CNN de raiz. O relatório da MobileNetV2 apresentou **F1 macro 0,293** e **F1 ponderado 0,307**. Na classe *Longitudinal fracture*, o recall foi 0/11; em *Avulsion fracture*, foi 14/18 (0,778); em *Greenstick fracture*, 12/17 (0,706). A matriz de confusão e o relatório por classe foram gerados no notebook.

### Limitações a declarar

- Os rótulos originais apresentaram conflitos para imagens semelhantes; a limpeza reduziu o problema identificado, mas não demonstra que todos os restantes rótulos sejam corretos.
- Não havia identificador de doente disponível para assegurar uma divisão por doente. Uma verificação de imagens semelhantes não substitui essa garantia.
- O teste contém apenas 11–22 imagens por classe; as métricas por classe têm grande incerteza.
- Os resultados descrevem este dataset e este protocolo. O modelo não deve ser usado para diagnóstico clínico.

## 4. Verificação e continuidade

- Confirmar que o ZIP de backup foi descarregado e que o notebook está guardado no Drive.
- Conservar `divisao_p1.csv` sem alterar os conjuntos; se houver experiências adicionais, escolher configurações com a validação e descrever qualquer nova consulta ao teste.
- Inserir no relatório as versões de ambiente, contagens, critérios de limpeza, parâmetros do treino, métricas de teste e limitações acima.
- Se forem feitas novas experiências, acrescentar aqui uma linha por proposta da IA, falha observada, correção e evidência de verificação. Não atribuir à IA execução ou resultados que não tenham sido observados.
