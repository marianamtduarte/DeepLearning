# 2.5. Com IA — a IA propõe, o aluno verifica

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? (S/N) | O que estava errado ou em falta | Como verificou (output, shape, métrica) |
| --- | --- | --- | --- |
| Carregar as imagens e mostrar nove exemplos com os respetivos rótulos. | S | A visualização confirma os rótulos das pastas, mas não garante a sua correção clínica. | Foram mostradas nove imagens rotuladas; o lote tinha shape `(32, 224, 224, 3)` e havia 10 classes. |
| Usar diretamente a divisão `Train` e `Test` existente no ZIP. | N | Foram encontradas imagens muito semelhantes nos dois conjuntos e imagens semelhantes com rótulos conflitantes. | Comparação automática de pares, seguida de inspeção visual; os resultados estão documentados no `AI_LOG.md`. |
| Criar uma nova divisão estratificada 70/15/15 e guardá-la em `divisao_p1.csv`. | S | Foi preciso manter o conjunto de teste fixo e usar a nova divisão em vez das pastas originais. Não foi possível dividir por doente, por falta de identificador. | CSV com 731 imagens de treino, 157 de validação e 157 de teste; zero ficheiros em falta e zero divergências entre rótulo e pasta. |
| Treinar uma CNN de raiz e uma MobileNetV2 pré-treinada e avaliar os resultados por classe. | S | A accuracy global não mostra o baixo desempenho em algumas classes. | Accuracy no teste: 12,74% para a CNN e 33,76% para a MobileNetV2. O relatório por classe mostrou recall zero para `Longitudinal fracture`. |
