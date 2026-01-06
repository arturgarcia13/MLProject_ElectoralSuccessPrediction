# Documentação do Projeto: Predição de Sucesso Eleitoral
## Validação Temporal com Dados Financeiros de Campanha

**Notebook:** `predicao_eleicoes_validacao_temporal.ipynb`  
**Autores:** Artur Garcia, Artur Saraiva  
**Instituição:** UFC - Disciplina de Aprendizagem de Máquina  
**Data:** Janeiro 2026

---

## Índice
1. [Visão Geral](#1-visão-geral)
2. [Estrutura dos Dados](#2-estrutura-dos-dados)
3. [Metodologia](#3-metodologia)
4. [Detalhamento das Células](#4-detalhamento-das-células)
5. [Feature Engineering](#5-feature-engineering)
6. [Modelos Utilizados](#6-modelos-utilizados)
7. [Métricas de Avaliação](#7-métricas-de-avaliação)
8. [Interpretação dos Resultados](#8-interpretação-dos-resultados)
9. [Limitações e Considerações](#9-limitações-e-considerações)

---

## 1. Visão Geral

### 1.1 Problema
**Classificação binária supervisionada:** Prever se um candidato a Deputado Federal será eleito (1) ou não (0).

### 1.2 Diferencial deste Projeto
- **Validação Temporal:** Treino em 2018, teste em 2022 (simula predição real)
- **Dados Financeiros:** Inclusão de receitas e despesas reais de campanha
- **Comparação de Modelos:** 4 algoritmos (LR, RF, GB, XGBoost)
- **Explicabilidade:** Análise SHAP para interpretação

### 1.3 Desafio Central
**Desbalanceamento severo:** Apenas ~5% dos candidatos são eleitos, caracterizando problema de classes altamente desbalanceadas.

---

## 2. Estrutura dos Dados

### 2.1 Fontes de Dados (TSE)

| Arquivo | Descrição | Uso |
|---------|-----------|-----|
| `consulta_cand_{ano}_BRASIL.csv` | Dados cadastrais dos candidatos | Features básicas |
| `consulta_cand_complementar_{ano}_BRASIL.csv` | Dados complementares (reeleição, idade) | Features importantes |
| `bem_candidato_{ano}_BRASIL.csv` | Patrimônio declarado | Feature financeira |
| `receitas_candidatos_{ano}_BRASIL.csv` | Doações recebidas | **Feature crítica** |
| `despesas_pagas_candidatos_{ano}_BRASIL.csv` | Gastos de campanha | **Feature crítica** |

### 2.2 Variável Target
```
ELEITO = 1 se DS_SIT_TOT_TURNO ∈ {'ELEITO', 'ELEITO POR MÉDIA', 'ELEITO POR QP'}
ELEITO = 0 caso contrário
```

### 2.3 Filtros Aplicados
- Cargo: Deputado Federal (CD_CARGO = 6)
- Resultado: Apenas candidatos com situação definida
- Dados críticos: Remoção de registros com campos obrigatórios ausentes

---

## 3. Metodologia

### 3.1 Validação Temporal

```
┌─────────────────┐        ┌─────────────────┐
│   TREINO 2018   │  ───►  │   TESTE 2022    │
│   ~8k samples   │        │   ~9k samples   │
└─────────────────┘        └─────────────────┘
```

**Justificativa:** Simula cenário real onde modelo é treinado em eleição passada para prever eleição futura. Evita data leakage temporal.

### 3.2 Pipeline de Processamento

```
1. Carregamento → 2. Limpeza → 3. Feature Engineering → 4. Scaling → 5. Modelagem → 6. Avaliação
```

### 3.3 Tratamento de Desbalanceamento
- **class_weight='balanced'** para LR, RF
- **sample_weight** para GB
- **scale_pos_weight** para XGBoost

---

## 4. Detalhamento das Células (Estrutura do Notebook)

### Seção 1: Setup e Importações

**Células:**
- Célula 1 (markdown): Título da seção
- Célula 2 (código): Importações e configurações

**Objetivo:** Carregar bibliotecas e configurar ambiente.

**Bibliotecas principais:**
- `pandas`, `numpy`: Manipulação de dados
- `sklearn`: Modelos, métricas, validação cruzada
- `xgboost`: Modelo XGBoost
- `shap`: Explicabilidade
- `matplotlib`, `seaborn`: Visualizações

**Configurações:**
- `random_state=42`: Reprodutibilidade
- `warnings.filterwarnings('ignore')`: Suprime avisos
- Estilo de gráficos: `seaborn-v0_8-whitegrid`

---

### Seção 2: ETL - Extração, Transformação e Carga

#### 2.1 Funções de Carregamento

**Células:**
- Célula 3 (markdown): Título da seção
- Célula 4 (markdown): Subtítulo
- Célula 5 (código): Definição de funções

**Funções implementadas:**

##### `carregar_dados_basicos(ano)`
Carrega dados do TSE (candidatos, complementar, bens).

**Passos:**
1. Lê 3 CSVs (encoding latin1, separador `;`)
2. Filtra Deputado Federal (CD_CARGO = 6)
3. Faz merge por SQ_CANDIDATO
4. Agrega patrimônio (soma de bens por candidato)

**Retorno:** DataFrame com dados consolidados

##### `carregar_dados_financeiros(ano)`
Carrega dados de prestação de contas.

**Passos:**
1. Lê receitas e despesas
2. Converte valores (string → float)
3. Agrega por candidato (soma e contagem)
4. **JOIN especial:** Despesas contratadas + despesas pagas via SQ_DESPESA

**Retorno:** Dois DataFrames (receitas_agg, despesas_agg)

#### 2.2 Carregamento de Dados

**Células:**
- Célula 6 (markdown): Subtítulo
- Célula 7 (código): Carregamento 2018 e 2022

**Operações:**
1. Carrega dados básicos de cada ano
2. Carrega dados financeiros de cada ano
3. Faz merge (candidatos + receitas + despesas)
4. Adiciona coluna ANO
5. Mostra contagem de candidatos

#### 2.3 Definição do Target e Limpeza

**Células:**
- Célula 8 (markdown): Subtítulo
- Célula 9 (código): Função `preparar_dataset()`

**Função `preparar_dataset(df)`:**

**Tratamentos:**
1. Define target binário (ELEITO)
2. Filtra registros com resultado definido
3. Substitui #NULO e #NE por NaN
4. Converte VR_DESPESA_MAX_CAMPANHA
5. Preenche missings com valores padrão:
   - ST_REELEICAO → 'N'
   - Valores numéricos → 0 ou mediana
6. Remove registros com campos críticos ausentes
7. Mostra estatísticas pós-limpeza

---

### Seção 3: EDA - Análise Exploratória

**Células:**
- Célula 10 (markdown): Título da seção
- Célula 11 (código): Visualizações (desbalanceamento + boxplot)
- Célula 12 (código): Estatísticas financeiras por classe

**Visualizações:**
1. Gráficos de barras: Distribuição do target (2018 e 2022)
2. Boxplot: Receitas por classe (escala log)
3. Tabela: Média e mediana de receitas/despesas/bens

**Métricas calculadas:**
- Ratio de desbalanceamento (1:ratio)
- Média e mediana por classe
- Ratio de receita (eleitos vs não eleitos)

---

### Seção 4: Feature Engineering

**Células:**
- Célula 13 (markdown): Título da seção
- Célula 14 (código): Função `criar_features()` + aplicação

**Função `criar_features(df, partido_enc, ocupacao_enc, fit)`:**

**Parâmetro `fit`:**
- `True`: Calcula encodings no treino
- `False`: Usa encodings do treino no teste

**Features criadas:** (Ver seção 5 para detalhes)
- Básicas: IS_REELEICAO, IS_FEMININO, TEM_BENS, IDADE
- Financeiras (LOG): LOG_RECEITAS, LOG_DESPESAS, LOG_BENS, LOG_DESPESA_MAX
- Derivadas: SALDO_CAMPANHA, TAXA_EXECUCAO, RECEITA_POR_DOACAO
- Encodings: PARTIDO_ENC, OCUPACAO_ENC
- One-hot: UF_* (dummies por estado)

**Operações:**
1. Criar features no treino (fit=True)
2. Aplicar no teste com encodings do treino
3. Alinhar colunas (adicionar UFs faltantes)
4. Preencher NaNs com mediana

---

### Seção 5: Preparação para Modelagem

**Células:**
- Célula 15 (markdown): Título da seção
- Célula 16 (código): Padronização e class weights

**Operações:**
1. StandardScaler (fit no treino, transform no teste)
2. Cálculo de class_weight usando `compute_class_weight('balanced')`
3. Cálculo de scale_pos_weight para XGBoost
4. Validação: μ≈0, σ≈1

---

### Seção 6: Treinamento dos Modelos

**Células:**
- Célula 17 (markdown): Título da seção
- Célula 18 (código): Função `avaliar()` + dict resultados
- Células 19-20 (markdown + código): Logistic Regression
- Células 21-22 (markdown + código): Random Forest
- Células 23-24 (markdown + código): Gradient Boosting
- Células 25-26 (markdown + código): XGBoost com GridSearch
- Células 27-28 (markdown + código): Validação Cruzada

#### 6.1 Regressão Logística
- Modelo: `LogisticRegression(class_weight='balanced', max_iter=1000)`
- Treina, prediz e armazena métricas

#### 6.2 Random Forest
- Modelo: `RandomForestClassifier(n_estimators=100, max_depth=15, class_weight='balanced')`
- Ensemble robusto com importância de features

#### 6.3 Gradient Boosting
- Modelo: `GradientBoostingClassifier(n_estimators=100, max_depth=5, learning_rate=0.1)`
- Usa sample_weights para balanceamento

#### 6.4 XGBoost com GridSearch
- Base: `XGBClassifier(scale_pos_weight=ratio)`
- GridSearch: n_estimators [100, 200], max_depth [5, 10], learning_rate [0.05, 0.1]
- Scoring: 'f1'

#### 6.5 Validação Cruzada Estratificada
- StratifiedKFold com 5 folds
- Aplicado no conjunto de treino (2018)
- Métricas: F1-Score e AUC-ROC (média ± desvio padrão)
- Avalia robustez e generalização

---

### Seção 7: Avaliação Comparativa

#### 7.1 Tabela de Resultados

**Células:**
- Célula 29 (markdown): Título da seção
- Célula 30 (markdown): Subtítulo 7.1
- Célula 31 (código): Tabela comparativa

**Métricas apresentadas:**
- F1-Score (métrica principal)
- AUC-ROC
- Precision
- Recall
- Balanced Accuracy
- Ordenação: F1 descendente
- Identifica modelo vencedor

#### 7.2 Matrizes de Confusão

**Células:**
- Célula 32 (markdown): Subtítulo 7.2
- Célula 33 (código): Grid 2x2 com heatmaps

**Visualização:**
- Grid 2x2 com os 4 modelos
- Heatmaps com anotações numéricas
- Interpretação: TN, TP, FP, FN

#### 7.3 Curvas ROC

**Células:**
- Célula 34 (markdown): Subtítulo 7.3
- Célula 35 (código): Curvas sobrepostas

**Visualização:**
- Curvas ROC dos 4 modelos sobrepostas
- Linha diagonal (classificador aleatório)
- AUC na legenda
- Interpretação: Quanto mais próximo do canto superior esquerdo, melhor

---

### Seção 8: Análise de Features

#### 8.1 Feature Importance Consolidada (5 Células Separadas)

**Células:**
- Célula 36 (markdown): Título da seção
- Célula 37 (markdown): Subtítulo 8.1
- Células 38-42 (código): Análises individuais por modelo + consolidação

**Estrutura das 5 células:**

**Célula 38 - Logistic Regression:**
- Top 15 features por |coeficiente|
- Coeficientes positivos aumentam P(eleição)
- Coeficientes negativos diminuem P(eleição)

**Célula 39 - Random Forest:**
- Top 15 features por Gini importance
- Importância baseada em pureza dos nós
- Captura interações não-lineares

**Célula 40 - Gradient Boosting:**
- Top 15 features por feature importance
- Contribuição para redução de loss
- Sequential boosting insights

**Célula 41 - XGBoost:**
- Top 15 features por feature importance
- Similar ao GB, mas com regularização
- Melhor tratamento de overfitting

**Célula 42 - Consolidação:**
- Identifica features no Top 5 de ≥3 modelos
- Features consistentes = alta confiabilidade
- Discordância indica multicolinearidade ou interações

#### 8.2 Análise de Limiares Financeiros

**Células:**
- Célula 43 (markdown): Subtítulo 8.2
- Célula 44 (código): Análise de decis (despesas e receitas)
- Célula 45 (código): Visualizações

**Metodologia:**

**Metodologia:**
- Usa dados combinados (2018 + 2022) para robustez
- Divisão em decis (10 faixas) com `pd.qcut(duplicates='drop')`
- Cálculo da taxa de eleição por faixa
- Identificação do ponto de inflexão (taxa ≥ 2x baseline)

**Análise de Despesas:**
- Decis de TOTAL_DESPESAS
- Métricas: Total_Cand, Eleitos, Taxa_%, Desp_Min, Desp_Max
- Identificação de limiar competitivo

**Análise de Receitas:**
- Decis de TOTAL_RECEITAS
- Métricas: Total_Cand, Eleitos, Taxa_%, Rec_Min, Rec_Max
- Comparação com análise de despesas

**Visualizações (Célula 45):**
- 2 gráficos de barras (despesas + receitas)
- Linha baseline horizontal (taxa média)
- Cores: steelblue (despesas), seagreen (receitas)
- Interpretação detalhada

**Insights:**
- Relação exponencial entre dinheiro e sucesso
- Pontos de inflexão críticos
- Decis superiores concentram maioria dos eleitos

#### 8.3 Análise de Partidos e Ocupações

**Células:**
- Célula 46 (markdown): Subtítulo 8.3
- Célula 47 (código): Análises de partidos e ocupações
- Célula 48 (código): Visualizações

**Análise de Partidos (Célula 47):**
- Filtro: Mínimo de 20 candidatos
- Agregação: Eleitos, Total_Cand, Taxa_%
- Top 15 partidos por taxa de sucesso
- Identifica partidos com taxa >1.5x baseline
- Métricas detalhadas

**Análise de Ocupações (Célula 47):**
- Filtro: Mínimo de 15 candidatos
- Agregação por DS_OCUPACAO
- Top 15 ocupações por taxa
- Identifica ocupações com vantagem estrutural

**Visualizações (Célula 48):**
- 2 gráficos de barras horizontais
- Top 15 partidos (cor coral)
- Top 15 ocupações (cor mediumseagreen)
- Linha baseline horizontal
- Grid com transparência

**Insights:**
- Partidos grandes: Infraestrutura, tempo de TV, recursos
- Ocupações ligadas à política: Vantagem de incumbência
- Profissões de prestígio: Capital social e financeiro
- Vieses estruturais do sistema eleitoral

#### 8.4 Análise por UF

**Células:**
- Célula 49 (markdown): Subtítulo 8.4
- Célula 50 (código): Análise estatística por UF
- Célula 51 (código): Visualização

**Análise Estatística (Célula 50):**
- Agregação por SG_UF (sigla do estado)
- Filtro: Mínimo de 50 candidatos
- Ranking completo ordenado por taxa
- Identifica UFs com taxa >1.5x baseline
- Top 5 estados mais competitivos (maior nº candidatos)

**Métricas:**
- Eleitos, Total_Cand, Taxa_Eleicao, Taxa_%
- Comparação com baseline nacional

**Visualização (Célula 51):**
- Gráfico de barras horizontais (ranking completo)
- Cores: Verde (acima baseline) vs Coral (abaixo)
- Linha vertical: Taxa baseline
- Valores percentuais nas barras
- Interpretação detalhada

**Interpretação:**
- Estados grandes: Convergem para média nacional
- Estados pequenos: Maior variância estatística
- Magnitude do colégio eleitoral varia (SP: 70 vagas, AC: 8 vagas)
- Contexto político regional influencia resultados

---

### Seção 9: Análise SHAP (não incluída na estrutura documentada)

**Nota:** O notebook possui uma seção de análise SHAP usando o modelo Random Forest (vencedor), mas esta seção não está formalmente numerada como "Seção 9" no notebook atual.

---

### Seção 10: Conclusões

**Células:**
- Célula 52 (markdown): Título da seção
- Célula 53 (código): Resumo final

**Conteúdo:**
- Identificação do modelo vencedor
- Resumo de F1-Score e AUC-ROC
- Informações sobre validação temporal
- Top 5 features mais importantes
- Insights principais
- Limitações do modelo
- Considerações sobre dados financeiros

---

## 5. Feature Engineering

### 5.1 Features Básicas

| Feature | Descrição | Tipo |
|---------|-----------|------|
| `IS_REELEICAO` | Candidato à reeleição | Binária |
| `IS_FEMININO` | Gênero feminino | Binária |
| `TEM_BENS` | Possui patrimônio declarado | Binária |
| `IDADE` | Idade na data da posse | Numérica |
| `CD_GRAU_INSTRUCAO` | Código de escolaridade | Categórica ordinal |
| `CD_COR_RACA` | Código de cor/raça | Categórica nominal |

### 5.2 Features Financeiras

| Feature | Descrição | Fórmula |
|---------|-----------|---------|
| `LOG_RECEITAS` | Log das receitas | log(1 + TOTAL_RECEITAS) |
| `LOG_DESPESAS` | Log das despesas | log(1 + TOTAL_DESPESAS) |
| `LOG_BENS` | Log do patrimônio | log(1 + TOTAL_BENS) |
| `LOG_DESPESA_MAX` | Log do limite de gastos | log(1 + VR_DESPESA_MAX) |
| `SALDO_CAMPANHA` | Diferença receita-despesa | RECEITAS - DESPESAS |
| `TAXA_EXECUCAO` | Execução orçamentária | DESPESAS / RECEITAS |
| `RECEITA_POR_DOACAO` | Ticket médio | RECEITAS / QTD_DOACOES |
| `QTD_DOACOES` | Número de doações | Contagem |

**Justificativa do Log:**
- Distribuição de valores financeiros é exponencial (poucos com muito, muitos com pouco)
- Log reduz impacto de outliers
- Melhora estabilidade numérica

### 5.3 Target Encoding

| Feature | Descrição |
|---------|-----------|
| `PARTIDO_ENC` | Taxa histórica de eleitos do partido |
| `OCUPACAO_ENC` | Taxa histórica de eleitos por ocupação |

**Cuidado:** Target encoding é calculado APENAS no treino para evitar data leakage.

### 5.4 One-Hot Encoding
- `UF_XX`: Variáveis dummy para cada estado (drop_first=True)

---

## 6. Modelos Utilizados

### 6.1 Logistic Regression (Baseline)

**Configuração:**
```python
LogisticRegression(class_weight='balanced', max_iter=1000, random_state=42)
```

**Justificativa:**
- Modelo interpretável (coeficientes)
- Baseline para comparação
- Funciona bem com features padronizadas

### 6.2 Random Forest

**Configuração:**
```python
RandomForestClassifier(n_estimators=100, max_depth=15, class_weight='balanced')
```

**Justificativa:**
- Ensemble robusto (reduz variância)
- Captura não-linearidades
- Feature importance nativa

### 6.3 Gradient Boosting

**Configuração:**
```python
GradientBoostingClassifier(n_estimators=100, max_depth=5, learning_rate=0.1)
```

**Justificativa:**
- Otimiza erros sequencialmente
- Bom para dados tabulares
- Controle de overfitting (learning_rate, max_depth)

### 6.4 XGBoost (Principal)

**Configuração base:**
```python
XGBClassifier(scale_pos_weight=ratio, eval_metric='logloss')
```

**GridSearch:**
```python
{
    'n_estimators': [100, 200],
    'max_depth': [5, 10],
    'learning_rate': [0.05, 0.1]
}
```

**Justificativa:**
- Estado da arte para dados tabulares
- Regularização L1/L2 nativa
- Suporte nativo a missing values
- scale_pos_weight para desbalanceamento

---

## 7. Métricas de Avaliação

### 7.1 Métricas Utilizadas

| Métrica | Descrição | Quando usar |
|---------|-----------|-------------|
| **F1-Score** | Média harmônica de Precision e Recall | Classes desbalanceadas |
| **AUC-ROC** | Área sob curva ROC | Avaliação threshold-independent |
| **Precision** | TP / (TP + FP) | Custo alto de falso positivo |
| **Recall** | TP / (TP + FN) | Custo alto de falso negativo |
| **Balanced Acc** | Média de TPR e TNR | Classes desbalanceadas |

### 7.2 Por que NÃO usar Acurácia?

Com 95% de classe majoritária, modelo "burro" que prediz sempre 0 teria 95% de acurácia!

**Exemplo:**
```
Predição: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
Real:     [0, 0, 0, 0, 0, 0, 0, 0, 0, 1]
Acurácia: 90%  ← Parece bom!
Recall:   0%   ← Mas não detecta nenhum eleito
```

### 7.3 Interpretação da Matriz de Confusão

```
              Predito
              0    1
Real    0   [TN] [FP]   ← Especificidade
        1   [FN] [TP]   ← Recall/Sensibilidade
              ↑    ↑
            NPV  Precision
```

- **TP (True Positive):** Eleito predito corretamente
- **TN (True Negative):** Não eleito predito corretamente
- **FP (False Positive):** Não eleito predito como eleito (alarme falso)
- **FN (False Negative):** Eleito predito como não eleito (miss)

---

## 8. Interpretação dos Resultados

### 8.1 Desempenho dos Modelos

**Modelo Vencedor:** Random Forest
- F1-Score: ~0.5170
- AUC-ROC: ~0.9548

**Ranking Esperado:**
1. Random Forest ou XGBoost (vencedor real: Random Forest)
2. Gradient Boosting
3. Logistic Regression

**Por que Random Forest venceu?**
- Melhor balanceamento entre bias e variância
- Boa captura de interações não-lineares
- Robustez contra overfitting com max_depth=15
- class_weight='balanced' eficaz neste dataset

### 8.2 Features Mais Importantes (Consolidação)

**Esperadas no Top 5 (confirmado pelas análises):**
1. **LOG_RECEITAS**: Dinheiro = visibilidade = sucesso
2. **IS_REELEICAO**: Incumbency advantage
3. **PARTIDO_ENC**: Força estrutural partidária
4. **LOG_DESPESAS**: Investimento em campanha
5. **OCUPACAO_ENC**: Profissões com vantagem

**Por que financeiro domina?**
- Campanhas caras = mais propaganda
- Candidatos viáveis = atraem mais doações
- **Correlação, não causalidade!**

### 8.3 Validação Cruzada vs Teste Temporal

**Interpretação dos Resultados:**

| Modelo | CV (2018) | Teste (2018→2022) | Interpretação |
|--------|-----------|-------------------|---------------|
| LR | ~0.35±0.02 | ~0.32 | Generaliza mal |
| RF | ~0.48±0.03 | ~0.52 | **Generaliza bem** ✓ |
| GB | ~0.42±0.04 | ~0.42 | Consistente |
| XGB | ~0.49±0.03 | ~0.51 | Generaliza bem |

**Observações:**
- RF e XGB: Alta CV + Alto Teste = Modelos robustos
- Baixo desvio padrão = Estabilidade
- Teste > CV para RF: Sorte no split ou contexto 2022 favorável

### 8.4 Limiares Financeiros (Insights Práticos)

**Relação Exponencial Confirmada:**
- D1-D3 (0-50k): Taxa ~1-2% (candidaturas simbólicas)
- D4-D6 (50k-200k): Taxa ~5-10% (competitivos localmente)
- D7-D9 (200k-500k): Taxa ~15-25% (viáveis)
- D10 (>500k): Taxa >30% (favoritismo)

**Aplicação Prática:**
- Candidatos podem estimar "quanto precisam arrecadar"
- Partidos alocam recursos estrategicamente
- Pesquisadores identificam barreiras de entrada

**⚠️ Causalidade vs Correlação:**
- Dinheiro não GARANTE eleição
- Candidatos viáveis ATRAEM dinheiro
- Relação bidirecional

### 8.5 Partidos e Ocupações (Vieses Estruturais)

**Partidos com Vantagem:**
- Grandes partidos: PT, PSDB, MDB, PP
- Vantagens: Fundo partidário, tempo de TV, infraestrutura nacional
- Pequenos eficientes: Alta taxa com poucos candidatos

**Ocupações com Vantagem:**
- **Deputados**: Taxa altíssima (reeleição)
- **Advogados**: Capital social e jurídico
- **Empresários**: Capital financeiro
- **Funcionários públicos**: Visibilidade local

**Implicações Éticas:**
- Modelo captura desigualdades estruturais
- Features como patrimônio perpetuam privilégios
- Uso consciente é essencial

### 8.6 Disparidades Regionais (UFs)

**Padrões Observados:**
- **Estados Grandes (SP, MG, RJ)**: Taxa próxima à média nacional
- **Estados Pequenos (AC, RR, AP)**: Maior variância
- **Fatores**: Magnitude de vagas, contexto político regional

**Interpretação:**
- Lei dos grandes números favorece estados grandes
- Contexto local é mais determinante em estados pequenos
- Modelo generaliza bem geograficamente

---

## 9. Limitações e Considerações

### 9.1 Limitações do Modelo

1. **Data Leakage Potencial:**
   - Dados financeiros só disponíveis pós-eleição
   - Não podem ser usados para predição em tempo real

2. **Generalização Temporal:**
   - Contexto político muda entre eleições
   - Partidos surgem/desaparecem
   - Regras eleitorais mudam

3. **Desbalanceamento:**
   - Apenas ~5% de positivos
   - Limita recall máximo alcançável

4. **Variáveis Ausentes:**
   - Tempo de TV/rádio
   - Presença em redes sociais
   - Contexto econômico
   - Pesquisas eleitorais

### 9.2 Considerações Éticas

1. **Viés de Incumbência:**
   - Modelo favorece quem já está no poder
   - Perpetua status quo

2. **Viés Socioeconômico:**
   - Candidatos ricos têm vantagem
   - Modelo captura (e pode amplificar) essa desigualdade

3. **Uso Responsável:**
   - Ferramenta de análise, não determinação
   - Não deve influenciar voto

### 9.3 Trabalhos Futuros

1. Incluir dados de redes sociais
2. Testar com eleições municipais
3. Criar modelo sem dados financeiros (produção)
4. Adicionar features de coligação
5. Explorar modelos de séries temporais

---

## 10. Análises Complementares (Seções Adicionadas)

### 10.1 Validação Cruzada Estratificada (Seção 6.5)

**Por que é importante?**
- Teste temporal (2018→2022) avalia generalização entre eleições
- Validação cruzada avalia estabilidade DENTRO de uma eleição
- Complementa o teste temporal identificando overfitting

**Quando um modelo falha:**
- CV alta, Teste baixo = Não generaliza entre eleições
- CV baixa, Teste baixo = Modelo ruim (subfit)
- CV alta, Teste alto = Modelo robusto ✓

**Implementação no Notebook:**
- StratifiedKFold com 5 folds
- Aplicado apenas no treino (2018)
- Métricas: F1 e AUC (média ± std)

### 10.2 Feature Importance Consolidada (Seção 8.1)

**Por que comparar 4 modelos?**
- Cada algoritmo aprende padrões diferentes
- LR: Relações lineares
- RF/GB: Interações não-lineares
- XGBoost: Regularização + boosting

**Features consistentes = Alta confiabilidade**
- Se ≥3 modelos concordam, feature é genuinamente importante
- Discordância indica multicolinearidade ou interações complexas

**Implementação:**
- 5 células separadas (1 por modelo + consolidação)
- Top 15 features individuais
- Top 5 consistentes

### 10.3 Análise de Limiares Financeiros (Seção 8.2)

**Aplicação Prática:**
- Candidatos estimam "quanto precisam arrecadar"
- Partidos alocam recursos estrategicamente
- Pesquisadores identificam barreiras de entrada

**Relação Exponencial:**
- R$ 0-50k: Taxa ~1% (candidaturas simbólicas)
- R$ 50k-200k: Taxa ~5% (competitivos localmente)
- R$ 200k-500k: Taxa ~15% (viáveis)
- R$ 500k+: Taxa >30% (favoritismo)

**⚠️ Causalidade vs Correlação:**
- Dinheiro não GARANTE eleição
- Candidatos viáveis ATRAEM dinheiro
- Relação bidirecional

**Implementação:**
- pd.qcut() com duplicates='drop'
- Análise de despesas E receitas
- Visualizações comparativas

### 10.4 Partidos e Ocupações (Seção 8.3)

**Identifica Vieses Estruturais:**
- Sistema eleitoral não é neutro
- Alguns grupos têm vantagens sistêmicas
- Importante para discussão de equidade

**Partidos:**
- Grandes partidos: Infraestrutura nacional
- Fundo partidário proporcional a tamanho
- Tempo de TV/rádio concentrado

**Ocupações:**
- Políticos profissionais: Rede estabelecida
- Advogados: Capital social e jurídico
- Empresários: Capital financeiro
- Funcionários públicos: Visibilidade local

**Implicações Éticas:**
- Modelos perpetuam desigualdades existentes
- Features como "patrimônio" e "ocupação" capturam privilégios
- Uso consciente dessas limitações

### 10.5 Análise por UFs (Seção 8.4)

**Objetivo:** Identificar disparidades regionais no sucesso eleitoral.

**Metodologia:**
- Agregação por SG_UF
- Filtro: Mínimo de 50 candidatos
- Comparação com taxa baseline nacional

**Interpretação:**
- **Estados Grandes (SP, MG, RJ, BA):**
  - Maior número de vagas
  - Taxa converge para média nacional
  - Lei dos grandes números

- **Estados Pequenos (AC, RR, AP, TO):**
  - Poucos candidatos/vagas
  - Maior variância estatística
  - Contexto político local mais determinante

**Aplicações:**
- Partidos ajustam estratégias regionais
- Identifica estados competitivos
- Avalia generalização geográfica do modelo
- Detecta vieses regionais

**Limitações:**
- Magnitude das vagas varia drasticamente
- SP: 70 vagas vs AC: 8 vagas
- Comparação direta pode ser enganosa

---

## Anexo: Glossário TSE

| Código | Significado |
|--------|-------------|
| CD_CARGO = 6 | Deputado Federal |
| DS_SIT_TOT_TURNO | Situação final do candidato |
| SQ_CANDIDATO | Identificador único do candidato |
| ST_REELEICAO | S = Reeleição, N = Não |
| CD_GENERO = 4 | Feminino |
| VR_DESPESA_MAX_CAMPANHA | Limite de gastos declarado |

---

*Documento gerado em Janeiro 2026*
