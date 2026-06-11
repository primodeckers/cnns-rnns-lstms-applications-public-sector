# Resumo Didático — Artigo de Chicago (CNN + LSTM para prever furtos)

> Material de estudo para entender o artigo a fundo e se preparar para a apresentação oral.
> Artigo: *Hybrid ST-ResNet and LSTM approach for precise crime hotspot prediction* — Shahmoradi, Alesheikh, Jafari & Lotfata, **Scientific Reports (Nature Portfolio), 2025**. DOI: 10.1038/s41598-025-24559-7 (acesso aberto).

---

## 1. A ideia central em uma frase

Os autores ensinaram um modelo de deep learning a olhar o **histórico de furtos de Chicago** e prever **onde** (em quais quadras do mapa) e **quando** (em quais dias) novos furtos têm mais chance de acontecer — desenhando "mapas de calor" de risco diários para a polícia se antecipar ao crime em vez de só reagir a ele.

A frase-chave do artigo: o modelo atinge **taxa média de acerto acima de 88% na resolução fina de 500 m**, justamente a escala em que os modelos anteriores falhavam — e a mais útil para o policiamento operacional (planejar a ronda da viatura, não a política do estado inteiro).

---

## 2. Por que esse problema importa (a motivação)

- O crime é uma das maiores ameaças à segurança urbana e à qualidade de vida. O FBI registrou **mais de 1,2 milhão de crimes violentos em 2017** nos EUA, com perdas de bilhões de dólares só em furtos.
- A criminologia clássica (teoria da atividade rotineira, escolha racional) já dizia que o crime tem **padrão espacial e temporal**: ele se concentra em certos lugares e se repete em certos horários/dias. O problema é transformar essa intuição em previsão numérica precisa.
- Se a polícia souber **com um dia de antecedência** quais células do mapa vão "esquentar", pode **alocar patrulhas de forma eficiente** — intervir antes do crime, e não depois. Isso é política pública baseada em evidência.
- O foco em **furto (theft)** foi escolhido porque é o crime mais frequente de Chicago (muitos dados para treinar) e porque furto exige **resposta rápida** — previsão mensal ou anual não ajuda a viatura de amanhã.

---

## 3. O que já existia — e por que não bastava

O artigo só faz sentido entendendo as **lacunas** que ele ataca. A evolução da área foi:

1. **GIS (sistemas de informação geográfica):** bons mapas do passado, zero capacidade de previsão.
2. **Estatística clássica (ARIMA, regressão bayesiana):** prevê séries temporais simples, mas não captura o espaço.
  - **ARIMA:** modelo que prevê o futuro de **uma série temporal** olhando os valores e erros dos dias anteriores (de forma linear). Bom para "quantos furtos na cidade amanhã?", mas cego ao espaço: cada célula do mapa seria uma série isolada, sem aprender nada dos vizinhos.
  - **Regressão bayesiana:** estima o efeito de variáveis (clima, renda etc.) sobre o crime, devolvendo a previsão **com incerteza** ("entre 3 e 7 furtos, 95% de confiança"). Boa para explicar fatores, mas as relações espaciais precisam ser definidas à mão — ela não descobre padrões sozinha.
  - **Por que não bastam:** nenhum dos dois aprende espaço e tempo **juntos e automaticamente** — exatamente o que a pergunta "qual quadradinho de 500 m vai esquentar amanhã?" exige.
3. **Machine learning tradicional (SVM, KNN, árvores, XGBoost):** melhorou, mas depende de ajuste manual e sofre com dados esparsos/ruidosos.
  - São algoritmos que aprendem relações **não lineares** a partir de exemplos (classificar célula como "quente/fria", por exemplo) — um avanço real sobre a estatística clássica.
  - **Limite 1 — engenharia manual de atributos:** eles não leem o mapa cru; um humano precisa criar as variáveis de entrada ("crimes na vizinhança nos últimos 7 dias", "distância ao centro"...). A qualidade do modelo fica refém da criatividade do pesquisador.
  - **Limite 2 — esparsidade e ruído:** na grade fina de 500 m, quase tudo é zero, e esses métodos tendem a "decorar" o ruído ou prever zero sempre. Já o deep learning extrai os padrões espaço-temporais **sozinho**, direto dos dados.
4. **Deep learning (a geração atual):** captura padrões espaço-temporais complexos sozinho. Mas mesmo os estudos recentes tinham **três problemas recorrentes**:


