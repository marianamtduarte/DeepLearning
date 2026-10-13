## PL04 — Avaliação, threshold e interpretação do modelo

### Objetivo

Avaliar o desempenho do modelo final na classificação de dez tipos de fraturas, identificar os principais erros e analisar as regiões das imagens que influenciam as previsões. Foi também estudado o efeito do threshold no equilíbrio entre falsos positivos e falsos negativos.

### Modelo e avaliação no teste

Foi avaliada a **EfficientNetB0 como extrator de características**, selecionada pela menor `val_loss` no conjunto de validação. O conjunto de teste não foi utilizado no treino nem na seleção do modelo.

A avaliação incluiu 157 imagens, utilizando a classe com maior probabilidade (`argmax`) para construir a matriz de confusão e o relatório de classificação.

| Métrica | Resultado |
|---|---:|
| Accuracy | 42,68% |
| F1 macro | 0,369 |
| AUC macro — One-vs-Rest | 0,785 |

O desempenho variou entre classes. **Greenstick** apresentou o melhor F1, enquanto **Longitudinal** não teve nenhum acerto e **Oblique** teve apenas um acerto em 12 casos. Estes resultados mostram a importância de analisar métricas por classe, além da accuracy global.

### Escolha do threshold

Foi analisada a classe **Pathological fracture versus as restantes classes**, testando thresholds entre 0,1 e 0,6 no conjunto de validação.

Escolheu-se **0,2**, por maximizar o índice de Youden (`TPR − FPR`) entre os valores avaliados:

| Indicador na validação | Resultado |
|---|---:|
| Recall / TPR | 68,4% |
| FPR | 15,2% |
| Precision | 38,2% |
| Índice de Youden | 0,532 |

Este threshold aplica-se à decisão binária sobre Pathological e não substitui automaticamente o `argmax` usado na classificação das dez classes.

### Interpretação com Grad-CAM

Foram gerados mapas Grad-CAM para **dois acertos e dois erros com elevada confiança**. Os mapas permitiram observar as regiões que contribuíram para as previsões e discutir se o foco era plausível ou se poderia estar associado a elementos irrelevantes, como texto e marcas nas imagens.

### Utilização e verificação da IA

Foi utilizado o **Gemini** para interpretar a matriz de confusão, o relatório de classificação e sugerir thresholds. As respostas foram verificadas contra os outputs do notebook e registadas na tabela 2.5.

Identificaram-se afirmações corretas, erros e recomendações sem comprovação experimental. Por exemplo, a IA afirmou que Longitudinal nunca tinha sido prevista, mas a matriz apresenta uma previsão incorreta dessa classe.

### Exercício ROC e custo dos erros

No exercício manual do protocolo, foi obtida uma **AUC de 0,84**. O threshold **0,6** maximizou o índice de Youden, enquanto **0,3** foi escolhido para o cenário de rastreio, por evitar falsos negativos nos casos apresentados, aceitando mais falsos positivos.

### Limitações

Os resultados evidenciam dificuldades na distinção entre várias classes. O threshold escolhido ainda necessita de resultados de teste registados, e as melhorias sugeridas pela IA, como pesos por classe ou rejeição de previsões incertas, não foram avaliadas nesta experiência.