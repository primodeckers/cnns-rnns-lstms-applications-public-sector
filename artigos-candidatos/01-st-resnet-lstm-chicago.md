# Candidato 01 — ST-ResNet + LSTM (Chicago)

## Dados do artigo
- **Título:** Hybrid ST-ResNet and LSTM approach for precise crime hotspot prediction
- **Autores:** Nasim Shahmoradi, Ali Asghar Alesheikh, Ali Jafari, Aynaz Lotfata
- **Periódico:** *Scientific Reports* (Nature Portfolio)
- **Ano:** 2025
- **DOI:** 10.1038/s41598-025-24559-7
- **Link:** https://www.nature.com/articles/s41598-025-24559-7
- **Acesso:** Aberto (open access)

## Problema (setor público)
Prever *hotspots* de furto urbano para que a polícia aloque recursos de forma eficiente. Mais da metade dos crimes violentos não é reportada, o que cria lacuna de dados para prevenção.

## Dados reais
- **Fonte:** Chicago Open Data Portal (dados abertos governamentais)
- **Volume:** 242.770 registros de furto
- **Local/Período:** Chicago, IL — 01/01/2014 a 31/12/2016 (1.096 dias)
- **Extras:** clima diário, localização de parques, classificação dia útil/fim de semana/feriado

## Método (rede neural)
- **Arquitetura:** CNN espaço-temporal (ST-ResNet com atenção, "Attention-Enhanced ResUnits") + LSTM de camada única + camada totalmente conectada para variáveis externas.
- **Config:** filtros 3×3 (64); ResUnits 2/4/6; LSTM lag 7/14/30 dias; LSTM units 64/256/512; batch 8/16/32.
- **Treino/validação:** split 90% treino-val / 10% teste; Adam; 100–200 épocas com early stopping; grid search; *ablation study*.
- **Justificativa:** CNN captura padrão espacial, LSTM captura dependência temporal — combinação adequada a dados espaço-temporais de crime.

## Resultados (métricas)
- RMSE 0,2215 a 500 m (melhora de 6,34% sobre baseline ST-RN)
- RMSE 0,4762 a 1000 m (4,76%) e 0,9785 a 2000 m (14,15%)
- *Mean Hit Rate*: 62,32% (5%), 79,94% (10%), 88,09% (20%)
- Índice PAI utilizado
- Baselines comparados (ST-RN etc.) ✔

## Limitações (declaradas)
- Erro maior em fins de semana/feriados (turismo, rotina irregular)
- Dificuldade em áreas densas/turísticas (ex.: Lake Michigan)
- Sensível a clima adverso
- Transformers tiveram desempenho insatisfatório (complexidade quadrática)

## Observações para a atividade
Cobre TODOS os critérios com folga: journal top revisado por pares, dados governamentais reais, método e avaliação completos, baseline e implicação para políticas explícitos.