| Lacuna                                | Exemplo na literatura                                                                                                                        |
| ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Grade espacial grossa demais**      | Wang et al. usaram células de **17,8 km** em Los Angeles — não diz nada sobre o nível do bairro.                                             |
| **Sem modelagem sequencial do tempo** | O ST-ResNet original trata o tempo como "canais paralelos" (fatias empilhadas), perdendo a **ordem** dos dias.                               |
| **Só variáveis externas estáticas**   | Clima e feriado valem para a cidade inteira no dia — não variam de lugar para lugar. Faltava uma variável **dinâmica no espaço E no tempo**. |


Além disso, havia um achado incômodo: estudos multi-escala (Zhang et al., Jing et al.) mostravam que os modelos rendem melhor em grades **médias** (1000–2400 m) e **pioram** nas finas (500 m), porque em células pequenas os dados ficam **esparsos** (muitas células com zero crime). O artigo de Chicago ataca exatamente isso: ser o primeiro a **sustentar a melhor acurácia a 500 m**.

---

## 4. O que é um "hotspot" e como a cidade vira uma grade

Imagine o mapa de Chicago dividido numa grade de quadradinhos (células). Um **hotspot** é uma célula onde o crime se concentra num dado período. Prever hotspots = dizer quais quadradinhos vão "esquentar" amanhã.

Em termos simples: pense numa **planilha gigante deitada sobre o mapa** — cada quadradinho guarda quantos furtos aconteceram ali naquele dia. Assim, **cada dia vira uma "foto"** da criminalidade da cidade (quadradinho escuro = muito crime, claro = pouco). O modelo recebe as fotos dos dias passados e tenta **desenhar a foto de amanhã**. (Na notação do artigo: a grade tem I × J células e a foto do dia t é a matriz Xt; o alvo é prever a matriz do dia seguinte.)

Exemplo em miniatura (grade 5 × 5; cada número = furtos na célula naquele dia):

```
   Segunda (X₁)        Terça (X₂)         Quarta (X₃)           AMANHÃ (X₄) = ?
 ┌──┬──┬──┬──┬──┐   ┌──┬──┬──┬──┬──┐   ┌──┬──┬──┬──┬──┐      ┌──┬──┬──┬──┬──┐
 │ 0│ 0│ 1│ 0│ 0│   │ 0│ 1│ 0│ 0│ 0│   │ 0│ 0│ 1│ 0│ 0│      │ ?│ ?│ ?│ ?│ ?│
 ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤      ├──┼──┼──┼──┼──┤
 │ 0│ 2│ 5│ 1│ 0│   │ 1│ 3│ 6│ 2│ 0│   │ 0│ 4│ 7│ 2│ 1│      │ ?│ ?│ ?│ ?│ ?│
 ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤  ──► ├──┼──┼──┼──┼──┤
 │ 0│ 1│ 3│ 0│ 0│   │ 0│ 2│ 4│ 1│ 0│   │ 1│ 2│ 5│ 1│ 0│      │ ?│ ?│ ?│ ?│ ?│
 ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤      ├──┼──┼──┼──┼──┤
 │ 0│ 0│ 0│ 0│ 0│   │ 0│ 0│ 1│ 0│ 0│   │ 0│ 0│ 0│ 1│ 0│      │ ?│ ?│ ?│ ?│ ?│
 ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤   ├──┼──┼──┼──┼──┤      ├──┼──┼──┼──┼──┤
 │ 0│ 0│ 0│ 0│ 0│   │ 0│ 0│ 0│ 0│ 0│   │ 0│ 0│ 0│ 0│ 0│      │ ?│ ?│ ?│ ?│ ?│
 └──┴──┴──┴──┴──┘   └──┴──┴──┴──┴──┘   └──┴──┴──┴──┴──┘      └──┴──┴──┴──┴──┘
```

Repare em três coisas que o desenho já mostra:

- O **hotspot** é visível: o miolo da grade (a célula com 5 → 6 → 7) concentra o crime e está **crescendo** dia a dia — o modelo deve prever que amanhã continua quente.
- O crime "**vaza**" para os vizinhos do hotspot (as células ao redor têm 1, 2, 3...) — é esse padrão de vizinhança que a **CNN** capta.
- A **maioria das células é zero** (esparsidade) — e fica pior quanto menor o quadradinho; é o problema que o DPIM e a atenção atacam.

