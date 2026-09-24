# 2.4. Par e dataset do P1
| Campo | Resposta |
| --- | --- |
| Dataset e URL | [Bone Break Classification Image Dataset](https://www.kaggle.com/datasets/pkdarabi/bone-break-classification-image-dataset) |
| N.º de imagens / n.º de classes | 1.129 imagens originais / 10 classes. Após a limpeza, foram utilizadas 1.045 imagens. |
| Distribuição por classe (%) | No dataset original: Fracture Dislocation: 13,82%; Comminuted fracture: 13,11%; Pathological fracture: 11,87%; Avulsion fracture: 10,89%; Greenstick fracture: 10,81%; Hairline Fracture: 9,83%; Spiral Fracture: 7,62%; Oblique fracture: 7,53%; Impacted fracture: 7,44%; Longitudinal fracture: 7,09%. |
| Licença | CC BY 4.0 |
| Existe identificador de doente, local ou sessão? Qual? | Não foi identificado nenhum identificador de doente, local ou sessão nos ficheiros analisados. Por isso, não foi possível garantir uma divisão por doente. |
| Porque é um problema interessante? | Algumas classes de fratura têm características visuais semelhantes, tornando a classificação das radiografias difícil. O problema permite analisar o desempenho por classe e os erros entre tipos de fratura. |

# 2.5. Com IA — a IA propõe, o aluno verifica

| Proposta da IA (código ou afirmação) | Funcionou / estava correta? (S/N) | O que estava errado ou em falta | Como verificou (output, shape, métrica) |
| --- | --- | --- | --- |
| Carregar as imagens e mostrar nove exemplos com os respetivos rótulos. | Sim | A visualização confirma os rótulos das pastas, mas não garante a sua correção clínica. | Foram mostradas nove imagens rotuladas; o lote tinha shape `(32, 224, 224, 3)` e havia 10 classes. |
| Usar diretamente a divisão `Train` e `Test` existente no ZIP. | Não | Foram encontradas imagens muito semelhantes nos dois conjuntos e imagens semelhantes com rótulos conflitantes. | Comparação automática de pares, seguida de inspeção visual; os resultados estão documentados no `AI_LOG.md`. |
| Criar uma nova divisão estratificada 70/15/15 e guardá-la em `divisao_p1.csv`. | Sim | Foi preciso manter o conjunto de teste fixo e usar a nova divisão em vez das pastas originais. Não foi possível dividir por doente, por falta de identificador. | CSV com 731 imagens de treino, 157 de validação e 157 de teste; zero ficheiros em falta e zero divergências entre rótulo e pasta. |
| Treinar uma CNN de raiz e uma MobileNetV2 pré-treinada e avaliar os resultados por classe. | Sim | A accuracy global não mostra o baixo desempenho em algumas classes. | Accuracy no teste: 12,74% para a CNN e 33,76% para a MobileNetV2. O relatório por classe mostrou recall zero para `Longitudinal fracture`. |
