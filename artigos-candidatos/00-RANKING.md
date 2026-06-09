# Ranking dos Candidatos — Segurança Pública

Avaliação dos artigos contra os requisitos de `ATIVIDADE.md` e os critérios de "bom artigo" do `GUIA-PASSO-A-PASSO.md`.
Legenda: ✅ atende · ⚠️ parcial / a confirmar · ❌ não atende (no material acessível)

## Matriz de requisitos

| # | Critério | 01 Chicago (Sci. Reports) | 02 Las Vegas (SMU) | 03 San Francisco (IntechOpen) | 04 Vídeo (WIDECOM) |
|---|----------|:---:|:---:|:---:|:---:|
| 1 | Publicado 2020–2026 | ✅ 2025 | ✅ 2024 | ✅ 2026 | ✅ 2024 |
| 2 | Revisado por pares | ✅ journal Q1 | ⚠️ review estudantil | ⚠️ cap. livro editorial | ✅ conferência |
| 3 | CNN / RNN / LSTM | ✅ CNN+LSTM | ✅ CNN+LSTM | ✅ CNN-LSTM | ✅ CNN+LSTM |
| 4 | Dados reais (governo) | ✅ Chicago Open Data | ✅ LVMPD | ✅ DataSF | ❌ não especificado |
| 5 | Setor público | ✅ segurança | ✅ segurança | ✅ segurança | ✅ segurança |
| 6 | Método descrito | ✅ completo | ⚠️ resumido | ✅ bom | ⚠️ resumido |
| 7 | Tabela de resultados/métricas | ✅ RMSE/HitRate/PAI | ✅ RMSE/MSE | ✅ MAE/MSE/RMSE | ⚠️ só acurácia |
| 8 | Dataset descrito (origem/tamanho/período) | ✅ completo | ⚠️ parcial | ⚠️ sem volume | ❌ ausente |
| 9 | Compara com baseline | ✅ ST-RN etc. | ⚠️ Cardiff model | ✅ DMD vs CNN-LSTM | ⚠️ vs só-CNN |
| 10 | Discute implicação p/ política pública | ✅ alocação policial | ✅ prevenção | ✅ capacidade preditiva | ✅ vigilância |
| 11 | Acesso ao texto completo | ✅ open access | ✅ aberto | ✅ aberto | ❌ paywall |
| 12 | Rede neural é o destaque do estudo | ✅ sim | ✅ sim | ❌ DMD venceu | ✅ sim |

## Pontuação (✅=1, ⚠️=0,5, ❌=0 · máx. 12)

| Posição | Artigo | Pontos | Veredito |
|:---:|--------|:---:|----------|
| 🥇 1º | **01 — Chicago (Scientific Reports)** | **12,0** | Escolha mais segura. Cobre tudo com folga. |
| 🥈 2º | 03 — San Francisco (IntechOpen) | 8,5 | Bom, mas CNN-LSTM perdeu p/ DMD e é cap. de livro. |
| 🥉 3º | 02 — Las Vegas (SMU) | 8,0 | Sólido, mas faltam detalhes; precisa ler PDF completo. |
| 4º | 04 — Vídeo-vigilância (WIDECOM) | 6,5 | Ângulo legal, mas dataset não documentado + paywall. |

## Recomendação

**Artigo 01 (Chicago — Scientific Reports)** é o vencedor claro: único com pontuação máxima.
- Journal de alto fator de impacto e open access (fácil citar e defender oralmente).
- Dados governamentais reais, volumosos e bem descritos.
- Método, treino, métricas, baselines e limitações todos explícitos → preenche as 5 seções sem buracos.

**Plano B:** se quiser o ângulo de imagem/vídeo em vez de série temporal, o 04 é o único; mas será preciso obter o PDF completo para documentar o dataset.

## Próximo passo
Escolhido o artigo, preencho as Seções 1–4 do `TEMPLATE-ENTREGA.md` a partir da ficha correspondente. A Seção 5 (reflexão) fica por sua conta — é a parte autoral exigida pela regra de uso de IA.