No artigo, a grade real a 500 m tem **84 × 70 = 5.880 células** — a mesma lógica, só que numa "foto" bem maior.

A grade foi testada em três resoluções:


| Resolução  | Grade resultante | Característica                                                                      |
| ---------- | ---------------- | ----------------------------------------------------------------------------------- |
| **2000 m** | 21 × 18 células  | visão macro, mais fácil de acertar, menos útil                                      |
| **1000 m** | 42 × 35 células  | intermediária — onde os modelos anteriores "batiam o teto"                          |
| **500 m**  | 84 × 70 células  | escala do quarteirão; mais útil para a patrulha e **mais difícil** (dados esparsos) |


> **Regra de bolso:** quanto menor o quadrado, mais útil para a polícia — e mais difícil acertar. A novidade do artigo é vencer justamente no quadrado pequeno.

---

## 5. Os dados (a "matéria-prima" do modelo)

### 5.1 Dados de crime (variável-alvo)


| Item                   | Detalhe                                                                   |
| ---------------------- | ------------------------------------------------------------------------- |
| Fonte                  | **Chicago Open Data Portal** — dados abertos do próprio governo municipal |
| Volume                 | **242.770** registros de furto                                            |
| Período                | 01/01/2014 a 31/12/2016 — **1.096 dias**                                  |
| Recorte espacial       | latitude [41,64°; 42,02°], longitude [−87,93°; −87,52°]                   |
| Granularidade temporal | **diária** (cada dia = uma matriz de contagem de furtos)                  |


### 5.2 Covariáveis (o "contexto" dado ao modelo)

- **Clima diário:** temperatura (°C), velocidade do vento (km/h) e nove condições do tempo (céu limpo, nublado, chuva, neve e combinações). A relação clima–crime é estudada há mais de 150 anos: calor → mais gente na rua → mais oportunidade de furto.
- **Calendário:** cada dia marcado como **dia útil, fim de semana ou feriado** (calendário oficial de Chicago). Crime tem ritmo semanal.
- **Parques:** localização de todos os parques da cidade (Chicago Park District). Aqui está **a grande novidade do artigo** — ver seção 6.3.

> **Por que dar contexto?** Crime é fenômeno social: muda com o tempo (frio/calor), com a rotina (semana/feriado) e com o lugar (perto de espaços públicos movimentados). Modelo que só vê o histórico de crimes ignora as causas que fazem o padrão mudar.

### 5.3 Pré-processamento — o problema da esparsidade e o DPIM

Quando a célula é pequena (500 m) e o intervalo é curto (1 dia), a matriz de crimes fica cheia de zeros — **esparsa e irregular**, o pesadelo de qualquer rede neural. Os autores usam duas técnicas:

- **DPIM (Diurnal Periodic Integral Mapping):** em vez da contagem "crua" de cada dia, o modelo usa a **soma acumulada dos dias recentes** — como uma foto de **longa exposição** em vez de um flash: a célula com 1 furto na segunda, 0 na terça e 1 na quarta deixa de aparecer como "0" isolado e vira "2 no acumulado". Isso "engorda" as células vazias sem inventar dados e suaviza o ruído preservando a dinâmica (célula quente continua mais quente que a fria). Sem isso, a rede aprenderia a resposta preguiçosa "prevê zero sempre". Importante: o DPIM foi aplicado **igualmente a todos os modelos comparados**, para a comparação ser justa.
- **Normalização Min-Max:** comprimir todos os valores para a escala de 0 a 1 com a fórmula `(x − mín) / (máx − mín)` — o menor valor vira 0, o maior vira 1, o resto fica proporcional no meio. Ex.: se as células variam de 0 a 20 furtos, uma célula com 5 vira 0,25. **Por quê?** As variáveis chegam em escalas muito diferentes (furtos: 0–20; temperatura: −20 a 35 °C; vento: 0–60 km/h). Sem normalizar, a variável de números grandes "grita mais alto" e domina o ajuste dos pesos, e o gradiente oscila — o treino fica lento ou instável. Com tudo entre 0 e 1, nenhuma variável domina por causa da unidade de medida e o otimizador converge mais rápido. (No fim, a previsão é "desnormalizada" de volta para a contagem real de crimes.)

Também foram removidos os pontos fora da área de estudo (outliers espaciais).

---

## 6. As duas redes neurais — e por que duas

