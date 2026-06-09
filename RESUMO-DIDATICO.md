# Resumo Didático — Artigo de Chicago (CNN + LSTM para prever furtos)

> Material de estudo para entender o artigo e se preparar para a apresentação oral.
> Artigo: *Hybrid ST-ResNet and LSTM approach for precise crime hotspot prediction* — Scientific Reports, 2025.

---

## 1. A ideia central em uma frase

Os autores ensinaram um computador a olhar o **histórico de furtos de Chicago** e prever **onde** (em quais quadras do mapa) e **quando** (em quais dias) novos furtos têm mais chance de acontecer — desenhando "mapas de calor" de risco para a polícia se antecipar.

---

## 2. O que é um "hotspot" (ponto quente)

Imagine o mapa da cidade dividido numa grade de quadradinhos (células). Um **hotspot** é uma célula onde o crime se concentra num dado período. Prever hotspots = dizer quais quadradinhos vão "esquentar" amanhã/na próxima semana.

A grade foi testada em três resoluções:
- **2000 m** (quadrados grandes, visão macro)
- **1000 m** (intermediário)
- **500 m** (quadrados pequenos, mais preciso e mais difícil)

Quanto menor o quadrado, mais útil para a polícia — e mais difícil acertar.

---

## 3. Os dados (a "matéria-prima" do modelo)

| Item | Detalhe |
|------|---------|
| Fonte | **Chicago Open Data Portal** (dados abertos do governo) |
| Volume | **242.770** registros de furto |
| Período | 01/01/2014 a 31/12/2016 (1.096 dias) |
| Extras | clima diário (temperatura, vento, condições), localização de **parques**, e marcação de **dia útil / fim de semana / feriado** |

> **Por que clima e parques?** Crime tem padrão social: muda com o tempo (frio/calor) e com o lugar (perto de parque, área turística). Dar esse "contexto" ao modelo melhora a previsão.

---

## 4. As duas redes neurais — e por que duas

O problema tem **duas dimensões ao mesmo tempo**: espaço (mapa) e tempo (dias). Cada rede cuida de uma.

### 4.1 CNN — a "rede que enxerga o mapa" (dimensão ESPAÇO)

- **CNN = Convolutional Neural Network** (Rede Neural Convolucional).
- Foi criada para **imagens**. Aqui, o mapa de crimes é tratado como uma imagem: cada pixel = uma célula da grade, e o "brilho" do pixel = quantidade de crime.
- **Convolução** = passar um pequeno filtro (uma janelinha 3×3) deslizando sobre a imagem para detectar **padrões locais** — por exemplo, "uma célula com muito crime cercada por células também perigosas". É assim que a CNN aprende a **vizinhança** espacial.
- **ST-ResNet** = a versão usada aqui. Significa *Spatio-Temporal Residual Network*:
  - *Residual* (o "Res") = um truque que permite empilhar muitas camadas sem o aprendizado "se perder" (as conexões residuais deixam a informação "pular" camadas). Isso evita um problema clássico de redes profundas.
  - Ela olha o passado em três escalas: **Closeness** (dias bem recentes), **Period** (padrão semanal) e **Trend** (tendência de longo prazo).
- **Atenção (attention)** = mecanismo que faz o modelo "dar mais peso" às regiões/momentos mais importantes, em vez de tratar tudo igual. Daí o nome "Attention-Enhanced ResUnits".

### 4.2 LSTM — a "rede com memória" (dimensão TEMPO)

- **LSTM = Long Short-Term Memory** (Memória de Longo e Curto Prazo). É um tipo de **RNN** (rede recorrente), feita para **sequências** (séries temporais, texto, áudio).
- Uma RNN comum "esquece" o passado distante. A LSTM resolve isso com **portões (gates)** internos que decidem o que **guardar**, o que **esquecer** e o que **usar agora**. Por isso ela consegue lembrar padrões de semanas atrás.
- Aqui ela recebe a sequência de dias e aprende a **dependência temporal**: "depois de um padrão assim, costuma vir um pico de furto".
- Testaram olhar **7, 14 ou 30 dias** para trás (o "lag").

### 4.3 Como elas se juntam

```
Mapa de crimes ─► CNN (ST-ResNet + atenção) ─┐
                                              ├─► camada final ─► previsão do hotspot
Sequência de dias ─► LSTM ────────────────────┤
Clima / parques / calendário ─────────────────┘
```

A CNN extrai o "onde", a LSTM o "quando", e uma **camada totalmente conectada** mistura tudo (incluindo clima e calendário) para cuspir a previsão final.

