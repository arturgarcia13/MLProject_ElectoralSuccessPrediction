# 🗳️ Predição de Sucesso Eleitoral - Deputado Federal

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![Status](https://img.shields.io/badge/Status-Finalizado-success.svg)]()

Projeto de Machine Learning para predição de sucesso eleitoral de candidatos a Deputado Federal utilizando dados reais do TSE (Eleições 2022).

---

## 📑 Descrição

Este projeto implementa e compara **3 modelos de classificação supervisionada** para prever se um candidato será eleito ou não, enfrentando o desafio de **desbalanceamento severo de classes** (apenas ~10% de eleitos).

### Modelos Implementados
- 🔷 **Regressão Logística** (baseline interpretável)
- 🌲 **Random Forest** (ensemble robusto)
- ⚡ **Gradient Boosting** (estado da arte)

### Métricas Avaliadas
- F1-Score (média harmônica)
- AUC-ROC (discriminação)
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
- **Eleições 2022** (Ordinárias)
- **Cargo:** Deputado Federal (CD_CARGO = 6)

### Arquivos Necessários
```
projeto/data/
├── consulta_cand_2022/
│   └── consulta_cand_2022_BRASIL.csv
├── consulta_cand_complementar_2022/
│   └── consulta_cand_complementar_2022_BRASIL.csv
└── bem_candidato_2022/
    └── bem_candidato_2022_BRASIL.csv
```

### Atributos Utilizados
- **Pessoais:** Idade, Gênero, Grau de Instrução, Estado Civil, Cor/Raça
- **Políticos:** Partido, Reeleição, UF
- **Financeiros:** Patrimônio declarado, Teto de despesas
- **Profissionais:** Ocupação

---

## 🚀 Como Executar

### 1. Pré-requisitos

```bash
Python 3.8+
Jupyter Notebook
```

### 2. Instalar Dependências

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

Ou usando o pyproject.toml:

```bash
pip install .
```

### 3. Executar o Notebook

#### Opção A: VS Code
1. Abra `projeto_ml_depfed.ipynb` no VS Code
2. Selecione o kernel Python
3. Execute "Run All Cells" (Ctrl+Shift+Alt+Enter)

#### Opção B: Jupyter Notebook
```bash
jupyter notebook projeto_ml_depfed.ipynb
```

#### Opção C: Jupyter Lab
```bash
jupyter lab projeto_ml_depfed.ipynb
```

### 4. Tempo de Execução
- **Carga de dados:** ~30 segundos
- **EDA:** ~1 minuto
- **Treinamento dos 3 modelos:** ~2-3 minutos
- **Validação cruzada:** ~5 minutos
- **Total estimado:** ~10 minutos

---

## 📁 Estrutura do Projeto

```
AprendizagemDeMaquina/
│
├── projeto_ml_depfed.ipynb          # 🎯 NOTEBOOK PRINCIPAL
├── RESUMO_EXECUTIVO.md              # Relatório completo
├── README.md                        # Este arquivo
├── pyproject.toml                   # Gerenciamento de dependências
│
└── projeto/
    └── data/                        # Dados do TSE
        ├── consolidado.md           # Documentação dos dados
        ├── consulta_cand_2022/
        ├── consulta_cand_complementar_2022/
        └── bem_candidato_2022/
```

---

## 📈 Resultados Esperados

### Modelo Vencedor: Regressão Logística

| Métrica          | Valor Esperado |
|------------------|----------------|
| **F1-Score**     | ~0.25          |
| **AUC-ROC**      | ~0.60          |
| **Recall**       | ~0.35          |
| **Precision**    | ~0.20          |
| **Balanced Acc** | ~0.65          |

### Fatores Mais Preditivos
1. 🏛️ **Reeleição** (IS_REELEICAO)
2. 🎯 **Partido** (PARTIDO_TAXA_ELEICAO)
3. 💰 **Patrimônio** (LOG_BENS)
4. 📍 **Região** (Features UF)
5. 💼 **Ocupação** (OCUPACAO_TAXA_ELEICAO)

---

## ⚙️ Dependências

```toml
[project]
dependencies = [
    "pandas>=2.0.0",
    "numpy>=1.24.0",
    "matplotlib>=3.7.0",
    "seaborn>=0.12.0",
    "scikit-learn>=1.3.0"
]
```

---

## 🔬 Metodologia Científica

### 1. ETL (Extração, Transformação, Carga)
- ✅ Limpeza de dados (tratamento de #NULO, #NE)
- ✅ Merge de 3 datasets (candidatos + complementar + bens)
- ✅ Agregação de patrimônio por candidato
- ✅ Remoção de registros com dados críticos ausentes

### 2. Engenharia de Features
- ✅ Variáveis binárias (IS_REELEICAO, IS_FEMININO)
- ✅ Target encoding (partido, ocupação)
- ✅ Transformações logarítmicas (bens, despesas)
- ✅ One-hot encoding (UFs)

### 3. Tratamento de Desbalanceamento
- ✅ Class weights balanceados (~9x para classe minoritária)
- ⚠️ SMOTE não utilizado (evita overfitting artificial)

### 4. Validação
- ✅ Split estratificado 80/20
- ✅ Validação cruzada 5-fold
- ✅ Métricas apropriadas para classes desbalanceadas

---

## ⚠️ Limitações

### Dados Ausentes
- ❌ Gastos reais de campanha
- ❌ Tempo de TV/Rádio
- ❌ Presença em redes sociais
- ❌ Pesquisas eleitorais

### Desafios Técnicos
- 🔴 Desbalanceamento severo (1:9)
- 🔴 Performance modesta (~60% AUC)
- 🔴 Generalização temporal incerta

### Riscos Identificados
- ⚠️ Viés de incumbência
- ⚠️ Viés socioeconômico (patrimônio)
- ⚠️ Proxy indireto (partido)

---

## 🚀 Melhorias Futuras

### Curto Prazo
- [ ] Incluir dados de 2018 e 2014
- [ ] Adicionar gastos reais pós-eleição
- [ ] Web scraping de redes sociais

### Médio Prazo
- [ ] XGBoost/LightGBM com GridSearch
- [ ] Feature engineering avançada
- [ ] Análise SHAP para explicabilidade

### Longo Prazo
- [ ] Dashboard interativo
- [ ] API REST para consultas
- [ ] Sistema de alerta para campanhas

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

## 🏆 Contribuição

Este projeto demonstra uma abordagem rigorosa para predição eleitoral usando Machine Learning, servindo como baseline para pesquisas futuras em ciência política computacional.

**Principais Contribuições:**
1. ✅ Implementação completa de pipeline ML com dados reais
2. ✅ Tratamento adequado de desbalanceamento severo
3. ✅ Comparação robusta de 3 algoritmos
4. ✅ Interpretação crítica e reconhecimento de limitações
5. ✅ Documentação acadêmica completa

---

⭐ **Se este projeto foi útil, considere dar uma estrela!**