O problema tem **duas dimensões ao mesmo tempo**: espaço (mapa) e tempo (sequência de dias). Cada rede cuida de uma — essa é a tese central do artigo.

### 6.1 CNN / ST-ResNet — a "rede que enxerga o mapa" (dimensão ESPAÇO)

- **CNN = Convolutional Neural Network** (Rede Neural Convolucional). Foi criada para **imagens** — e aqui o mapa diário de crimes É uma imagem: cada pixel = uma célula da grade, e o "brilho" do pixel = quantidade de crime.
- **Convolução** = passar um pequeno filtro (janelinha **3×3**) deslizando sobre a imagem para detectar **padrões locais** — ex.: "célula com muito crime cercada por células também perigosas". É assim que a CNN aprende a **vizinhança** espacial. O modelo usa **64 filtros de 3×3** na entrada de cada ramo.
- **Problema:** uma CNN rasa só enxerga vizinhança próxima. Para capturar dependências **distantes** (um padrão no norte da cidade que se relaciona com o sul) é preciso empilhar muitas camadas — e redes muito profundas sofrem do **gradiente que desaparece** (vanishing gradient).
  - **Vanishing gradient em uma linha:** a rede aprende passando um "recado de correção" de trás para frente, camada por camada; a cada camada o recado é multiplicado por números menores que 1 (0,5 × 0,5 × 0,5... ≈ 0) — como num **telefone sem fio**, ele chega às primeiras camadas fraco demais e elas param de aprender. Esse mesmo vilão aparece duas vezes no artigo: nas camadas da CNN (solução: ResNet) e nos passos de tempo da RNN (solução: LSTM).
- **Solução: conexões residuais (o "Res" de ResNet).** Cada bloco ganha um "atalho" que deixa a informação **pular camadas**. Com isso dá para empilhar profundidade sem perder o sinal — e a rede passa a enxergar padrões próximos **e** distantes.
- **ST-ResNet** (*Spatio-Temporal Residual Network*, de Zhang et al. 2017) organiza essa CNN residual em **três ramos paralelos**, cada um olhando o passado numa escala:
  - **Closeness (proximidade):** os últimos dias — "ontem estava quente, amanhã tende a continuar";
  - **Period (período):** o padrão **semanal** — "toda sexta esse lugar esquenta";
  - **Trend (tendência):** o padrão de **longo prazo / anual** — sazonalidade.
  - Cada ramo usa 3 fatias temporais da sua escala; as saídas dos três ramos são combinadas por uma **soma ponderada com pesos aprendíveis** (a própria rede aprende quanto cada escala importa).
- **Atenção (attention):** pense num **marca-texto automático**: depois que a convolução extrai os padrões do mapa, o bloco de atenção "grifa" as regiões que realmente importam (os hotspots) e apaga o que é ruído (o mar de células vazias). Em vez de tratar todas as células igualmente, a rede **aprende onde olhar**. Isso é crucial na grade de 500 m, onde quase tudo é zero e o sinal útil é raro. Os autores colocaram esse bloco **dentro de cada ResUnit** (após as convoluções) — daí o nome "**Attention-Enhanced ResUnits**". Ordem interna de cada ResUnit: batch normalization → ReLU → convolução → atenção.
- **ReLU (Rectified Linear Unit):** a função de ativação mais simples que existe: **positivo passa como está, negativo vira zero** — `ReLU(x) = max(0, x)`. Cumpre dois papéis: (1) dá **não linearidade** à rede (sem ela, empilhar 20 camadas equivaleria a uma só, e padrões complexos não seriam aprendidos); (2) ajuda contra o **vanishing gradient**, pois sua derivada é 1 nos valores positivos — o "recado de correção" atravessa as camadas sem encolher (diferente da sigmoid/tanh antigas).

### 6.2 LSTM — a "rede com memória" (dimensão TEMPO)

