# 🗳️ Predição de Sucesso Eleitoral - Deputado Federal

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![Status](https://img.shields.io/badge/Status-Finalizado-success.svg)]()

Projeto de Machine Learning para predição de sucesso eleitoral de candidatos a Deputado Federal utilizando **validação temporal** com dados reais do TSE (Treino: Eleições 2018 → Teste: Eleições 2022).

---

## 📑 Descrição

Este projeto implementa e compara **4 modelos de classificação supervisionada** para prever se um candidato será eleito ou não, enfrentando o desafio de **desbalanceamento severo de classes** (apenas ~10% de eleitos) e utilizando **validação temporal rigorosa** (treino em 2018, teste em 2022 - 4 anos de gap).

### Modelos Implementados
- 🔷 **Regressão Logística** (baseline interpretável)
- 🌲 **Random Forest** (ensemble robusto)
- ⚡ **Gradient Boosting** (boosting tradicional)
- 🚀 **XGBoost** (estado da arte)

### Estratégia de Otimização em 3 Etapas
1. **Screening** - GridSearch reduzido em todos os modelos
2. **Seleção** - Top 2 finalistas por F1-Score
3. **Otimização Final** - GridSearch/RandomizedSearch completo nos finalistas

### Métricas Avaliadas
- F1-Score (média harmônica)
- AUC-ROC (discriminação)
- AUC-PR (curva Precision-Recall)
- Precision & Recall
- Balanced Accuracy
- Matriz de Confusão

---

## 🎯 Objetivo

Desenvolver um modelo defensável academicamente que:
1. Utilize apenas dados declaratórios pré-eleição (TSE)
2. Trate adequadamente o desbalanceamento de classes
3. Seja interpretável e auditável
4. Forneça insights sobre fatores preditivos

---

## 📊 Dataset

### Fonte
- **Tribunal Superior Eleitoral (TSE)** - Portal de Dados Abertos
- **Eleições 2018** (Treino) + **Eleições 2022** (Teste)
- **Cargo:** Deputado Federal (CD_CARGO = 6)

### Arquivos Necessários
```
projeto/data/
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
- **Pessoais:** Idade, Gênero, Grau de Instrução, Estado Civil, Cor/Raça
- **Políticos:** Partido, Reeleição, UF, Coligação (QTD_PARTIDOS, IS_COLIGADO)
- **Financeiros:** Patrimônio declarado (LOG_BENS), Teto de despesas (LOG_DESPESAS)
- **Profissionais:** Ocupação (com target encoding)
- **Engenharia de Features:** Target encoding (partido, ocupação), one-hot encoding (UF)

---

## 🚀 Como Executar

### 1. Pré-requisitos

```bash
Python 3.8+
Jupyter Notebook
```

### 2. Instalar Dependências

```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost shap
```

Ou usando o pyproject.toml:

```bash
pip install .
```

### 3. Executar o Notebook

#### Opção A: VS Code
1. Abra `projeto/src/predicao_eleicoes_validacao_temporal.ipynb` no VS Code
2. Selecione o kernel Python
3. Execute "Run All Cells" (Ctrl+Shift+Alt+Enter)

#### Opção B: Jupyter Notebook
```bash
cd projeto/src
jupyter notebook predicao_eleicoes_validacao_temporal.ipynb
```

#### Opção C: Jupyter Lab
```bash
cd projeto/src
jupyter lab predicao_eleicoes_validacao_temporal.ipynb
```

### 4. Tempo de Execução
- **Carga de dados (2018 + 2022):** ~1 minuto
- **EDA e Engenharia de Features:** ~2 minutos
- **Screening (4 modelos):** ~5 minutos
- **Otimização Completa (Top 2):** ~10-15 minutos
- **Análise SHAP e Visualizações:** ~3 minutos
- **Total estimado:** ~20-25 minutos

---

## 📁 Estrutura do Projeto

```
AprendizagemDeMaquina/
│
├── pyproject.toml                   # Gerenciamento de dependências
│
└── projeto/
    ├── src/
    │   ├── predicao_eleicoes_validacao_temporal.ipynb  # 🎯 NOTEBOOK PRINCIPAL
    │   └── projeto_ml_depfed.ipynb                      # Versão anterior (2022 apenas)
    │
    ├── doc/
    │   └── README.md                # Este arquivo
    │
    └── data/                        # Dados do TSE
        ├── consulta_cand_2018/
        ├── consulta_cand_complementar_2018/
        ├── bem_candidato_2018/
        ├── consulta_cand_2022/
        ├── consulta_cand_complementar_2022/
        └── bem_candidato_2022/
```

---

## 📈 Resultados Obtidos

### Metodologia de Validação
- **Treino:** Eleições 2018 (N ≈ 3.000 candidatos)
- **Teste:** Eleições 2022 (N ≈ 3.000 candidatos)
- **Gap temporal:** 4 anos (validação prospectiva real)

### Modelo Vencedor (após otimização em 3 etapas)

Os resultados variam conforme a execução do RandomizedSearchCV, mas tipicamente:

| Métrica          | Valor Típico   |
|------------------|----------------|
| **F1-Score**     | 0.23 - 0.28    |
| **AUC-ROC**      | 0.58 - 0.65    |
| **AUC-PR**       | 0.18 - 0.25    |
| **Precision**    | 0.18 - 0.25    |
| **Recall**       | 0.30 - 0.40    |
| **Balanced Acc** | 0.62 - 0.68    |

> **Nota:** A performance modesta reflete o desafio real do problema (desbalanceamento 1:9 + validação temporal rigorosa)

### Fatores Mais Preditivos (via SHAP)
1. 🏛️ **Reeleição** (IS_REELEICAO) - Impacto massivo (+0.4 SHAP value)
2. 🎯 **Partido** (PARTIDO_TAXA_ELEICAO) - Proxy de estrutura
3. 💰 **Patrimônio** (LOG_BENS) - Correlação com recursos
4. 📍 **UF** (Features dummy de estado) - Regionalização
5. 💼 **Ocupação** (OCUPACAO_TAXA_ELEICAO) - Perfil profissional
6. 👥 **Coligação** (IS_COLIGADO, QTD_PARTIDOS) - Alianças

---

## ⚙️ Dependências

```toml
[project]
dependencies = [
    "pandas>=2.0.0",
    "numpy>=1.24.0",
    "matplotlib>=3.7.0",
    "seaborn>=0.12.0",
    "scikit-learn>=1.3.0",
    "xgboost>=2.0.0",
    "shap>=0.42.0"
]
```

---

## 🔬 Metodologia Científica

### 1. ETL (Extração, Transformação, Carga) - Modularizado
- ✅ Carregamento de 2018 e 2022 (funções `_carregar_csv_padronizado`)
- ✅ Limpeza de dados (tratamento de #NULO, #NE via `_limpar_valores_tse`)
- ✅ Merge de 3 datasets por ano (candidatos + complementar + bens)
- ✅ Agregação de patrimônio por candidato (`_agregar_bens`)
- ✅ Filtro de candidatos válidos (situação regular, resultado definido)
- ✅ Mapeamento de fusões partidárias (PR→PL, DEM→UNIÃO, etc.)

### 2. Engenharia de Features - Pipeline Completo
- ✅ Variáveis binárias (IS_REELEICAO, IS_FEMININO, IS_COLIGADO)
- ✅ Target encoding com smoothing bayesiano (partido, ocupação)
- ✅ Transformações logarítmicas (LOG_BENS, LOG_DESPESAS)
- ✅ One-hot encoding (27 UFs)
- ✅ Features de coligação (QTD_PARTIDOS_COLIGACAO)
- ✅ Imputação inteligente (idade: mediana, reeleição: 'N', despesas: 0)

### 3. Tratamento de Desbalanceamento
- ✅ Class weights balanceados (~9x para classe minoritária)
- ✅ `scale_pos_weight` para XGBoost
- ✅ Stratified CV (preserva proporções em folds)
- ⚠️ SMOTE não utilizado (evita overfitting artificial em validação temporal)

### 4. Validação Temporal Rigorosa
- ✅ **Treino exclusivo em 2018** (N ≈ 3.000)
- ✅ **Teste exclusivo em 2022** (N ≈ 3.000)
- ✅ Gap de 4 anos (simula cenário real prospectivo)
- ✅ Encodings ajustados no treino, aplicados no teste
- ✅ Mais rigoroso que split 80/20 tradicional

### 5. Otimização em 3 Etapas
- ✅ **Etapa 1:** Screening com GridSearch reduzido (4 modelos)
- ✅ **Etapa 2:** Seleção dos Top 2 por F1-Score
- ✅ **Etapa 3:** Otimização completa (GridSearch/RandomizedSearch)
- ✅ Análise de convergência do RandomizedSearchCV

### 6. Interpretabilidade Multi-Nível
- ✅ Feature Importance (MDI para árvores, coeficientes para LR)
- ✅ Análise SHAP (valores Shapley, direcionalidade, interações)
- ✅ Waterfall plots (explicações individuais)
- ✅ Consenso entre modelos (robustez dos fatores)

### 7. Considerações Éticas
- ✅ Discussão de vieses algorítmicos (incumbência, socioeconômico)
- ✅ Riscos de manipulação (gaming do sistema)
- ✅ Impacto em autodeterminação democrática
- ✅ Princípios norteadores (transparência, equidade, accountability)

---

## ⚠️ Limitações

### Dados Ausentes
- ❌ Gastos reais de campanha
- ❌ Tempo de TV/Rádio
- ❌ Presença em redes sociais
- ❌ Pesquisas eleitorais

### Desafios Técnicos
- 🔴 Desbalanceamento severo (1:9)
- 🔴 Performance modesta (~60% AUC-ROC, ~25% F1)
- 🔴 Validação temporal rigorosa (4 anos de gap)
- 🔴 Mudanças no cenário político entre 2018-2022

### Riscos Identificados
- ⚠️ Viés de incumbência
- ⚠️ Viés socioeconômico (patrimônio)
- ⚠️ Proxy indireto (partido)

---

## 🚀 Melhorias Futuras

### Curto Prazo
- [x] ~~Incluir dados de 2018~~ ✅ Implementado (validação temporal)
- [x] ~~XGBoost com GridSearch~~ ✅ Implementado
- [x] ~~Análise SHAP para explicabilidade~~ ✅ Implementado
- [ ] Incluir dados de 2014 (validação em 3 eleições)
- [ ] Adicionar gastos reais pós-eleição
- [ ] Web scraping de redes sociais

### Médio Prazo
- [ ] LightGBM e CatBoost (algoritmos adicionais)
- [ ] Feature selection (SelectKBest, RFE)
- [ ] Análise de fairness (disparate impact por gênero/raça)
- [ ] Calibração de probabilidades (Platt scaling)

### Longo Prazo
- [ ] Dashboard interativo (Streamlit/Dash)
- [ ] API REST para consultas
- [ ] Sistema de alerta para campanhas
- [ ] Modelo de linguagem para análise de propostas

---

## 📚 Referências

### Dados
- [TSE - Portal de Dados Abertos](https://dadosabertos.tse.jus.br/)
- [Repositório de Dados Eleitorais](https://sig.tse.jus.br/)

### Literatura
- Chawla, N. V. et al. (2002). "SMOTE: Synthetic Minority Over-sampling Technique"
- He, H. & Garcia, E. A. (2009). "Learning from Imbalanced Data"

### Bibliotecas
- [Scikit-learn Documentation](https://scikit-learn.org/)
- [Pandas Documentation](https://pandas.pydata.org/)

---

## 👥 Equipe

**Autores:**  
- Artur Garcia  
- Artur Saraiva  

**Orientação:**  
Prof. Dr. César Lincoln Cavalcante Mattos

**Instituição:**  
Universidade Federal do Ceará (UFC)  
Disciplina: Aprendizagem de Máquina  
Período: 2025.2

---

## 📄 Licença

Este projeto é acadêmico e utiliza dados públicos do TSE.

---

## 📞 Contato

Para dúvidas ou sugestões sobre este projeto:
- 📧 E-mail: estatistica@tse.jus.br (dados do TSE)
- 🎓 Universidade: UFC - Departamento de Computação

---

## ✅ Status

**🟢 PROJETO FINALIZADO - PRONTO PARA ENTREGA**

Data de Conclusão: Janeiro 2026  
Versão: 1.0  

---

## 🏆 Contribuição Científica

Este projeto demonstra uma abordagem rigorosa para predição eleitoral usando Machine Learning, servindo como baseline para pesquisas futuras em ciência política computacional.

**Principais Contribuições:**
1. ✅ **Validação Temporal Rigorosa** (treino 2018 → teste 2022, gap de 4 anos)
2. ✅ **Implementação completa de pipeline ML** com dados reais do TSE
3. ✅ **Tratamento adequado de desbalanceamento severo** (class weights, métricas apropriadas)
4. ✅ **Comparação robusta de 4 algoritmos** com otimização em 3 etapas
5. ✅ **Interpretabilidade multi-nível** (SHAP, feature importance, consenso)
6. ✅ **Engenharia de features justificada** (target encoding, transformações log, coligação)
7. ✅ **Discussão ética abrangente** (vieses, fairness, impacto democrático)
8. ✅ **Interpretação crítica e reconhecimento de limitações**
9. ✅ **Documentação acadêmica completa e replicável**

**Relevância Acadêmica:**
- **Ciência Política Computacional:** Quantifica fatores estruturais de sucesso eleitoral
- **Machine Learning Aplicado:** Caso de estudo de validação temporal com desbalanceamento extremo
- **Ética e Sociedade:** Análise crítica de vieses algorítmicos em contexto democrático

---

⭐ **Se este projeto foi útil, considere dar uma estrela!**
