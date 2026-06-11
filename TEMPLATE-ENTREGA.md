# Entrega — Redes Neurais Profundas Aplicadas ao Setor Público

> Preencha cada seção seguindo as instruções em comentário. Apague os comentários `> _(...)_` antes de entregar.
> Consulte o `[GUIA-PASSO-A-PASSO.md](GUIA-PASSO-A-PASSO.md)` se tiver dúvidas em alguma seção.

**Aluno(a):** Renê Estevam Deckers
**Área de interesse / atuação:** Segurança pública
**Data de entrega:** 11/06/2026

---

## 1. Dados do Artigo

- **Título:** Hybrid ST-ResNet and LSTM approach for precise crime hotspot prediction
- **Autores:** Nasim Shahmoradi, Ali Asghar Alesheikh, Ali Jafari, Aynaz Lotfata
- **Periódico / Conferência:** *Scientific Reports* (Nature Portfolio)
- **Ano:** 2025
- **DOI ou link de acesso:** [10.1038/s41598-025-24559-7](https://www.nature.com/articles/s41598-025-24559-7) (open access)

---

## 2. Problema de Pesquisa

A alocação eficiente de policiamento é uma necessidade das forças de segurança, mas mais da metade dos crimes não é reportada, o que gera lacunas de dados para a prevenção.  Este estudo investiga se um modelo combinado de CNN e LSTM é capaz de prever, com precisão tanto no espaço quanto no tempo, os hotspots de furto urbano em Chicago. A proposta é identificar onde e quando os crimes são mais prováveis, facilitando a alocação preventiva de agentes policiais.

---

## 3. Método (CNN, RNN ou LSTM)

**Contagem de palavras:** 97 / 100

oi empregada uma arquitetura hibrida CNN + LSTM: um ST-ResNet com atenção extrai padrões no espaço (filtros 3×3, ResUnits residuais) e uma LSTM de única camada capta a dependência no tempo, conectadas por uma camada densa às variáveis externas (clima, parques, feriados). Os dados — 242.770 casos de furto do Chicago Open Data Portal (2014–2016) — foram separados em 90% para treino e validação e 10% para teste, utilizando o otimizador Adam, com 100 a 200 épocas, aplicação de early stopping e busca em grade para hiperparâmetros.  A validação foi realizada utilizando RMSE, Mean Hit Rate e PAI. Essa combinação é pertinente uma vez que o crime possui uma natureza espaço-temporal: a CNN é responsável por modelar o espaço, enquanto a LSTM cuida do tempo.

---

## 4. Principais Resultados

### 4.1 Métricas quantitativas

- **RMSE (erro de previsão — quanto menor, melhor):** o modelo errou menos que o baseline ST-RN nas três escalas testadas — 0,2215 nas células de 500 m (erro 6,34% menor), 0,4762 nas de 1000 m (4,76% menor) e 0,9785 nas de 2000 m (14,15% menor). Destaque para a escala de 500 m (nível de quarteirão), onde os modelos anteriores costumavam falhar.
- **Mean Hit Rate (acerto de hotspots — quanto maior, melhor):** patrulhando apenas os 5% do território apontados como mais arriscados, o modelo captura 62,32% dos furtos reais; com 10% do território, 79,94%; e com 20%, 88,74% — ou seja, vigiando um quinto da cidade, cobre-se quase 9 em cada 10 furtos.
- **PAI (eficiência por área):** métrica complementar que premia capturar muitos crimes usando pouca área.
- O modelo proposto superou o baseline (ST-RN) e suas variações em todas as resoluções testadas.

### 4.2 Conclusões dos autores

O modelo híbrido ST-ResNet + LSTM com atenção supera os modelos de base na previsão de hotspots de furto, principalmente em resoluções espaciais mais detalhadas.  O estudo de ablação confirmou que a remoção do módulo LSTM, das variáveis externas e da proximidade de parques prejudica o desempenho — ou seja, cada parte é crucial para a acurácia.

### 4.3 Contribuição para políticas públicas

Gestores de segurança podem alocar patrulhamento de forma antecipada e fundamentada em evidências, concentrando os recursos onde e quando há maior risco, graças a previsões espaço-temporais confiáveis. Isso muda o policiamento de uma abordagem reativa para uma preventiva, o que pode levar a um uso mais eficaz do efetivo e à diminuição de furtos.

---

## 5. Reflexão Pessoal

### 5.1 Conexão com sua área

Trabalho com engenharia de dados na SEGES, orquestrando DAGs diárias no Airflow sobre Kubernetes, e este artigo me mostrou que o modelo de Chicago é, na essência, uma pipeline de dados com uma rede neural na ponta: sem ingestão confiável todas as madrugadas (crimes, clima, calendário), o mapa de risco simplesmente não existe. No setor público, o valor está menos no algoritmo e mais na esteira que o alimenta.

### 5.2 Crítica construtiva

A principal limitação é o **viés dos dados de registro policial**: o modelo aprende onde a polícia *registrou* crime, não onde ele *ocorre*, com risco de profecia autorrealizável sobre territórios já mais patrulhados. O artigo reconhece a subnotificação, mas não a trata — e há caminhos viáveis para isso: calibrar os registros com pesquisas de vitimização (que estimam a cifra oculta por região), monitorar continuamente a distribuição espacial das previsões para detectar concentração progressiva de patrulhamento, e incorporar as variáveis sociais que os próprios autores admitem faltar (fluxo de pessoas, uso do solo, indicadores socioeconômicos por célula). Para uma replicação no Brasil, o caminho concreto seria um **piloto em São Paulo** — único estado com microdados georreferenciados de boletins de ocorrência — começando em grade mais grossa (1–2 km, menos sensível à esparsidade), com auditoria de viés e avaliação por criminologistas antes de qualquer uso operacional. Em paralelo, a expansão nacional dependeria de o Sinesp evoluir de agregados município/mês para microdados anonimizados com coordenada e data.

### 5.3 Aprendizado

O maior aprendizado que levo para um projeto real é o peso da **contextualização do domínio**: boa parte do poder preditivo do modelo veio de atributos guiados pela teoria criminológica — a distância diária aos parques, o clima e o calendário —, tanto que a variante apenas com o canal de parques chegou a superar o modelo completo num dos cenários. Isso me ensina que, antes de escolher a arquitetura, vale investir em entender o fenômeno e traduzi-lo em dados: a rede neural potencializa o contexto, mas não o substitui.

---

## Checklist final (apague antes de entregar)

- [ ] Artigo publicado entre 2020 e 2026
- [ ] Artigo revisado por pares (journal/conferência indexada)
- [ ] Usa CNN, RNN ou LSTM (ou variações: BiLSTM, ConvLSTM, GRU)
- [ ] Usa dados reais ligados ao setor público
- [ ] As 5 seções estão preenchidas
- [ ] Seção 2 tem 2–3 frases
- [ ] Seção 3 tem no máximo 100 palavras
- [ ] Seção 4 cita pelo menos uma métrica numérica
- [ ] Reflexão pessoal é autoral (não é cópia do abstract)
- [ ] DOI ou link testado e funcionando
- [ ] Ortografia e linguagem acadêmica revisadas