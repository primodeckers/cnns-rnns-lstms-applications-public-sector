# Guia Passo a Passo — Como Fazer o Trabalho

Este guia transforma os requisitos da atividade em um fluxo prático. Siga na ordem.

---

## Visão geral do fluxo

```
1. Escolher artigo  →  2. Ler e anotar  →  3. Preencher template  →  4. Revisar  →  5. Entregar + preparar apresentação
```

**Tempo sugerido:** 2 a 4 horas (com atalho de busca: ~15 min para escolher o artigo).

---

## Passo 1 — Encontrar o artigo certo (15–30 min)

### Sim: pode ser qualquer tema do setor público

Não existe tema "obrigatório". Vale **qualquer área** em que o governo atua, por exemplo:

| Área | Exemplo de problema |
|------|---------------------|
| Saúde pública | Previsão de demanda hospitalar ou detecção em imagens médicas |
| Mobilidade urbana | Previsão de tráfego ou fluxo de transporte público |
| Segurança | Reconhecimento de padrões em câmeras ou análise de séries temporais |
| Meio ambiente | Monitoramento de desmatamento ou qualidade do ar |
| Educação | Análise de desempenho escolar ao longo do tempo |
| Gestão pública | Previsão de arrecadação ou demanda de serviços |

O que importa não é o tema específico, e sim: **CNN, RNN ou LSTM + dados reais + contexto governamental**.

---

### Atalho: você NÃO precisa vasculhar 5 sites

Os sites listados na atividade (ScienceDirect, IEEE, Springer…) são **opções**, não uma obrigação. Na prática:

1. Use **só o Google Scholar** na primeira tentativa (resolve a maioria dos casos).
2. Abra **3–5 abstracts** — não leia artigos inteiros ainda.
3. Escolha **1** e baixe o PDF.
4. Só vá a outro site se não achar nada em 20 minutos.

**Tempo realista com atalho:** 15–20 min para escolher + 1 h para ler.

---

### Busca rápida no Google Scholar (copie e cole)

Clique em um link, leia só o resumo, descarte se não servir:

- [CNN + public sector (2020–2026)](https://scholar.google.com/scholar?q=CNN+public+sector&as_ylo=2020&as_yhi=2026)
- [LSTM + government data (2020–2026)](https://scholar.google.com/scholar?q=LSTM+government+data&as_ylo=2020&as_yhi=2026)
- [RNN + policy evaluation (2020–2026)](https://scholar.google.com/scholar?q=RNN+policy+evaluation&as_ylo=2020&as_yhi=2026)
- [deep learning + urban mobility + LSTM (2020–2026)](https://scholar.google.com/scholar?q=deep+learning+urban+mobility+LSTM&as_ylo=2020&as_yhi=2026)

**Filtro rápido no abstract (30 segundos):** confira se o artigo menciona explicitamente CNN, RNN ou LSTM; se cita um dataset real (órgão, plataforma, volume de dados); e se tem seção de evaluation ou results. Se falhar em dois desses três critérios, feche e passe para o próximo.

---

### O que o artigo PRECISA ter

Antes de ler tudo, confira estes critérios no resumo (abstract):

| Critério | O que procurar |
|----------|----------------|
| Período | Publicado entre 2020 e 2026 |
| Revisão por pares | Journal/conference indexada (não blog, não preprint sem revisão) |
| Rede neural adequada | CNN, RNN ou LSTM (ou variações como BiLSTM, ConvLSTM) |
| Dados reais | Não só simulação; menciona dataset, órgão, base governamental, etc. |
| Setor público | Saúde, educação, mobilidade, segurança, meio ambiente, gestão pública, etc. |
| Método descrito | Seções de methodology, experiments, evaluation |

### Se preferir buscar por conta (estratégia completa)

1. Abra o [Google Scholar](https://scholar.google.com).
2. Use os links prontos acima **ou** termos como `CNN public sector`, `LSTM government data`, `RNN policy evaluation`.
3. Abra 3–5 candidatos e descarte pelos critérios do filtro rápido.
4. Escolha **1 artigo** que você consiga entender razoavelmente.

### Sinais de que é um bom artigo

- Tem tabela de resultados (accuracy, RMSE, MAE, F1, etc.).
- Descreve dataset (origem, tamanho, período, tipo de dado).
- Compara o modelo com baseline ou método anterior.
- Discute implicações para gestão pública ou políticas.

### Evite

- Artigos só teóricos, sem experimento.
- Trabalhos que usam dados sintéticos sem ligação com governo.
- Repetir artigo que colega já postou (pergunte na turma se possível).

---

## Passo 2 — Ler o artigo de forma eficiente (45–90 min)

Não leia linearmente do início ao fim na primeira passada. Use esta ordem:

### 2.1 Primeira leitura (15 min)

Leia apenas:

1. **Título e abstract**
2. **Introdução** (últimos parágrafos — pergunta de pesquisa)
3. **Conclusão**
4. **Tabelas/Figuras** de resultados

Perguntas para responder mentalmente:

- Qual problema público eles resolvem?
- Qual rede neural usaram (CNN, RNN ou LSTM)?
- Funcionou bem ou não?

### 2.2 Segunda leitura (30–45 min)

Aprofunde nas seções:

| Seção do artigo | O que anotar para o trabalho |
|-----------------|------------------------------|
| Introduction | Desafio público + pergunta central → **Seção 2** |
| Related Work | Contexto (opcional, ajuda na reflexão) |
| Method / Model | Arquitetura, camadas, hiperparâmetros, treino → **Seção 3** |
| Dataset | De onde vieram os dados, quantos registros, tipo (imagem, série temporal, texto) |
| Experiments / Evaluation | Métricas, validação cruzada, test set → **Seções 3 e 4** |
| Results | Números concretos, comparações → **Seção 4** |
| Discussion / Limitations | Críticas prontas → **Seção 5** |

### 2.3 Anotações (use um rascunho)

Crie um arquivo de notas com bullets. Exemplo:

```
- Problema: prever demanda de leitos hospitalares em hospitais públicos
- Modelo: LSTM com 2 camadas, 128 unidades
- Dados: 5 anos de internações de hospital municipal (2018–2023)
- Métricas: RMSE = 12,3 leitos, melhor que ARIMA (18,7)
- Limitação: dados de uma única cidade
```

---

## Passo 3 — Preencher a entrega (60–90 min)

Use o arquivo [`TEMPLATE-ENTREGA.md`](TEMPLATE-ENTREGA.md). Cada seção corresponde a um requisito da atividade.

### Seção 1 — Dados do Artigo

Copie do site do periódico ou da primeira página do PDF:

- Título completo
- Lista de autores
- Nome do journal/conference
- Ano
- DOI (preferível) ou URL estável

### Seção 2 — Problema de Pesquisa (2–3 frases)

**Fórmula útil:**

> [Contexto do setor público]. O estudo investiga [pergunta central]. O objetivo é [o que queriam alcançar com a rede neural].

**Exemplo de estrutura (adaptar ao SEU artigo):**

> Hospitais públicos enfrentam picos imprevisíveis de demanda por leitos, dificultando o planejamento de recursos. O estudo investiga se uma rede LSTM pode prever a ocupação hospitalar com base em séries históricas de internações. O objetivo é apoiar a alocação antecipada de leitos e reduzir filas de atendimento.

### Seção 3 — Método (máx. 100 palavras)

Inclua **sempre** estes 4 elementos, nesta ordem:

1. **Rede neural** — ex.: "LSTM bidirecional com 2 camadas" ou "CNN com 3 blocos convolucionais"
2. **Dados de treino** — origem, volume, divisão train/val/test, tipo de dado
3. **Validação** — métricas usadas (RMSE, MAE, accuracy, F1, etc.)
4. **Justificativa** — por que essa arquitetura para esse problema (série temporal → LSTM; imagem → CNN; etc.)

**Dica:** conte as palavras. Se passar de 100, corte adjetivos e detalhes secundários.

### Seção 4 — Principais Resultados

Organize em 3 blocos:

1. **Métricas quantitativas** — cite números do artigo
2. **Conclusões dos autores** — o que eles afirmam ter demonstrado
3. **Contribuição para políticas públicas** — traduza o resultado em impacto prático (eficiência, planejamento, decisão, etc.)

### Seção 5 — Reflexão Pessoal

Esta parte **precisa ser sua**. Estrutura sugerida:

1. **Conexão com sua área** — "Na minha atuação em X, isso se aplica porque..."
2. **Crítica construtiva** — limitação metodológica, viés de dados, aplicabilidade prática
3. **Aprendizado** — o que você levaria para um projeto real

---

## Passo 4 — Revisar antes de entregar (20–30 min)

Percorra a entrega nesta ordem:

1. Confirme que o artigo está entre 2020 e 2026.
2. Verifique se todas as 5 seções estão preenchidas.
3. Conte as palavras da seção 3 — deve ter no máximo 100.
4. Confira se a seção 2 tem 2–3 frases.
5. Garanta que a seção 4 cita pelo menos uma métrica numérica.
6. Leia a reflexão pessoal e confirme que não é cópia do abstract.
7. Teste se o DOI ou link funciona.
8. Revise ortografia e linguagem acadêmica.

**Teste de autoria (importante para a regra de IA):** leia em voz alta. Se você não conseguir explicar a arquitetura da rede ou defender a crítica na seção 5, reescreva com suas palavras após reler o artigo.

---

## Passo 5 — Preparar apresentação oral (15–20 min)

Mesmo sem saber se será chamado, prepare um roteiro de **2–3 minutos**:

1. **Problema** (20 s) — qual desafio público
2. **Solução** (40 s) — qual rede neural (CNN, RNN ou LSTM) e por quê
3. **Resultado** (40 s) — 1–2 métricas principais
4. **Relevância** (40 s) — por que importa para o setor público
5. **Sua opinião** (20 s) — uma limitação ou oportunidade

---

## Como usar IA de forma permitida (e segura)

| Pode usar IA para | Não use IA para |
|-------------------|-----------------|
| Explicar o que é LSTM, convolução, RMSE, etc. | Escrever a reflexão pessoal inteira |
| Resumir um parágrafo difícil do artigo | Gerar o texto completo da entrega |
| Revisar clareza e gramática | Inventar métricas que não estão no paper |
| Sugerir estrutura de frases | Escolher o artigo sem você ler |

**Fluxo recomendado:** você escreve → IA ajuda a clarificar → você valida no PDF do artigo.

---

## Cronograma sugerido (se o prazo é 11/06/2026)

| Quando | Tarefa |
|--------|--------|
| Dia 1 | Buscar e escolher artigo |
| Dia 2 | Ler, anotar, preencher rascunho |
| Dia 3 | Revisar, finalizar, preparar fala |
| Véspera do prazo | Conferência final |

---

## Próximo passo

1. Abra [`TEMPLATE-ENTREGA.md`](TEMPLATE-ENTREGA.md)
2. Escolha seu artigo
3. Preencha seção por seção usando suas anotações

Se quiser ajuda em uma etapa específica (buscar artigo na sua área, entender uma métrica, revisar um rascunho), traga o artigo ou o texto que você escreveu.