- **LSTM = Long Short-Term Memory** (Memória de Longo e Curto Prazo, Hochreiter & Schmidhuber, 1997). É um tipo de **RNN** (rede recorrente), feita para **sequências** (séries temporais, texto, áudio).
- Uma RNN comum "esquece" o passado distante (de novo o vanishing gradient, agora no tempo). A LSTM resolve isso com **portões (gates)** internos que decidem o que **guardar**, o que **esquecer** e o que **usar agora**. Por isso ela lembra padrões de semanas atrás.
- **Por que ela é necessária se o ST-ResNet já tem Closeness/Period/Trend?** Porque o ST-ResNet **concatena** as fatias de tempo num tensor único — ele vê "quais dias", mas perde **a ordem** entre eles (segunda → terça → quarta). A LSTM processa a sequência passo a passo, preservando a ordem e aprendendo dependências sequenciais: "depois de um padrão assim, costuma vir um pico de furto". Essa é exatamente a limitação do ST-ResNet que o artigo corrige.
- **Como entra no modelo:** cada linha da entrada é a sequência de n dias de crime de **uma** célula da grade; a LSTM (de camada única) processa todas as células e devolve uma matriz I × J de "características temporais", pronta para fundir com a saída espacial.
- Testaram janelas de **7, 14 ou 30 dias** para trás (o "lag") e **64, 256 ou 512 unidades** internas.

### 6.3 A novidade do artigo: distância ao parque como variável DINÂMICA

Essa é a contribuição mais original (primeira vez na literatura de previsão de crime):

- Para **cada furto**, calcula-se a **distância euclidiana diária até o parque mais próximo**.
- Essas distâncias formam uma **segunda matriz** Dt (mesma grade do crime), que entra no ST-ResNet como um **segundo canal de imagem** — como se a foto do crime fosse "RGB" com dois canais: canal 1 = quantos crimes, canal 2 = quão perto de parques.
- Por que parques? Teorias criminológicas apontam **espaços públicos como pontos focais**: concentram gente, e mais gente = mais alvos e mais oportunidade de furto.
- O detalhe importante é que essa variável é **dinâmica no espaço e no tempo** (muda célula a célula e dia a dia conforme onde os crimes ocorrem), diferente de clima e feriado, que são iguais para a cidade inteira. Era exatamente isso que faltava na literatura.

### 6.4 Como tudo se junta (a arquitetura completa)

```
                      ┌─ ramo Closeness (dias recentes)  ─┐
Crime (canal 1)   ──► ├─ ramo Period   (padrão semanal)   ├─ soma ponderada ─┐
Parques (canal 2) ──► └─ ramo Trend    (padrão anual)     ┘   (ST-ResNet     │
                         cada ramo: Conv → Attention-       com atenção)     │
                         Enhanced ResUnits → Conv                            ├─► FUSÃO ─► previsão
                                                                             │    do mapa de
Sequência de n dias por célula ──► LSTM ─► camada densa ────────────────────┤    crimes de
                                                                             │    amanhã
Clima + feriado/fim de semana (one-hot) ──► camada totalmente conectada ────┘
```

Em palavras: a CNN extrai o "**onde**" (em duas escalas de canal: crime + parques), a LSTM extrai o "**quando**" (ordem sequencial dos dias), e a **camada totalmente conectada** injeta o contexto externo (clima e calendário, codificados em one-hot). A fusão dos três módulos gera o mapa previsto do dia seguinte.

---

## 7. As 4 contribuições declaradas (bom para citar na prova)

1. **Primeiro uso da proximidade a parques** como variável dinâmica em previsão de crime.
2. **Integração de variáveis estáticas + dinâmicas** para enriquecer o contexto espaço-temporal.
3. **ST-ResNet aprimorado com LSTM e atenção**, capturando ordem sequencial e resistindo a dados esparsos.
4. **Primeira avaliação sistemática em três escalas** (2000/1000/500 m) mostrando que, ao contrário dos baselines (que rendem melhor a 1000 m), o modelo proposto **sustenta a melhor acurácia a 500 m** — a escala operacional da polícia.

---

## 8. Como o modelo foi treinado e testado


| Conceito                 | O que é (em linguagem simples)                                                                   | No artigo                                                                          |
| ------------------------ | ------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------- |
| **Divisão treino/teste** | Separar dados que o modelo nunca viu para o teste final honesto.                                 | ~90% treino+validação / 10% teste                                                  |
| **Otimizador Adam**      | O "algoritmo de ajuste" que corrige os pesos da rede a cada erro, de forma eficiente.            | batch size testado em 8, 16 e 32                                                   |
| **Épocas**               | Quantas vezes o modelo percorre todos os dados aprendendo.                                       | 100–200 épocas                                                                     |
| **Early stopping**       | "Pare de treinar quando parar de melhorar" — evita **overfitting** (decorar em vez de aprender). | usado                                                                              |
| **Grid search**          | Testar combinações de configurações e ficar com a melhor.                                        | ResUnits: 2/4/6; lag LSTM: 7/14/30 dias; unidades LSTM: 64/256/512; batch: 8/16/32 |
| **Repetição**            | Rodar várias vezes e tirar a média, porque redes neurais têm aleatoriedade.                      | cada modelo rodado **5 vezes**; RMSE médio reportado (± desvio)                    |
| **Implementação**        | —                                                                                                | Keras (inicialização padrão)                                                       |


