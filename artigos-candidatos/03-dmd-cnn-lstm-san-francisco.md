# Candidato 03 — DMD vs CNN-LSTM (San Francisco)

## Dados do artigo
- **Título:** Spatio-Temporal Crime Prediction Using Dynamic Mode Decomposition and CNN-LSTM: A Comparative Study
- **Autores:** Adrian Joseph Albino, Julian Ernest Camello, Christine F. Peña
- **Publicação:** IntechOpen — capítulo de livro ("Matrix Theory and Principal Component Analysis Applications in Industry")
- **Ano:** 2026 (publicado em 12/03/2026)
- **DOI:** 10.5772/intechopen.1012879
- **Link:** https://www.intechopen.com/chapters/1229261
- **Acesso:** Aberto

## Problema (setor público)
Prever padrões espaço-temporais de criminalidade urbana para apoiar capacidade preditiva das forças de segurança ("crime ocorre quando ofensor, alvo e violação convergem no tempo e espaço").

## Dados reais
- **Fonte:** San Francisco Incident Reports — DataSF (portal oficial de dados abertos da cidade)
- **Local/Período:** San Francisco, CA — 01/01/2018 em diante
- **Volume:** não especificado; 27 colunas; linhas sem lat/long/data removidas

## Método (rede neural)
- **Arquitetura:** CNN-LSTM (CNN: convolução + pooling + FC para extração espacial; LSTM: previsão temporal) comparada com DMD (Dynamic Mode Decomposition).
- **Treino/validação:** split 80/20; agregação por data + junção espacial via GeoJSON em matrizes cluster-por-data.

## Resultados (métricas)
| Métrica | DMD | CNN-LSTM |
|---|---|---|
| MAE | 12,5742 | 18,9254 |
| MSE | 279,4321 | 426,2176 |
| RMSE | 16,7162 | 20,5643 |

> ⚠️ Aqui o **DMD venceu** o CNN-LSTM (~13 vs ~19 crimes/cluster/dia de desvio). O foco da rede neural fica como comparação, não como vencedor.

## Limitações (declaradas)
- Foco em tendência, não em previsão momento-a-momento
- Margem de erro grande em eventos atípicos
- CNN-LSTM sensível a outliers; design complexo pode causar overfitting
- Pior acurácia em áreas de baixa frequência de crime
- Escopo limitado aos bairros de SF definidos por GeoJSON

## Observações para a atividade
Dados governamentais reais e método comparativo bem descrito. **Atenção:** é capítulo de livro IntechOpen (revisão editorial, não journal/conferência clássica) — verificar se o(a) professor(a) aceita como "revisado por pares". Além disso, a rede neural NÃO foi o melhor modelo no estudo.
