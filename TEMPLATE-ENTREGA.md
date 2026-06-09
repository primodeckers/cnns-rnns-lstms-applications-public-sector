# Entrega — Redes Neurais Profundas Aplicadas ao Setor Público

> Preencha cada seção seguindo as instruções em comentário. Apague os comentários `> _(...)_` antes de entregar.
> Consulte o [`GUIA-PASSO-A-PASSO.md`](GUIA-PASSO-A-PASSO.md) se tiver dúvidas em alguma seção.

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

As forças de segurança precisam alocar policiamento de forma eficiente, mas mais da metade dos crimes não chega a ser reportada, criando lacunas de dados para a prevenção. O estudo investiga se um modelo híbrido CNN + LSTM consegue prever, com precisão espacial e temporal, os *hotspots* de furto urbano em Chicago. O objetivo é antecipar onde e quando os crimes tendem a se concentrar, apoiando a distribuição preventiva de recursos policiais.

---

## 3. Método (CNN, RNN ou LSTM)

**Contagem de palavras:** 97 / 100

Utilizou-se uma arquitetura híbrida CNN + LSTM: um ST-ResNet com mecanismo de atenção extrai padrões espaciais (filtros 3×3, ResUnits residuais) e uma LSTM de camada única captura a dependência temporal, integradas por camada totalmente conectada às variáveis externas (clima, parques, calendário). Os dados — 242.770 furtos do Chicago Open Data Portal (2014–2016) — foram divididos em 90% treino-validação e 10% teste, com otimizador Adam, 100–200 épocas, early stopping e grid search. A validação usou RMSE, Mean Hit Rate e PAI. A combinação é adequada porque o crime é espaço-temporal: a CNN modela o espaço e a LSTM, o tempo.

---

## 4. Principais Resultados

> _(Organize em três blocos.)_

### 4.1 Métricas quantitativas
- **RMSE** de 0,2215 na resolução de 500 m (melhora de 6,34% sobre o baseline ST-RN); 0,4762 a 1000 m (4,76%) e 0,9785 a 2000 m (14,15%).
- **Mean Hit Rate:** 62,32% (cobertura de 5%), 79,94% (10%) e 88,09% (20%).
- **Predictive Accuracy Index (PAI)** usado como métrica complementar.
- Comparado a baselines (ST-RN e variações), superando-os em todas as resoluções.

### 4.2 Conclusões dos autores
O modelo híbrido ST-ResNet + LSTM com atenção prevê *hotspots* de furto com maior precisão que os baselines, especialmente em resoluções espaciais mais finas. O *ablation study* confirmou que a remoção do módulo LSTM, das variáveis externas e da proximidade de parques degrada o desempenho — ou seja, cada componente contribui para a acurácia.

### 4.3 Contribuição para políticas públicas
Previsões espaço-temporais confiáveis permitem que gestores de segurança aloquem patrulhamento de forma antecipada e baseada em evidências, concentrando recursos nos locais e horários de maior risco. Isso desloca o policiamento de uma postura reativa para uma preventiva, com potencial de uso mais eficiente do efetivo e redução de furtos.



---

## 5. Reflexão Pessoal

> _(Esta parte precisa ser autoral. Não copie o abstract.)_

### 5.1 Conexão com sua área
> _("Na minha atuação em ..., isso se aplica porque ...")_



### 5.2 Crítica construtiva
> _(Escolha pelo menos uma: limitação metodológica, viés de dados, aplicabilidade prática.)_



### 5.3 Aprendizado
> _(O que você levaria para um projeto real.)_



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