### 8.1 O desenho da ablação (a parte metodologicamente mais elegante)

**Ablation study** = "estudo de remoção": provar que cada peça ajuda, tirando uma de cada vez. O artigo faz isso **nos dois sentidos**, o que dá evidência dupla:

- **Aditivo (de baixo para cima):** parte do baseline **ST-RN** (ST-ResNet com atenção, sozinho) e adiciona uma melhoria por vez:
  - **ST-RN(P)** = baseline + canal de **p**arques;
  - **ST-RN(E)** = baseline + variáveis **e**xternas (clima/calendário);
  - **ST-RN(L)** = baseline + **L**STM;
  - **Modelo proposto** = tudo junto.
- **Subtrativo (de cima para baixo):** parte do modelo completo e remove uma peça por vez:
  - **W/O(P)** = sem parques; **W/O(E)** = sem externas; **W/O(L)** = sem LSTM.

Se adicionar a peça melhora E remover a peça piora, a contribuição daquela peça está provada com folga.

---

## 9. As métricas (como medir se acertou)


| Métrica                                  | O que mede                                                                                                                                                                         | Como ler           |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| **RMSE** (Root Mean Square Error)        | Erro médio entre o número previsto e o real de crimes por célula. Eleva o erro ao quadrado antes de tirar a média, então **penaliza mais os erros grandes**.                       | **Menor = melhor** |
| **Mean Hit Rate** (taxa média de acerto) | Marcando como "hotspot" os X% de células mais arriscadas segundo o modelo, que fração dos crimes reais caiu dentro delas? Medido com cobertura de **5%, 10% e 20%** do território. | **Maior = melhor** |
| **PAI** (Predictive Accuracy Index)      | Razão entre a fração de crimes capturados e a fração de área usada. Premia quem acerta muito **usando pouca área** — eficiência da previsão.                                       | **Maior = melhor** |


> **Intuição do Hit Rate:** é a métrica "do mundo real". A polícia não pode patrulhar a cidade inteira; o Hit Rate responde "se eu patrulhar só os 20% mais arriscados que o modelo apontou, quantos por cento dos furtos eu cubro?".

---

## 10. Resultados em detalhe

### 10.1 RMSE por resolução (modelo proposto vs. baseline ST-RN)


| Resolução | Baseline ST-RN | Modelo proposto | Melhoria   |
| --------- | -------------- | --------------- | ---------- |
| 2000 m    | 1,1399         | **0,9785**      | **14,15%** |
| 1000 m    | 0,5000         | **0,4762**      | **4,76%**  |
| 500 m     | 0,2365         | **0,2215**      | **6,34%**  |


O modelo proposto venceu o baseline em **todas** as resoluções, com o menor desvio-padrão entre execuções (±0,0005 a 500 m — ou seja, resultado **estável**, não sorte de uma rodada).

### 10.2 Hit Rate a 500 m (modelo proposto)

- **62,32%** dos furtos capturados vigiando só **5%** do território;
- **79,94%** com **10%**;
- **~88,7%** com **20%**.

> Tradução para política pública: concentrando o patrulhamento em apenas **20% da cidade — os 20% certos —, cobre-se quase 9 em cada 10 furtos.**

Detalhe honesto que o próprio artigo destaca: no nível de 20% a 500 m, a variante **ST-RN(P)** (só baseline + parques) atingiu **89,09%**, levemente acima do modelo completo. Interpretação dos autores: o modelo completo é melhor para os hotspots **mais densos** (5% e 10%), enquanto o ST-RN(P) capta melhor os pontos de **menor densidade**. Também foi o ST-RN(P) que obteve o melhor PAI (4,4544). Isso reforça o peso da variável ambiental (parques) na escala fina.

### 10.3 O que a ablação revelou (qual peça importa mais?)

