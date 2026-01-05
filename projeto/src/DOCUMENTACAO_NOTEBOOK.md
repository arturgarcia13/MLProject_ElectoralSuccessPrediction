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

## 4. Detalhamento das Células

### Célula 1: Setup e Importações
**Objetivo:** Carregar bibliotecas e configurar ambiente.

**Bibliotecas principais:**
- `pandas`, `numpy`: Manipulação de dados
- `sklearn`: Modelos e métricas
- `xgboost`: Modelo principal
- `shap`: Explicabilidade

**Configurações:**
- `random_state=42`: Reprodutibilidade
- `warnings.filterwarnings('ignore')`: Suprime avisos
- Estilo de gráficos: `seaborn-v0_8-whitegrid`

---

### Célula 2: Funções de Carregamento

#### `carregar_dados_basicos(ano)`
**Objetivo:** Carregar dados do TSE (candidatos, complementar, bens).

**Passos:**
1. Lê 3 CSVs (encoding latin1, separador `;`)
2. Filtra Deputado Federal (CD_CARGO = 6)
3. Faz merge por SQ_CANDIDATO
4. Agrega patrimônio (soma de bens por candidato)

**Retorno:** DataFrame com dados consolidados

#### `carregar_dados_financeiros(ano)`
**Objetivo:** Carregar dados de prestação de contas.

**Passos:**
1. Lê receitas e despesas
2. Converte valores (string → float)
3. Agrega por candidato (soma e contagem)

**Retorno:** Dois DataFrames (receitas_agg, despesas_agg)

---

### Célula 3: Carregamento de Dados
**Objetivo:** Carregar dados de 2018 (treino) e 2022 (teste).

**Operações:**
1. Carrega dados básicos de cada ano
2. Carrega dados financeiros de cada ano
3. Faz merge (candidatos + receitas + despesas)
4. Adiciona coluna ANO

---

### Célula 4: Preparação do Dataset

#### `preparar_dataset(df)`
**Objetivo:** Limpar dados e definir target.

**Tratamentos:**
1. Define target binário (ELEITO)
2. Filtra registros com resultado definido
3. Substitui #NULO e #NE por NaN
4. Converte VR_DESPESA_MAX_CAMPANHA
5. Preenche missings com valores padrão:
   - ST_REELEICAO → 'N'
   - Valores numéricos → 0 ou mediana
6. Remove registros com campos críticos ausentes

---

### Célula 5-6: EDA
**Objetivo:** Análise exploratória visual e numérica.

**Visualizações:**
1. Distribuição do target por ano
2. Boxplot de receitas por classe
3. Estatísticas financeiras por classe

**Métricas calculadas:**
- Ratio de desbalanceamento
- Média e mediana de receitas/despesas por classe
- Ratio de receita (eleitos vs não eleitos)

---

### Célula 7: Feature Engineering

#### `criar_features(df, partido_enc, ocupacao_enc, fit)`
**Objetivo:** Criar todas as features para modelagem.

**Parâmetro `fit`:**
- `True`: Calcula encodings (treino)
- `False`: Usa encodings existentes (teste)

**Features criadas:** Ver seção 5 (Feature Engineering)

---

### Célula 8: Preparação para Modelagem
**Objetivo:** Padronização e cálculo de pesos.

**Operações:**
1. StandardScaler (fit no treino, transform no teste)
2. Cálculo de class_weight (sklearn)
3. Cálculo de scale_pos_weight (XGBoost)

---

### Células 9-13: Treinamento dos Modelos
**Objetivo:** Treinar 4 modelos e coletar métricas.

**Modelos:**
1. Logistic Regression (baseline linear)
2. Random Forest (ensemble bagging)
3. Gradient Boosting (ensemble boosting)
4. XGBoost com GridSearch (estado da arte)

---

### Células 14-15: Avaliação
**Objetivo:** Comparar modelos e visualizar resultados.

**Visualizações:**
1. Tabela comparativa de métricas
2. Matriz de confusão (melhor modelo)
3. Curvas ROC (todos os modelos)

---

### Células 16-17: SHAP
**Objetivo:** Explicabilidade das predições.

**Análises:**
1. Summary plot (importância global)
2. Ranking de features

---

### Célula 18: Análise por UF
**Objetivo:** Avaliar performance regional.

**Métricas por UF:**
- Taxa real de eleitos
- Probabilidade média predita
- Diferença (calibração)

---

### Célula 19: Conclusões
**Objetivo:** Resumo final do projeto.

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

### 8.1 SHAP Values

**O que é SHAP?**
- SHapley Additive exPlanations
- Baseado em teoria dos jogos
- Mede contribuição marginal de cada feature

**Interpretação do Summary Plot:**
- Eixo X: Impacto na predição (SHAP value)
- Eixo Y: Features ordenadas por importância
- Cor: Valor da feature (vermelho=alto, azul=baixo)
- Posição horizontal: Direção do impacto

**Exemplo:**
```
IS_REELEICAO: pontos vermelhos à direita
→ Ser candidato à reeleição (valor alto) aumenta probabilidade de eleição
```

### 8.2 Features Mais Importantes

**Esperado (hipótese):**
1. LOG_RECEITAS (dinheiro = visibilidade)
2. IS_REELEICAO (incumbency advantage)
3. PARTIDO_ENC (força partidária)
4. LOG_DESPESAS (investimento em campanha)

**Por que financeiro é importante?**
- Campanhas caras = mais propaganda
- Candidatos com apoio = mais doações
- Correlação (não causalidade) com sucesso

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
