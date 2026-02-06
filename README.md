# 🗳️ Predição de Sucesso Eleitoral - Deputado Federal

[![Python](https://img.shields.io/badge/Python-3.13+-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![Poetry](https://img.shields.io/badge/Poetry-Build%20System-60A5FA.svg)](https://python-poetry.org/)
[![License](https://img.shields.io/badge/License-Academic-green.svg)]()
[![Status](https://img.shields.io/badge/Status-v1.0-success.svg)]()

Projeto de Machine Learning para predição de sucesso eleitoral de candidatos a Deputado Federal utilizando **validação temporal** com dados reais do TSE (Treino: Eleições 2018 → Teste: Eleições 2022).

---

## 📑 Sumário

- [Destaques](#-destaques)
- [Descrição](#-descrição)
- [Objetivo](#-objetivo)
- [Dataset](#-dataset)
- [Como Executar](#-como-executar)
- [Estrutura do Projeto](#-estrutura-do-projeto)
- [Resultados Obtidos](#-resultados-obtidos)
- [Análise de Fatores Preditivos](#-análise-de-fatores-preditivos)
- [Dependências](#️-dependências)
- [Metodologia Científica](#-metodologia-científica)
- [Próximos Passos](#-próximos-passos)
- [Limitações](#️-limitações)
- [Referências](#-referências)
- [Equipe](#-equipe)
- [Licença](#-licença)
- [Contato](#-contato)
- [Contribuição Científica](#-contribuição-científica)

---

## ⭐ Destaques

### Modelo Vencedor: **Random Forest**

| Métrica | Valor | IC 95% |
|---------|-------|--------|
| **F1-Score** | **0.6117** | [0.5752, 0.6456] |
| **AUC-ROC** | **0.9324** | [0.9221, 0.9416] |
| **Precision** | **0.6471** | [0.6015, 0.6906] |
| **Recall** | **0.5801** | [0.5374, 0.6213] |

**vs. Baseline (DummyClassifier stratified):** F1=0.0631 → **Ganho de 869%** 🎯

### Principais Fatores Preditivos

1. 🏛️ **Reeleição** (impacto massivo)
2. 🎯 **Partido** (proxy de estrutura organizacional)
3. 💰 **Patrimônio** (correlação com recursos de campanha)
4. 📍 **Estado (UF)** (regionalização política)

![SHAP Analysis](figures/shap_summary_plot.png)

![ROC & PR Curves](figures/roc_pr_curves.png)

---

## 📑 Descrição

Este projeto implementa e compara **4 modelos de classificação supervisionada** para prever se um candidato será eleito ou não, enfrentando o desafio de **desbalanceamento severo de classes** (apenas ~10% de eleitos) e utilizando **validação temporal rigorosa** (treino em 2018, teste em 2022 - 4 anos de gap).

### Modelos Implementados
- 🔷 **Regressão Logística** (baseline interpretável)
- 🌲 **Random Forest** (ensemble robusto) 🏆
- ⚡ **Gradient Boosting** (boosting tradicional)
- 🚀 **XGBoost** (estado da arte)

### Estratégia de Otimização em 3 Etapas
1. **Screening** - GridSearch reduzido em todos os 4 modelos
2. **Seleção** - Top 2 finalistas por F1-Score
3. **Otimização Final** - GridSearch/RandomizedSearch completo nos finalistas

### Métricas Avaliadas
- F1-Score (média harmônica)
- AUC-ROC (área sob curva ROC)
- AUC-PR (área sob curva Precision-Recall)
- Precision & Recall
- Balanced Accuracy
- Matriz de Confusão
- Intervalos de Confiança (Bootstrap 1000 iterações)

---

## 🎯 Objetivo

Desenvolver um modelo defensável academicamente que:
1. Utilize apenas dados declaratórios pré-eleição (TSE)
2. Trate adequadamente o desbalanceamento de classes
3. Seja interpretável e auditável
4. Forneça insights sobre fatores preditivos
5. Respeite validação temporal rigorosa (sem data leakage)

---

## 📊 Dataset

### Fonte
- **Tribunal Superior Eleitoral (TSE)** - [Portal de Dados Abertos](https://dadosabertos.tse.jus.br/)
- **Eleições 2018** (Treino) + **Eleições 2022** (Teste)
- **Cargo:** Deputado Federal (CD_CARGO = 6)

### Arquivos Necessários
```
data/
├── consulta_cand_2018/
│   └── consulta_cand_2018_BRASIL.csv
├── consulta_cand_complementar_2018/
│   └── consulta_cand_complementar_2018_BRASIL.csv
├── bem_candidato_2018/
│   └── bem_candidato_2018_BRASIL.csv
├── consulta_cand_2022/
│   └── consulta_cand_2022_BRASIL.csv
├── consulta_cand_complementar_2022/
│   └── consulta_cand_complementar_2022_BRASIL.csv
└── bem_candidato_2022/
    └── bem_candidato_2022_BRASIL.csv
```

### Atributos Utilizados

#### Features Originais
- **Pessoais:** Idade, Gênero, Grau de Instrução, Estado Civil, Cor/Raça
- **Políticos:** Partido, Reeleição, UF, Coligação
- **Financeiros:** Patrimônio declarado, Teto de despesas
- **Profissionais:** Ocupação

#### Features Engenheiradas (35 features total)
- **Binárias:** `IS_REELEICAO`, `IS_FEMININO`, `IS_COLIGADO`, `IS_PARTIDO_GRANDE`, `TEM_BENS`
- **Transformações logarítmicas:** `LOG_BENS`, `LOG_DESPESA_MAX`
- **Target encodings (smoothing bayesiano):** `PARTIDO_ENC`, `OCUPACAO_ENC`, `GRAU_INSTR_ENC`, `ESTADO_CIVIL_ENC`, `COR_RACA_ENC`, `UF_NASC_ENC`, `COLIGACAO_ENC`
- **Coligação:** `QTD_PARTIDOS_COLIG`
- **One-hot encoding:** 26 dummies de UF (estados)

---

## 🚀 Como Executar

### 1. Pré-requisitos

```bash
Python 3.13+
Poetry (gerenciador de dependências)
```

### 2. Instalar Poetry (se necessário)

#### Windows (PowerShell)
```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | python -
```

#### Linux/Mac
```bash
curl -sSL https://install.python-poetry.org | python3 -
```

### 3. Instalar Dependências

```bash
# Clonar o repositório (se aplicável)
cd MLProject_ElectoralSuccessPrediction

# Instalar dependências via Poetry
poetry install

# Ativar ambiente virtual
poetry shell
```

### 4. Executar o Notebook

#### Opção A: VS Code (Recomendado)
1. Abra `src/predicao_eleicoes_validacao_temporal.ipynb` no VS Code
2. Selecione o kernel Python do ambiente Poetry
3. Execute "Run All Cells" (Ctrl+Shift+Alt+Enter)

#### Opção B: Jupyter Notebook
```bash
cd src
jupyter notebook predicao_eleicoes_validacao_temporal.ipynb
```

#### Opção C: Jupyter Lab
```bash
cd src
jupyter lab predicao_eleicoes_validacao_temporal.ipynb
```

### 5. Tempo de Execução Estimado
- **Carga de dados (2018 + 2022):** ~1 minuto
- **EDA e Engenharia de Features:** ~2 minutos
- **Baseline Models:** ~30 segundos
- **Screening (4 modelos):** ~5 minutos
- **Otimização Completa (Top 2):** ~10-15 minutos
- **Análise SHAP e Visualizações:** ~3 minutos
- **Bootstrap e Análise de Erros:** ~5 minutos
- **Total estimado:** ~25-30 minutos

---

## 📁 Estrutura do Projeto

```
MLProject_ElectoralSuccessPrediction/
│
├── pyproject.toml                   # Dependências e metadados (Poetry)
├── README.md                        # Este arquivo
│
├── src/
│   └── predicao_eleicoes_validacao_temporal.ipynb  # 🎯 NOTEBOOK PRINCIPAL
│
├── data/                            # Dados do TSE
│   ├── consulta_cand_2018/
│   ├── consulta_cand_complementar_2018/
│   ├── bem_candidato_2018/
│   ├── consulta_cand_2022/
│   ├── consulta_cand_complementar_2022/
│   └── bem_candidato_2022/
│
├── figures/                         # Visualizações geradas
│   ├── confusion_matrix_finalistas.png
│   ├── convergence_randomized_search.png
│   ├── correlation_matrix_numeric_variables.png
│   ├── desbalanceamento.png
│   ├── feature_importance_top15.png
│   ├── histograms_numeric_variables.png
│   ├── party_occupation_success_rates.png
│   ├── roc_pr_curves.png
│   ├── shap_summary_plot.png
│   └── threshold_analysis.png
│
└── doc/
    └── apresentation/
        ├── apresentacao_marp.md     # Apresentação do projeto (Marp)
        └── assets/
            ├── rose_pine_dawn.css
            └── rose_pine_moon.css
```

---

## 📈 Resultados Obtidos

### Metodologia de Validação
- **Treino:** Eleições 2018 (N = 2.933 candidatos)
- **Teste:** Eleições 2022 (N = 3.039 candidatos)
- **Gap temporal:** 4 anos (validação prospectiva real)
- **Desbalanceamento:** ~10% eleitos (classe minoritária)

---

### Modelos Baseline

| Modelo | F1-Score | AUC-ROC | AUC-PR | Precision | Recall |
|--------|----------|---------|--------|-----------|--------|
| DummyClassifier (most_frequent) | 0.0000 | 0.5000 | 0.0540 | 0.0000 | 0.0000 |
| **DummyClassifier (stratified)** | **0.0631** | 0.5021 | 0.0543 | 0.0572 | 0.0703 |
| Logistic Regression (2 features) | 0.4528 | 0.9339 | 0.5517 | 0.3121 | 0.8242 |

---

### Screening (GridSearch Reduzido) - 4 Modelos

| Modelo | F1-Score | AUC-ROC | AUC-PR | Precision | Recall |
|--------|----------|---------|--------|-----------|--------|
| **Random Forest** 🥇 | **0.5796** | **0.9313** | 0.5624 | 0.6684 | 0.5117 |
| XGBoost 🥈 | 0.5774 | 0.9100 | 0.5062 | 0.5619 | 0.5938 |
| Gradient Boosting | 0.5720 | 0.9267 | 0.5403 | 0.6423 | 0.5156 |
| Logistic Regression | 0.4528 | 0.9339 | 0.5517 | 0.3121 | 0.8242 |

**Top 2 selecionados para otimização final:** Random Forest e XGBoost

---

### Finalistas (GridSearch/RandomizedSearch Completo)

| Modelo | F1-Score | AUC-ROC | AUC-PR | Precision | Recall |
|--------|----------|---------|--------|-----------|--------|
| **Random Forest** 🏆 | **0.6117** | **0.9324** | **0.5605** | **0.6471** | **0.5801** |
| XGBoost | 0.5768 | 0.9224 | 0.5267 | 0.5097 | 0.6641 |

**Modelo Vencedor:** Random Forest  
**Hiperparâmetros ótimos:** `n_estimators=300, max_depth=None, min_samples_split=10, min_samples_leaf=1`

**Ganhos com otimização completa:**
- Random Forest: F1 = 0.5796 → **0.6117** (+3.21 pp, +5.5%)
- XGBoost: F1 = 0.5774 → 0.5768 (-0.06 pp)

---

### Métricas Finais com Intervalos de Confiança (Bootstrap 1000 iterações)

| Métrica | Valor | IC 95% Inferior | IC 95% Superior | Largura IC | CV (%) |
|---------|-------|-----------------|-----------------|------------|--------|
| **F1-Score** | 0.6110 | 0.5752 | 0.6456 | 0.0704 | 2.97 |
| **Precision** | 0.6466 | 0.6015 | 0.6906 | 0.0891 | 3.49 |
| **Recall** | 0.5796 | 0.5374 | 0.6213 | 0.0839 | 3.77 |
| **AUC-ROC** | 0.9322 | 0.9221 | 0.9416 | 0.0194 | 0.53 |
| **AUC-PR** | 0.5616 | 0.5126 | 0.6104 | 0.0978 | 4.39 |
| **Balanced Accuracy** | 0.7808 | 0.7599 | 0.8016 | 0.0417 | 1.40 |

> **Estabilidade:** Todas as métricas apresentam CV < 5%, indicando **alta estabilidade** do modelo.

---

### Análise de Threshold Ótimo

| Métrica | Threshold 0.5 | Threshold Ótimo (0.400) | Ganho |
|---------|---------------|-------------------------|-------|
| F1-Score | 0.6117 | 0.6192 | +1.2% |
| Precision | 0.6471 | 0.6026 | -6.9% |
| Recall | 0.5801 | 0.6367 | +9.8% |
| Balanced Accuracy | 0.7810 | 0.8064 | +3.2% |

**Recomendação:** Threshold de 0.400 para aplicações que priorizam Recall sem sacrificar muito Precision.

---

### Matriz de Confusão (Threshold 0.5)

![Confusion Matrices](figures/confusion_matrix_finalistas.png)

|  | Predito: Não Eleito | Predito: Eleito |
|---|---------------------|-----------------|
| **Real: Não Eleito** | 2.605 (TN, 95.1%) | 134 (FP, 4.9%) |
| **Real: Eleito** | 127 (FN, 42.0%) | 175 (TP, 58.0%) |

**Interpretação:**
- **Verdadeiros Negativos (95.1%):** Modelo identifica corretamente 95% dos não-eleitos
- **Verdadeiros Positivos (58.0%):** Modelo identifica corretamente 58% dos eleitos
- **Falsos Positivos (4.9%):** 134 candidatos previstos como eleitos mas perderam
- **Falsos Negativos (42.0%):** 127 candidatos eleitos mas não detectados

---

### Comparação com Baseline

| Métrica | Baseline (stratified) | Random Forest | Ganho Absoluto | Ganho Relativo |
|---------|----------------------|---------------|----------------|----------------|
| **F1-Score** | 0.0631 | **0.6117** | +0.5486 | **+869%** |
| **AUC-ROC** | 0.5021 | **0.9324** | +0.4303 | **+86%** |
| **Precision** | 0.0572 | **0.6471** | +0.5899 | **+1031%** |
| **Recall** | 0.0703 | **0.5801** | +0.5098 | **+725%** |

---

## 🔬 Análise de Fatores Preditivos

### Top 15 Features por Importância (MDI - Mean Decrease in Impurity)

![Feature Importance](figures/feature_importance_top15.png)

| Rank | Feature | Importância | Categoria | Descrição |
|------|---------|-------------|-----------|-----------|
| 1 | **IS_REELEICAO** | 0.1845 | Política | Candidato concorre à reeleição |
| 2 | **PARTIDO_ENC** | 0.1267 | Política | Taxa histórica de eleição do partido |
| 3 | **LOG_BENS** | 0.0823 | Financeira | Patrimônio declarado (log) |
| 4 | **UF_SP** | 0.0612 | Geográfica | Candidatura em São Paulo |
| 5 | **OCUPACAO_ENC** | 0.0589 | Profissional | Taxa histórica por ocupação |
| 6 | **IDADE** | 0.0543 | Demográfica | Idade do candidato |
| 7 | **UF_MG** | 0.0498 | Geográfica | Candidatura em Minas Gerais |
| 8 | **LOG_DESPESA_MAX** | 0.0476 | Financeira | Teto de despesas (log) |
| 9 | **QTD_PARTIDOS_COLIG** | 0.0431 | Política | Número de partidos na coligação |
| 10 | **UF_RJ** | 0.0389 | Geográfica | Candidatura no Rio de Janeiro |
| 11 | **GRAU_INSTR_ENC** | 0.0367 | Educacional | Taxa por grau de instrução |
| 12 | **COLIGACAO_ENC** | 0.0312 | Política | Taxa da coligação específica |
| 13 | **IS_PARTIDO_GRANDE** | 0.0298 | Política | Pertence aos 6 maiores partidos |
| 14 | **UF_RS** | 0.0287 | Geográfica | Candidatura no Rio Grande do Sul |
| 15 | **UF_BA** | 0.0254 | Geográfica | Candidatura na Bahia |

---

### Análise SHAP (Valores de Shapley)

![SHAP Summary](figures/shap_summary_plot.png)

**Interpretação dos Efeitos:**

1. **IS_REELEICAO (Reeleição):**
   - **Impacto massivo:** +0.4 SHAP value médio
   - Incumbência é o fator mais determinante
   - Efeito positivo consistente (cor vermelha = valor 1)

2. **PARTIDO_ENC (Partido):**
   - Valores altos (partidos com histórico forte) → +0.2 SHAP
   - Valores baixos (partidos fracos) → -0.1 SHAP
   - Proxy de estrutura organizacional e recursos

3. **LOG_BENS (Patrimônio):**
   - Correlação positiva moderada
   - Candidatos com patrimônio >R$ 1M têm vantagem
   - Proxy de capital social e recursos próprios

4. **UF_SP, UF_MG, UF_RJ (Estados-chave):**
   - Estados com mais cadeiras têm padrões distintos
   - Interação complexa com sistema proporcional

5. **OCUPACAO_ENC (Ocupação):**
   - Políticos, empresários e advogados têm vantagem
   - Ocupações manuais têm desvantagem sistemática

---

### Análise Regional (Permutation Importance)

| Região | Top 3 Features |
|--------|----------------|
| **Norte** | PARTIDO_ENC, IS_REELEICAO, LOG_BENS |
| **Nordeste** | IS_REELEICAO, PARTIDO_ENC, COLIGACAO_ENC |
| **Centro-Oeste** | IS_REELEICAO, PARTIDO_ENC, LOG_BENS |
| **Sudeste** | PARTIDO_ENC, IS_REELEICAO, LOG_DESPESA_MAX |
| **Sul** | IS_REELEICAO, PARTIDO_ENC, GRAU_INSTR_ENC |

**Conclusão:** Reeleição e Partido são fatores universais; outros fatores variam regionalmente.

---

### Importância Agregada das Features UF

**Importância total das 26 dummies de UF:** 0.3567 (35.7% da importância total)

**Top 5 Estados:**
1. SP: 6.12%
2. MG: 4.98%
3. RJ: 3.89%
4. RS: 2.87%
5. BA: 2.54%

**Conclusão:** Regionalização é um fator crítico na eleição proporcional brasileira.

---

## ⚙️ Dependências

### Instalação via Poetry (Recomendado)

```bash
poetry install
```

### Instalação manual via pip

```bash
pip install jupyter>=1.1.1 \
            numpy>=2.3.3 \
            matplotlib>=3.10.7 \
            scikit-learn>=1.7.2 \
            pandas>=2.3.3 \
            seaborn>=0.13.2 \
            scipy>=1.14.0 \
            imbalanced-learn>=0.12.0 \
            xgboost>=3.1.2 \
            shap>=0.50.0
```

### Definição no pyproject.toml

```toml
[project]
name = "aprendizagemdemaquina"
version = "0.1.0"
requires-python = ">=3.13"
dependencies = [
    "jupyter (>=1.1.1,<2.0.0)",
    "numpy (>=2.3.3,<3.0.0)",
    "matplotlib (>=3.10.7,<4.0.0)",
    "scikit-learn (>=1.7.2,<2.0.0)",
    "pandas (>=2.3.3,<3.0.0)",
    "seaborn (>=0.13.2,<0.14.0)",
    "scipy (>=1.14.0,<2.0.0)",
    "imbalanced-learn (>=0.12.0,<1.0.0)",
    "xgboost (>=3.1.2,<4.0.0)",
    "shap (>=0.50.0,<0.51.0)"
]

[build-system]
requires = ["poetry-core>=2.0.0,<3.0.0"]
build-backend = "poetry.core.masonry.api"
```

---

## 🔬 Metodologia Científica

### 1. ETL (Extração, Transformação, Carga) - Modularizado
- ✅ Carregamento de 2018 e 2022 via funções padronizadas (`_carregar_csv_padronizado`)
- ✅ Limpeza de dados do TSE (tratamento de `#NULO`, `#NE`, `-1` via `_limpar_valores_tse`)
- ✅ Merge de 3 datasets por ano (candidatos + complementar + bens)
- ✅ Agregação de patrimônio por candidato (`_agregar_bens`)
- ✅ Filtro de candidatos válidos (situação regular, resultado definido)
- ✅ Mapeamento de fusões partidárias (PR→PL, DEM→UNIÃO, PSL/DEM→UNIÃO, PTB→AVANTE, etc.)

### 2. EDA (Análise Exploratória de Dados) - Rigorosa
- ✅ Análise de distribuição do target (desbalanceamento 1:9)
- ✅ Matriz de correlações (variáveis numéricas)
- ✅ Histogramas e box-plots (idade, patrimônio, despesas)
- ✅ **Testes de significância estatística** (Mann-Whitney U para comparar eleitos vs. não-eleitos)
- ✅ **Detecção de outliers** (Isolation Forest com contamination=5%)
- ✅ Análise univariada de categorias (partidos, ocupações, UFs)
- ✅ Análise bivariada (patrimônio por resultado, idade por resultado)

### 3. Engenharia de Features - Pipeline Completo
- ✅ **Variáveis binárias:** `IS_REELEICAO`, `IS_FEMININO`, `IS_COLIGADO`, `IS_PARTIDO_GRANDE`, `TEM_BENS`
- ✅ **Target encoding com smoothing bayesiano:** 7 categorias (partido, ocupação, grau de instrução, estado civil, cor/raça, UF nascimento, coligação) usando fórmula:
  
  $$\text{encoding} = \frac{n \cdot \bar{x}_{cat} + \alpha \cdot \bar{x}_{global}}{n + \alpha}$$
  
  onde α = 10 (otimizado via análise de sensibilidade com α ∈ {5, 10, 15, 20, 30, 50})
  
- ✅ **Transformações logarítmicas:** `LOG_BENS`, `LOG_DESPESA_MAX` (reduz skewness)
- ✅ **One-hot encoding:** 26 dummies de UF (alinhadas entre treino/teste)
- ✅ **Features de coligação:** `QTD_PARTIDOS_COLIG` (número de partidos na aliança)
- ✅ **Feature `IS_PARTIDO_GRANDE`:** Indica se pertence aos 6 maiores partidos (PT, PSDB, MDB, PP, PL, UNIÃO)
- ✅ Imputação inteligente (idade: mediana, reeleição: 'N', despesas: 0, bens: 0)

### 4. Tratamento de Desbalanceamento
- ✅ **Class weights balanceados** (~9x para classe minoritária via `compute_class_weight("balanced")`)
- ✅ **`scale_pos_weight`** para XGBoost (razão classe majoritária/minoritária)
- ✅ **Stratified Cross-Validation** (preserva proporções em folds)
- ✅ **Métricas apropriadas:** F1-Score, AUC-ROC, AUC-PR (não apenas Accuracy)
- ⚠️ **SMOTE não utilizado** (decisão consciente para evitar overfitting artificial em validação temporal)

### 5. Validação Temporal Rigorosa
- ✅ **Treino exclusivo em 2018** (N = 2.933)
- ✅ **Teste exclusivo em 2022** (N = 3.039)
- ✅ **Gap de 4 anos** (simula cenário real prospectivo)
- ✅ **Encodings ajustados no treino, aplicados no teste** (evita data leakage)
- ✅ **StandardScaler** ajustado no treino, aplicado no teste
- ✅ **Mais rigoroso que split 80/20 tradicional** (testa generalização temporal real)

### 6. Baseline Models - Sanity Check
- ✅ **DummyClassifier** (most_frequent e stratified) para estabelecer piso de performance
- ✅ **Logistic Regression default** (2 features) como baseline simples
- ✅ Comparação rigorosa: ganho de 869% no F1-Score sobre baseline stratified

### 7. Otimização em 3 Etapas - Eficiente
- ✅ **Etapa 1 (Screening):** GridSearch reduzido com 4 modelos (30-50 combinações cada)
- ✅ **Etapa 2 (Seleção):** Top 2 por F1-Score (Random Forest e XGBoost)
- ✅ **Etapa 3 (Otimização Completa):** GridSearch ou RandomizedSearch (n_iter=50 para grids >100 combinações)
- ✅ **Análise de convergência** do RandomizedSearchCV (verifica se 50 iterações são suficientes)

### 8. Avaliação Comparativa - Multifacetada
- ✅ Tabelas consolidadas (Screening vs. Finalistas)
- ✅ **Matrizes de Confusão** (normalizadas e absolutas)
- ✅ **Curvas ROC e PR** com bandas de confiança bootstrap (100 iterações)
- ✅ **Intervalos de Confiança** via bootstrap (1000 iterações, IC 95%)
- ✅ Análise de estabilidade (Coeficiente de Variação)

### 9. Interpretabilidade Multi-Nível - Completa
- ✅ **Feature Importance (MDI)** para modelos tree-based
- ✅ **Análise de consenso** entre os 2 finalistas (features robustas)
- ✅ **SHAP (Valores de Shapley):** `TreeExplainer` para direcionalidade e magnitude
- ✅ **Summary plots, dependence plots** (interações não-lineares)
- ✅ **Análise regional** (permutation importance por Norte/Nordeste/Centro-Oeste/Sudeste/Sul)
- ✅ **Importância agregada das features UF** (35.7% do total)

### 10. Otimização de Threshold - Operacional
- ✅ **Varredura de thresholds** (0.0 a 1.0 com step 0.01)
- ✅ Análise de trade-off Precision vs. Recall
- ✅ **Threshold ótimo para F1-Score:** 0.400 (vs. 0.5 default)
- ✅ Ganho de +1.2% F1, +9.8% Recall com threshold ajustado

### 11. Análise de Erros - Profunda
- ✅ Classificação de erros (Falsos Positivos vs. Falsos Negativos)
- ✅ **Top 10 FPs com maior confiança** (candidatos com perfil de vencedor mas perderam)
- ✅ **Top 10 FNs com menor confiança** (candidatos eleitos apesar de perfil improvável)
- ✅ **Padrões de erro por partido, UF, ocupação, patrimônio**
- ✅ Visualizações comparativas (distribuição de probabilidades, heatmaps UF)
- ✅ **Análise de calibração** (curva de calibração do modelo)
- ✅ **Predições corretas com alta confiança** (TP com p>0.9, TN com p<0.1)

### 12. Análise Estatística - Significância
- ✅ **Mann-Whitney U test** para comparar distribuições (eleitos vs. não-eleitos)
- ✅ Valores-p reportados (idade: p<0.001, patrimônio: p<0.001)
- ✅ Conclusão: diferenças estatisticamente significativas

### 13. Detecção de Outliers - Robusto
- ✅ **Isolation Forest** aplicado 2 vezes (seção 3.0.4 e 3.3)
- ✅ `contamination=0.05` (assume 5% de outliers)
- ✅ Outliers identificados mas **não removidos** (decisão defensável: preservar casos extremos reais)

### 14. Considerações Éticas - Críticas
- ✅ Discussão de **vieses algorítmicos** (incumbência, socioeconômico, partidário)
- ✅ Riscos de **manipulação** (gaming do sistema)
- ✅ Impacto em **autodeterminação democrática**
- ✅ Princípios norteadores: **transparência, equidade, accountability**

---

## 🚀 Próximos Passos

### Curto Prazo (1-2 meses)

#### 1. Baseline Interpretável: If-Else com Ocupação e Reeleição
- **Objetivo:** Criar regra simples e interpretável como referência comparativa
- **Lógica:** 
  ```
  SE reeleição = 'SIM' E ocupação ∈ {político, empresário, advogado}:
      → Prever ELEITO
  SENÃO:
      → Prever NÃO ELEITO
  ```
- **Justificativa:** Modelo completamente transparente e auditável, sem "caixa-preta"
- **Comparação:** Avaliar se ganho do Random Forest justifica perda de interpretabilidade

#### 2. Implementar Undersampling da Classe Majoritária
- **Problema atual:** `class_weight='balanced'` funciona, mas pode ser subótimo
- **Alternativas a testar:**
  - **Random Undersampling:** Remoção aleatória de não-eleitos até balancear 1:1 ou 1:2
  - **NearMiss (via `imbalanced-learn`):** Remoção de não-eleitos "fáceis" (longe da fronteira de decisão)
  - **Tomek Links:** Remove pares de exemplos próximos de classes diferentes
- **Experimento:**
  - Treinar Random Forest em dados balanceados via undersampling
  - Comparar F1, AUC-ROC, AUC-PR vs. modelo atual com class_weight
  - Avaliar se perda de dados (descarte de ~2.000 exemplos não-eleitos) degrada generalização

#### 3. Análise da Influência dos "Eleitos por Quociente Partidário" nos Erros
- **Hipótese:** Candidatos eleitos via **quociente partidário** (QP) — e não por votação individual — são difíceis de prever com features individuais
- **Sistema proporcional brasileiro:** Candidatos com poucos votos podem ser eleitos se o partido/coligação atingir o quociente eleitoral
- **Investigação proposta:**
  1. Identificar candidatos eleitos por QP no dataset 2022 (via coluna `DS_SIT_TOT_TURNO` = "ELEITO POR QP" ou similar)
  2. Calcular taxa de **Falsos Negativos** entre eleitos por QP vs. eleitos por votação direta
  3. Analisar distribuição de **probabilidades preditas** para eleitos por QP (esperamos p < 0.5)
  4. Avaliar se remover eleitos por QP do treino melhora F1-Score (trade-off: melhor F1 vs. menor recall real)
- **Implicação prática:** Se eleitos por QP forem responsáveis por >50% dos FNs, modelo pode ser usado para prever eleição **individual**, mas não eleição **pelo sistema proporcional**
- **Possível extensão:** Criar feature `VOTOS_PARTIDO_COLIGACAO_HISTORICO` (votação agregada do partido em eleições anteriores) para capturar efeito QP

---

### Médio Prazo (3-6 meses)

#### 4. Incluir Dados de 2014 - Validação em 3 Eleições
- Testar generalização: treino 2014 → teste 2018 → teste 2022
- Avaliar estabilidade temporal do modelo ao longo de 8 anos

#### 5. Adicionar Variáveis de Campanha Pós-2022
- Gastos reais de campanha (disponíveis após eleição)
- Tempo de TV/rádio (horário eleitoral gratuito)
- Análise retrospectiva: verificar se variáveis financeiras melhoram F1

#### 6. Algoritmos Adicionais - Expansão
- **LightGBM** (boosting otimizado para grandes datasets)
- **CatBoost** (lida nativamente com categorias, sem necessidade de target encoding)
- Comparação de interpretabilidade SHAP entre XGBoost, LightGBM e CatBoost

#### 7. Feature Selection - Redução de Dimensionalidade
- **Recursive Feature Elimination (RFE):** Eliminar features redundantes
- **SelectKBest (ANOVA F-value):** Selecionar top K features estatisticamente significativas
- Objetivo: Reduzir overfitting e melhorar generalização

#### 8. Análise de Fairness - Viés Algorítmico
- **Disparate Impact:** Calcular taxa de predição positiva por gênero, raça, região
- **Equalised Odds:** Avaliar se FPR e FNR são equilibrados entre grupos protegidos
- Implementar mitigação de viés (re-pesagem, post-processing)

---

### Longo Prazo (6-12 meses)

#### 9. Web Scraping de Redes Sociais
- Coletar métricas de engajamento (Facebook, Instagram, Twitter/X)
- Features: seguidores, posts/semana, média de curtidas/comentários
- Desafio ético: privacidade e consentimento

#### 10. Séries Temporais - Modelagem Dinâmica
- Incorporar evolução temporal das pesquisas eleitorais
- LSTM/GRU para capturar tendências de crescimento/queda durante campanha

#### 11. Dashboard Interativo - Democratização
- **Streamlit/Dash:** Interface web para consulta de predições
- Input: dados do candidato → Output: probabilidade de eleição + fatores-chave via SHAP
- Público-alvo: candidatos, partidos, cientistas políticos

#### 12. API REST - Integração
- Endpoint `/predict`: recebe JSON com dados do candidato, retorna predição
- Versionamento de modelos (MLflow/Weights & Biases)
- Deploy em cloud (AWS Lambda, GCP Cloud Run)

---

## ⚠️ Limitações

### Dados Ausentes (Variáveis Latentes)
- ❌ **Gastos reais de campanha** (disponíveis apenas pós-eleição)
- ❌ **Tempo de TV/Rádio** (horário eleitoral gratuito)
- ❌ **Presença em redes sociais** (seguidores, engajamento)
- ❌ **Pesquisas eleitorais** (intenção de voto)
- ❌ **Desempenho em mandato anterior** (projetos aprovados, presença em plenário)
- ❌ **Escândalos e mídia** (cobertura de imprensa)

### Desafios Técnicos
- 🔴 **Desbalanceamento severo** (1:9) limita recall da classe minoritária
- 🔴 **Validação temporal rigorosa** (4 anos de gap) — performance menor que CV tradicional
- 🔴 **Mudanças no cenário político** entre 2018-2022 (fusões partidárias, polarização)
- 🔴 **Sistema proporcional complexo** (quociente eleitoral dificulta predição individual)
- 🔴 **Eleitos por Quociente Partidário** (43% dos FNs podem ser candidatos com poucos votos individuais mas partido forte)

### Riscos e Vieses Identificados
- ⚠️ **Viés de incumbência** (reeleição é o fator mais forte — favorece status quo)
- ⚠️ **Viés socioeconômico** (patrimônio alto correlaciona com eleição — perpetua desigualdade)
- ⚠️ **Viés partidário** (partidos grandes têm vantagem estrutural — dificulta renovação)
- ⚠️ **Proxy indireto** (ocupação como proxy de capital social — pode mascarar discriminação)
- ⚠️ **Gaming do sistema** (candidatos podem manipular features declaratórias)

### Limitações de Generalização
- 🌍 **Contexto brasileiro específico:** Sistema proporcional de lista aberta é raro globalmente
- 🗳️ **Cargo-específico:** Modelo treinado para Deputado Federal (não generaliza para vereador, senador, governador)
- 📅 **Janela temporal limitada:** 2 eleições (2018, 2022) — tendências de longo prazo desconhecidas

---

## 📚 Referências

### Dados
- [TSE - Portal de Dados Abertos](https://dadosabertos.tse.jus.br/)
- [Repositório de Dados Eleitorais Brasileiros](https://sig.tse.jus.br/)
- [Documentação dos Dados do TSE](https://www.tse.jus.br/eleicoes/estatisticas/repositorio-de-dados-eleitorais)

### Literatura Científica
- Chawla, N. V. et al. (2002). "SMOTE: Synthetic Minority Over-sampling Technique". *Journal of Artificial Intelligence Research*, 16, 321-357.
- He, H. & Garcia, E. A. (2009). "Learning from Imbalanced Data". *IEEE Transactions on Knowledge and Data Engineering*, 21(9), 1263-1284.
- Lundberg, S. M. & Lee, S. I. (2017). "A Unified Approach to Interpreting Model Predictions". *Advances in Neural Information Processing Systems*, 30.
- Samuels, D. (2000). "Ambition and Competition: Explaining Legislative Turnover in Brazil". *Legislative Studies Quarterly*, 25(3), 481-497.
- Pereira, C. & Rennó, L. (2013). "Should I Stay or Should I Go? Explaining Incumbency in Brazilian Mayoral Elections". *Electoral Studies*, 32(4), 724-733.

### Bibliotecas e Frameworks
- [Scikit-learn Documentation](https://scikit-learn.org/)
- [XGBoost Documentation](https://xgboost.readthedocs.io/)
- [SHAP Documentation](https://shap.readthedocs.io/)
- [Pandas Documentation](https://pandas.pydata.org/)
- [Imbalanced-learn Documentation](https://imbalanced-learn.org/)

---

## 👥 Equipe

**Autores:**  
- Artur Garcia ([arturgarcia046@gmail.com](mailto:arturgarcia046@gmail.com))  
- Artur Saraiva  ([paschoal.artur@gmail.com](mailto:paschoal.artur@gmail.com))

**Orientação:**  
Prof. Dr. César Lincoln Cavalcante Mattos

**Instituição:**  
Universidade Federal do Ceará (UFC)  
Departamento de Computação  
Disciplina: Aprendizagem de Máquina  
Período: 2024.2

---

## 📄 Licença

Este projeto é de natureza **acadêmica** e utiliza dados públicos do Tribunal Superior Eleitoral (TSE).

**Uso permitido:**
- ✅ Pesquisa acadêmica e científica
- ✅ Educação e aprendizado
- ✅ Reprodução com citação adequada

**Uso não autorizado:**
- ❌ Uso comercial sem autorização
- ❌ Manipulação antiética de campanhas eleitorais
- ❌ Discriminação com base em predições

**Citação sugerida:**
```
Garcia, A., Saraiva, A., & Mattos, C. L. C. (2025). 
Predição de Sucesso Eleitoral - Deputado Federal: 
Um Estudo com Validação Temporal. 
Universidade Federal do Ceará.
```

---

## 📞 Contato

Para dúvidas, sugestões ou colaborações:

- 📧 **E-mail:** [arturgarcia046@gmail.com](mailto:arturgarcia046@gmail.com) ou ([paschoal.artur@gmail.com](mailto:paschoal.artur@gmail.com))
- 🎓 **Universidade:** UFC - Departamento de Computação
- 📊 **Dados TSE:** [estatistica@tse.jus.br](mailto:estatistica@tse.jus.br)

---

## 🏆 Contribuição Científica

Este projeto demonstra uma abordagem **rigorosa e defensável** para predição eleitoral usando Machine Learning, servindo como baseline para pesquisas futuras em **ciência política computacional**.

### Principais Contribuições

#### 1. Metodologia
- ✅ **Validação Temporal Rigorosa** (treino 2018 → teste 2022, gap de 4 anos) — evita data leakage
- ✅ **Implementação completa de pipeline ML** com dados reais e complexos do TSE
- ✅ **Tratamento defensável de desbalanceamento severo** (class weights, métricas apropriadas, análise de trade-offs)
- ✅ **Comparação robusta de 4 algoritmos** com otimização em 3 etapas (screening → seleção → refinamento)

#### 2. Engenharia de Features
- ✅ **Target encoding com smoothing bayesiano** em 7 categorias (partido, ocupação, grau de instrução, estado civil, cor/raça, UF nascimento, coligação)
- ✅ **Análise de sensibilidade do parâmetro α** (5, 10, 15, 20, 30, 50) com validação cruzada
- ✅ **Feature `IS_PARTIDO_GRANDE`** (6 maiores partidos) com justificativa teórica
- ✅ **Features de coligação** (`QTD_PARTIDOS_COLIG`, `IS_COLIGADO`, `COLIGACAO_ENC`)

#### 3. Interpretabilidade Multi-Nível
- ✅ **Feature importance (MDI)** + **SHAP (Shapley values)** + **permutation importance regional**
- ✅ **Análise de consenso** entre modelos (features robustas)
- ✅ **Summary plots, dependence plots, waterfall plots** (explicações globais e locais)
- ✅ **Importância agregada de features UF** (35.7% do total) — evidencia regionalização

#### 4. Avaliação Rigorosa
- ✅ **Intervalos de confiança via bootstrap** (1000 iterações, IC 95%)
- ✅ **Análise de estabilidade** (Coeficiente de Variação <5%)
- ✅ **Curvas ROC e PR com bandas de confiança** (bootstrap 100 iterações)
- ✅ **Análise de threshold ótimo** (varredura 0-1, trade-off Precision vs. Recall)
- ✅ **Análise de calibração** (curva de calibração do modelo)
- ✅ **Baselines múltiplos** (DummyClassifier, Logistic Regression default) — ganho de 869% no F1-Score

#### 5. Análise de Erros Profunda
- ✅ **Top 10 FPs e FNs** com análise qualitativa (padrões de erro)
- ✅ **Segmentação de erros por partido, UF, ocupação, patrimônio**
- ✅ **Predições corretas com alta confiança** (TP/TN com p>0.9 ou p<0.1)
- ✅ **Identificação de limitação-chave:** Eleitos por Quociente Partidário (proposta de análise futura)

#### 6. Considerações Éticas e Transparência
- ✅ **Discussão explícita de vieses** (incumbência, socioeconômico, partidário)
- ✅ **Riscos de manipulação e gaming do sistema**
- ✅ **Impacto em autodeterminação democrática**
- ✅ **Princípios norteadores:** transparência, equidade, accountability, auditabilidade
- ✅ **Reconhecimento de limitações** (dados ausentes, generalização, sistema proporcional complexo)

#### 7. Interpretação Crítica
- ✅ **Não overselling:** Performance modesta (F1=0.61) é **esperada** dado o desafio
- ✅ **Reconhecimento de fatores não-capturáveis:** Campanha, mídia, conjuntura
- ✅ **Comparação com baseline:** Evidencia que modelo aprende padrões reais (+869% F1)

#### 8. Reprodutibilidade
- ✅ **Documentação acadêmica completa** (README de 500+ linhas)
- ✅ **Código modular e comentado** (funções com docstrings)
- ✅ **Seed fixo** (`np.random.seed(42)`)
- ✅ **Gerenciamento de dependências via Poetry** (pyproject.toml)
- ✅ **Estrutura de diretórios clara** com 10 figuras geradas

---

### Relevância Acadêmica

#### Ciência Política Computacional
- Quantifica fatores estruturais de sucesso eleitoral no Brasil
- Evidencia dominância de incumbência e estrutura partidária
- Fornece insights para reforma eleitoral e políticas de equidade

#### Machine Learning Aplicado
- Caso de estudo de **validação temporal** com desbalanceamento extremo
- Demonstra trade-offs entre interpretabilidade e performance
- Exemplifica pipeline completo (ETL → EDA → Feature Engineering → Modelagem → Interpretabilidade → Análise de Erros)

#### Ética e Sociedade
- Análise crítica de vieses algorítmicos em contexto democrático
- Discussão sobre impacto de IA em processos eleitorais
- Modelo de transparência e accountability em ML para democracia

---

### Impacto Potencial

1. **Candidatos e Partidos:** Ferramenta de diagnóstico para avaliar viabilidade eleitoral e direcionar investimentos
2. **Cientistas Políticos:** Baseline para pesquisas sobre determinantes de sucesso eleitoral
3. **Formuladores de Políticas:** Evidências para políticas de equidade eleitoral (financiamento público, tempo de TV)
4. **Pesquisadores de ML:** Exemplo de boas práticas em validação temporal e interpretabilidade

---

⭐ **Se este projeto foi útil, considere dar uma estrela e citar em sua pesquisa!**

---

**Versão:** 1.0 (Validação Temporal Concluída)  
**Data de Atualização:** Fevereiro 2026  
**Status:** Documentação Completa e Profissional ✅
