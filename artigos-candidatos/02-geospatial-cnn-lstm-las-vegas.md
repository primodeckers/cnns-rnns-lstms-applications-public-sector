# Candidato 02 — CNN + LSTM (Las Vegas)

## Dados do artigo
- **Título:** Geospatial Temporal Crime Prediction Using Convolution and LSTM Neural Networks: Enhancing the Las Vegas Cardiff Model
- **Autores:** Corey D. Holmes, Christian Orji, Chris Papesh
- **Periódico:** *SMU Data Science Review*, Vol. 8, No. 2, Art. 3
- **Ano:** 2024
- **DOI:** — (link estável institucional)
- **Link:** https://scholar.smu.edu/datasciencereview/vol8/iss2/3/
- **Acesso:** Aberto

## Problema (setor público)
Mais da metade dos crimes violentos nos EUA não é reportada, criando lacuna de dados. O estudo prevê onde/quando crimes violentos tendem a ocorrer para apoiar prevenção e policiamento.

## Dados reais
- **Fonte:** Las Vegas Metropolitan Police Department (LVMPD) — base policial pública
- **Local:** Las Vegas, Nevada
- **Período/Volume:** não especificados no resumo acessível

## Método (rede neural)
- **Arquitetura:** CNN + LSTM (CNN para componente espacial, LSTM para temporal).
- **Detalhes de camadas/hiperparâmetros:** não disponíveis no conteúdo acessado.

## Resultados (métricas)
- LSTM (só totais de crime): RMSE 8,621 em horizonte de 30 dias
- CNN + LSTM (com componente espacial): MSE 0,0009 em 30 dias
- Produz mapas de calor preditivos

## Limitações (declaradas)
- Depende de dados com latitude/longitude suficientes
- Limitações não enumeradas explicitamente no resumo

## Observações para a atividade
Bom enquadramento (dados policiais reais, CNN+LSTM), mas faltam detalhes de arquitetura, tamanho do dataset e período no material acessível — exigiria ler o PDF completo para preencher a Seção 3 com segurança.
