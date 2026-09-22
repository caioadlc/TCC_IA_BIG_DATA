# Previsão da Taxa de Inadimplência de Crédito PJ: uma Abordagem Hurdle Baseada em Perceptrons Multicamadas

Repositório complementar ao Trabalho de Conclusão de Curso, contendo o notebook de análise exploratória e modelagem preditiva da taxa de inadimplência de operações de crédito de Pessoa Jurídica (PJ) no Sistema Financeiro Nacional, sob a abordagem de Hurdle (classificação + regressão condicional) com redes neurais MLP.

## Sobre o experimento

O notebook cobre todo o pipeline experimental: ingestão e integração dos dados, engenharia de *features*, construção da variável-alvo via empilhamento multi-horizonte (*multi-horizon stacking*, h = 1 a 12 meses), particionamento temporal, pré-processamento, treinamento do modelo de Hurdle (classificador + regressor condicional), calibração isotônica das probabilidades, avaliação de desempenho (agregada, por decil de risco e por horizonte de previsão), interpretabilidade via SHAP e projeção financeira do saldo em risco (carteira ativa × taxa de inadimplência) frente aos *baselines* Naïve e Mean.

Este repositório é referenciado no apêndice da monografia como registro reprodutível do experimento descrito no Capítulo 4 (Avaliação Experimental).

## Estrutura do repositório

```
.
├── exploration_data_analysis.ipynb   # Notebook principal do experimento
├── README.md                         # Este arquivo
└── output/
    └── Figures/                      # Figuras geradas e utilizadas na monografia
```

## Ambiente e dependências

O notebook foi executado no Google Colab. Principais bibliotecas utilizadas:

- `pandas`, `numpy`, `polars` — manipulação de dados
- `seaborn`, `matplotlib` — visualização
- `scikit-learn` — pré-processamento, calibração isotônica
- `tensorflow`/`keras` — redes MLP (classificador e regressor)
- `shap` — interpretabilidade
- `optuna` — busca de hiperparâmetros (bloco exploratório)
- `catboost`, `lightgbm`, `xgboost` — usados apenas em blocos de benchmarking/exploração, não integram o pipeline final

Para rodar localmente, recomenda-se criar um ambiente virtual e instalar as dependências listadas no início do notebook.

## Origem e acesso aos dados

Os dados utilizados no experimento são provenientes de três fontes públicas, integradas em nível mensal (`anomes`, formato `YYYYMM`). Os dados **não estão versionados neste repositório** devido ao volume do arquivo agregado; as instruções de obtenção de cada fonte são descritas abaixo.

### 1. SCR — Sistema de Informações de Crédito (Banco Central do Brasil)

Base principal do experimento, com as operações de crédito de Pessoa Jurídica (PJ) segmentadas por UF, segmento da instituição financeira, porte do cliente e modalidade de crédito.

**Esta base não é baixada automaticamente pelo notebook** e precisa ser obtida manualmente:

1. Acesse o portal de dados abertos do BCB: [https://dadosabertos.bcb.gov.br/dataset/scr_data](https://dadosabertos.bcb.gov.br/dataset/scr_data)
2. Faça o download dos arquivos referentes ao período utilizado no experimento (2023–2025), filtrando `cliente == 'PJ'`.
3. Posicione os arquivos baixados no diretório esperado pelo notebook (indicado na célula de carregamento de dados) antes de executar o pipeline.

### 2. Taxa Selic (BACEN — API SGS, série 1178)

Consultada diretamente via API do Banco Central dentro do próprio notebook, com defasagens de 1, 3 e 6 meses calculadas em código. Não requer download manual.

### 3. INPC (IBGE — SIDRA, série 1736)

Consultado diretamente via Sistema IBGE de Recuperação Automática (SIDRA) dentro do próprio notebook, também com defasagens de 1, 3 e 6 meses. Não requer download manual.

> As rotinas de consulta e integração (`merge` por `anomes`) das séries de Selic e INPC com a base do SCR estão implementadas nas células iniciais do notebook, na seção de ingestão de dados.

## Como reproduzir

1. Baixe os dados do SCR conforme instruções acima.
2. Abra `exploration_data_analysis.ipynb` no Google Colab ou em um ambiente Jupyter local.
3. Ajuste os caminhos de leitura dos arquivos do SCR, se necessário.
4. Execute as células em ordem — a ingestão de Selic e INPC é feita automaticamente via API.

Observação: o notebook contém um bloco final marcado explicitamente como **"IGNORE ESSE BLOCO"**, referente a experimentação exploratória com modelos de árvore/lineares (não utilizados no modelo final de Hurdle). Esse bloco é mantido apenas como registro histórico de decisões de modelagem.

## Citação

Caso este material seja utilizado, favor referenciar a monografia correspondente:

> ARAUJO, Caio. **Previsão da Taxa de Inadimplência de Crédito PJ: uma Abordagem Hurdle Baseada em Perceptrons Multicamadas**. 2026. Trabalho de Conclusão de Curso — MBA em IA e BigData - ICMC USP.