- **A LSTM é a peça mais crítica nas escalas maiores:** removê-la (W/O(L)) causou a maior queda a 2000 m e 1000 m; e adicioná-la sozinha ao baseline (ST-RN(L): 0,9863 a 2000 m) já quase iguala o modelo completo. Conclusão: **modelar a ordem temporal é o que mais agrega** em visão macro.
- **O canal de parques é a peça mais crítica na escala fina:** a 500 m, remover os parques (W/O(P)) derrubou visivelmente o acerto de hotspots a 20% de cobertura. Sem o contexto ambiental, a rede se perde nos detalhes do quarteirão.
- **Variáveis externas (clima/calendário) ajudam, mas menos** — sozinhas (ST-RN(E)) chegaram a piorar a identificação de hotspots em relação ao baseline num dos testes visuais.

### 10.4 Análises complementares (mostram domínio do artigo na prova)

- **Comparação dia a dia:** nos 72 dias de teste a 500 m, o modelo proposto venceu o baseline em **38 dias (52,78%)** contra 32 do baseline; a 1000 m, 51,39% contra 40,28%.
- **Efeito dos parques medido em erro absoluto (AE):** remover o canal de parques elevou o AE de 156 para 162 (**+6,41%**) nas áreas de alta densidade criminal.
- **Efeito da LSTM:** removê-la elevou o AE em **+3,85%** no último dia de teste, de novo concentrado nas áreas densas. A LSTM ajudou principalmente nos **dias úteis**, quando a rotina é estruturada e previsível.
- **Efeito do clima:** o modelo rende melhor em **dias de sol**; acurácia cai um pouco com chuva/neve. **Temperatura mais alta correlaciona com mais furtos** (mais atividade ao ar livre, mais interação social — coerente com a teoria criminológica).
- **Contra o estado da arte:** além do baseline, o modelo foi comparado com CNN-LSTM, Conv-LSTM, GRU, LSTM pura, ST-ResNet, **Vision Transformer (ViT)** e **Enhanced ST-GCN**. Padrão encontrado: todos esses rendem melhor a 1000 m e degradam a 500 m; o proposto é o único que **melhora** na escala fina — e ainda **treina mais rápido** que os híbridos e SOTA citados.

---

## 11. Limitações (importante para a crítica construtiva)

1. **Fins de semana e feriados:** erro consistentemente maior — a rotina muda, o padrão fica irregular, e nem LSTM nem clima capturam bem essa dinâmica "não rotineira".
2. **Áreas turísticas densas** (ex.: orla do Lago Michigan, norte de Chicago): fluxo diário de turistas gera flutuações fortes nos furtos; os autores reconhecem que faltam **variáveis sociais** no modelo para capturar isso.
3. **Clima adverso:** acurácia cai com chuva/neve; temperatura e umidade precisariam entrar de forma mais refinada.
4. **Ganho modesto no PAI** e erro absoluto ainda alto nos hotspots mais quentes — prever a célula certa é uma coisa, prever a **quantidade exata** de crimes nela ainda é difícil.
5. **Transformers não serviram:** os autores tentaram um Transformer espaço-temporal, mas a **complexidade quadrática** da autoatenção inviabilizou o treino em alta resolução e o desempenho foi insatisfatório. (Bom contraponto à ideia de que "Transformer resolve tudo".)
6. **Uma cidade, um crime (crítica nossa, não declarada):** treinado só com furtos de Chicago, 2014–2016. Não há garantia de generalização para outra cidade, outro tipo de crime ou outro país — replicar no Brasil exigiria dados abertos locais de qualidade equivalente. Há também a questão ética clássica do policiamento preditivo: dados de registro policial carregam **viés de subnotificação e de patrulhamento histórico** — o modelo aprende onde a polícia *registrou* crime, não necessariamente onde o crime *ocorre*.

---

## 12. Glossário rápido (para a prova oral)