---

## 5. Como o modelo foi treinado e testado

| Conceito | O que é (em linguagem simples) |
|----------|-------------------------------|
| **Divisão 90/10** | 90% dos dados para o modelo **aprender** (treino+validação) e 10% **separados** para testar de verdade, em dados que ele nunca viu. |
| **Otimizador Adam** | O "algoritmo de ajuste" que vai corrigindo os pesos da rede a cada erro, de forma eficiente. |
| **Épocas (100–200)** | Quantas vezes o modelo passa por todos os dados aprendendo. |
| **Early stopping** | "Pare de treinar quando parar de melhorar" — evita **overfitting** (decorar em vez de aprender). |
| **Grid search** | Testar várias combinações de configurações (ex.: 64 vs 512 unidades na LSTM) e ficar com a melhor. |
| **Ablation study** | "Estudo de remoção": tirar uma peça por vez (a LSTM, o clima, os parques) para **provar que cada uma ajuda**. Se tirar e piorar, é porque a peça era útil. |

---

## 6. As métricas (como medir se acertou)

| Métrica | O que mede | Como ler |
|---------|-----------|----------|
| **RMSE** (Root Mean Square Error) | Erro médio entre previsto e real | **Menor = melhor.** Penaliza erros grandes. |
| **Mean Hit Rate** (Taxa de acerto) | Dos lugares que o modelo apontou como perigosos, quantos crimes realmente caíram ali | **Maior = melhor.** Medido em coberturas de 5%, 10%, 20% do território. |
| **PAI** (Predictive Accuracy Index) | Eficiência da previsão considerando a área usada | **Maior = melhor.** Premia acertar concentrando em pouca área. |

### Resultados (resolução de 500 m)
- **RMSE 0,2215** — 6,34% melhor que o baseline (ST-RN).
- **Hit Rate:** 62,32% apontando só 5% do território; 79,94% em 10%; 88,09% em 20%.
  - Tradução: vigiando apenas 20% da cidade (os lugares certos), o modelo "cobriria" ~88% dos furtos.
- Melhoria sobre o baseline em **todas** as resoluções (até 14,15% a 2000 m).

> **Baseline** = o modelo de comparação, o "adversário". Superar o baseline é o que prova que a ideia nova vale a pena.

---

## 7. Limitações (importante para pensar criticamente)

1. **Fins de semana e feriados:** erro consistentemente maior — a rotina das pessoas muda e o padrão fica irregular.
2. **Áreas turísticas densas** (ex.: orla do Lago Michigan): difícil prever, fluxo de gente imprevisível.
3. **Clima adverso:** acurácia cai; temperatura/umidade precisariam entrar de forma mais refinada.
4. **Uma só cidade:** treinado só com Chicago — não há garantia de que funcione igual em outra cidade ou país (viés geográfico).
5. **Transformers não ajudaram:** uma técnica mais nova (Transformer) teve desempenho ruim aqui por ser cara computacionalmente em alta resolução.

---

## 8. Glossário rápido (para a prova oral)

- **CNN:** rede que detecta padrões espaciais (imagens/mapas) via filtros de convolução.
- **RNN:** rede para sequências; processa um passo de cada vez carregando "memória".
- **LSTM:** RNN melhorada, com portões que guardam/esquecem informação — boa em séries temporais.
- **ST-ResNet:** CNN espaço-temporal com conexões residuais (camadas profundas sem perder o sinal).
- **Atenção:** mecanismo que pondera o que é mais relevante.
- **Hotspot:** célula do mapa com concentração de crime.
- **Overfitting:** quando o modelo "decora" o treino e vai mal no que é novo.
- **Baseline:** modelo de referência para comparação.
- **RMSE / Hit Rate / PAI:** métricas de erro e de acerto espacial.

---

## 9. Roteiro de 2–3 min para a apresentação oral

1. **Problema (20s):** polícia precisa antecipar furtos; metade dos crimes nem é reportada.
2. **Solução (40s):** modelo híbrido **CNN (espaço) + LSTM (tempo)** sobre 242 mil furtos abertos de Chicago, com clima e calendário.
3. **Resultado (40s):** RMSE 0,2215; vigiando 20% certo do território cobre ~88% dos furtos; bateu todos os baselines.
4. **Relevância (40s):** permite policiamento **preventivo e baseado em evidências** em vez de reativo.
5. **Sua opinião (20s):** forte no método, mas é só Chicago — replicar no Brasil exigiria dados locais e cuidado com viés.
