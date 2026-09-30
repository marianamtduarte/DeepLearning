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
| Carregar as imagens e mostrar nove exemplos com os respetivos rótulos. | Sim | A visualização confirma os rótulos das pastas, mas não garante a sua correção clínica. | Foram mostradas nove imagens rotuladas; o lote tinha shape `(32, 180, 180, 3)` e havia 10 classes. |
| Usar diretamente a divisão `Train` e `Test` existente no ZIP. | Não | Foram encontradas imagens muito semelhantes nos dois conjuntos e imagens semelhantes com rótulos conflitantes. | Comparação automática de pares, seguida de inspeção visual; os resultados estão documentados no `AI_LOG.md`. |
| Criar uma nova divisão estratificada 70/15/15 e guardá-la em `divisao_p1.csv`. | Sim | Foi preciso manter o conjunto de teste fixo e usar a nova divisão em vez das pastas originais. Não foi possível dividir por doente, por falta de identificador. | CSV com 731 imagens de treino, 157 de validação e 157 de teste; zero ficheiros em falta e zero divergências entre rótulo e pasta. |
| Treinar uma CNN de raiz e avaliar os resultados por classe. | Sim | A accuracy global não mostra o baixo desempenho em algumas classes. | Accuracy no teste: 12,74% para a CNN. O relatório por classe mostrou recall zero para `Longitudinal fracture`. |

# PL02
## 2.1. Caracterização do dataset 

| Classe                | N.º imagens |        % | Dimensão típica | Observações (qualidade, duplicados, marcas) |
| --------------------- | ----------: | -------: | --------------- | ------------------------------------------- |
| Avulsion fracture     |         123 |    10,9% |      (640, 640) | Presença de um texto muito tênue no canto superior esquerdo de uma imagem                                            |
| Fracture Dislocation  |         156 |    13,8% |      (640, 640) | —                                           |
| Comminuted fracture   |         148 |    13,1% |      (640, 640) | —                                           |
| Oblique fracture      |          85 |     7,5% |      (640, 640) | —                                           |
| Impacted fracture     |          84 |     7,4% |      (640, 640) | —                                           |
| Longitudinal fracture |          80 |     7,1% |      (640, 640) |Presença da marca radiológica “R”, indicando o lado direito. Foi observada uma anotação visível (elipse verde) a destacar uma área específica.                                                                   |
| Pathological fracture |         134 |    11,9% |      (640, 640) | —                                           |
| Greenstick fracture   |         122 |    10,8% |      (640, 640) | —                                           |
| Spiral Fracture       |          86 |     7,6% |      (640, 640) | —                                           |
| Hairline Fracture     |         111 |     9,8% |      (640, 640) | —                                           |
| **Total**             |    **1129** | **100%** | —               | —                                           |