- **CNN:** rede que detecta padrões espaciais (imagens/mapas) deslizando filtros de convolução.
- **RNN:** rede para sequências; processa um passo de cada vez carregando "memória".
- **LSTM:** RNN melhorada, com portões (esquecer/guardar/usar) que preservam informação de longo prazo — boa em séries temporais.
- **Vanishing gradient:** sinal de aprendizado que se dilui em redes muito profundas (ou sequências muito longas); motivou tanto o ResNet quanto a LSTM.
- **ResNet / conexões residuais:** "atalhos" que deixam a informação pular camadas, permitindo redes profundas estáveis.
- **ST-ResNet:** CNN residual espaço-temporal com três ramos de passado (Closeness = dias recentes, Period = semanal, Trend = anual) fundidos por pesos aprendíveis.
- **Atenção (attention):** mecanismo que pondera o que é mais relevante e suprime ruído; aqui, embutido dentro de cada ResUnit.
- **ReLU:** função de ativação "só passa positivo" (`max(0, x)`); dá não linearidade à rede e mantém o gradiente vivo em redes profundas.
- **DPIM:** técnica de soma acumulada no tempo que combate a esparsidade dos dados em grades finas.
- **Hotspot:** célula do mapa com concentração de crime.
- **Hit Rate / PAI / RMSE:** métricas de acerto espacial, eficiência por área e erro numérico.
- **Baseline:** o modelo de comparação, o "adversário" a ser batido — aqui, o ST-RN. Superar o baseline é o que prova que a ideia nova vale a pena.
- **Ablation study:** tirar (ou adicionar) uma peça por vez para provar a contribuição de cada componente.
- **Overfitting:** quando o modelo "decora" o treino e vai mal em dados novos; combatido com early stopping.
- **One-hot encoding:** transformar categorias (ex.: "feriado", "nublado") em vetores de 0s e 1s que a rede entende.
- **SOTA (state of the art):** os melhores métodos publicados até então (aqui: ViT, Enhanced ST-GCN).

---

## 13. Roteiro de 3 min para a apresentação oral

1. **Problema (30s):** crime urbano custa bilhões; a polícia precisa decidir onde patrulhar amanhã. Os modelos existentes só funcionavam em grades grossas (≥1 km) — boas para estatística, inúteis para a ronda. Pergunta do estudo: dá para prever hotspots de furto **por dia e por quarteirão (500 m)**?
2. **Método (60s):** modelo híbrido sobre **242.770 furtos abertos de Chicago (2014–2016)**. Três módulos: (a) **CNN residual com atenção (ST-ResNet)** lê o mapa diário de crimes como imagem e capta o padrão espacial em três escalas de passado (dias, semana, ano); (b) **LSTM** lê a sequência de dias e capta a ordem temporal que o ST-ResNet sozinho perde; (c) camada final injeta **clima e calendário**. Novidade inédita: a **distância diária de cada crime ao parque mais próximo** entra como segundo canal da imagem — primeira variável ambiental verdadeiramente dinâmica da literatura.
3. **Validação (30s):** 90/10 treino-teste, Adam, early stopping, grid search, 5 execuções por modelo, e **ablação dupla** (adicionando e removendo cada componente) para provar a contribuição de cada peça.
4. **Resultados (40s):** RMSE 0,2215 a 500 m (−6,34% vs. baseline; −14,15% a 2000 m). Vigiando os **20% certos** do território, cobre-se **~89% dos furtos**. Único modelo que melhora na escala fina, batendo CNN-LSTM, Conv-LSTM, GRU, ViT e ST-GCN — treinando mais rápido.
5. **Relevância pública + crítica (40s):** permite policiamento **preventivo, baseado em evidência e com dados abertos governamentais**. Limites: erro maior em fins de semana/feriados e áreas turísticas, só uma cidade e um tipo de crime, e o risco ético de retroalimentar viés de patrulhamento. Replicar no Brasil exigiria dados abertos locais e auditoria de viés.

### Perguntas prováveis do professor (e a resposta curta)

- **"Por que precisa de LSTM se o ST-ResNet já olha o passado?"** → O ST-ResNet concatena fatias de tempo num tensor: vê *quais* dias, mas perde a *ordem* entre eles. A LSTM processa a sequência passo a passo. A ablação confirma: tirar a LSTM causa a maior queda de acurácia nas escalas de 1000/2000 m.
- **"Qual a novidade de verdade?"** → A distância diária ao parque como **segundo canal** da CNN — primeira variável dinâmica no espaço-tempo na literatura de previsão de crime — mais a atenção dentro dos ResUnits e a vitória inédita na escala de 500 m.
- **"Por que não usar Transformer?"** → Eles tentaram: complexidade quadrática inviabilizou em alta resolução e o desempenho foi ruim. CNN+LSTM deu melhor equilíbrio entre acurácia e custo.
- **"Qual o risco de usar isso em política pública?"** → Dados policiais carregam viés de registro; um modelo treinado neles pode concentrar ainda mais o patrulhamento onde já se patrulha (profecia autorrealizável). Uso responsável exige auditoria e variáveis sociais complementares.

