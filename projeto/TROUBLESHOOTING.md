# 🔧 Guia de Troubleshooting - Projeto ML Deputado Federal

Este guia ajuda a resolver problemas comuns ao executar o projeto.

---

## 📋 Índice de Problemas

1. [Erros de Carregamento de Dados](#1-erros-de-carregamento-de-dados)
2. [Erros de Encoding](#2-erros-de-encoding)
3. [Erros de Memória](#3-erros-de-memória)
4. [Erros de Dependências](#4-erros-de-dependências)
5. [Erros no Kernel do Jupyter](#5-erros-no-kernel-do-jupyter)
6. [Resultados Inesperados](#6-resultados-inesperados)
7. [Performance Lenta](#7-performance-lenta)

---

## 1. Erros de Carregamento de Dados

### ❌ Problema: `FileNotFoundError: [Errno 2] No such file or directory`

**Causa:** Caminho dos arquivos CSV incorreto

**Solução:**
```python
# Verificar se os arquivos existem
import os
print(os.path.exists('projeto/data/consulta_cand_2022/consulta_cand_2022_BRASIL.csv'))

# Se retornar False, ajustar o caminho:
# Windows: usar \\ ou r'caminho\arquivo.csv'
# Linux/Mac: usar / normalmente
```

**Alternativa:** Usar caminho absoluto
```python
cand = pd.read_csv(
    r'E:\MYAREA\AREA_DEV\Faculdade\AprendizagemDeMaquina\projeto\data\consulta_cand_2022\consulta_cand_2022_BRASIL.csv',
    encoding='latin1',
    sep=';',
    low_memory=False
)
```

---

## 2. Erros de Encoding

### ❌ Problema: `UnicodeDecodeError: 'utf-8' codec can't decode byte`

**Causa:** Encoding incorreto (TSE usa Latin-1)

**Solução:**
```python
# SEMPRE usar encoding='latin1' para arquivos do TSE
cand = pd.read_csv('arquivo.csv', encoding='latin1', sep=';')
```

### ❌ Problema: Caracteres estranhos (�, ã, ç)

**Causa:** Encoding UTF-8 aplicado em arquivo Latin-1

**Solução:**
```python
# Recarregar com encoding correto
df = pd.read_csv('arquivo.csv', encoding='latin1', sep=';')
```

---

## 3. Erros de Memória

### ❌ Problema: `MemoryError` ao carregar dados

**Causa:** Dataset muito grande (pode ter ~10-15GB de RAM)

**Solução 1:** Usar `low_memory=False`
```python
cand = pd.read_csv('arquivo.csv', encoding='latin1', sep=';', low_memory=False)
```

**Solução 2:** Carregar apenas colunas necessárias
```python
cols_needed = ['SQ_CANDIDATO', 'CD_CARGO', 'DS_SIT_TOT_TURNO', 'SG_PARTIDO']
cand = pd.read_csv('arquivo.csv', encoding='latin1', sep=';', usecols=cols_needed)
```

**Solução 3:** Usar chunks
```python
chunks = pd.read_csv('arquivo.csv', encoding='latin1', sep=';', chunksize=10000)
df = pd.concat([chunk[chunk['CD_CARGO'] == 6] for chunk in chunks])
```

---

## 4. Erros de Dependências

### ❌ Problema: `ModuleNotFoundError: No module named 'sklearn'`

**Solução:**
```bash
pip install scikit-learn
```

### ❌ Problema: `ImportError: cannot import name 'StratifiedKFold'`

**Solução:** Atualizar scikit-learn
```bash
pip install --upgrade scikit-learn
```

### ❌ Problema: Versões incompatíveis

**Solução:** Instalar versões específicas
```bash
pip install pandas==2.0.0 numpy==1.24.0 scikit-learn==1.3.0
```

---

## 5. Erros no Kernel do Jupyter

### ❌ Problema: `Kernel appears to have died. It will restart automatically.`

**Causas possíveis:**
- Memória insuficiente
- Código infinito/recursivo
- Erro de segmentação

**Solução:**
1. Reiniciar kernel: `Kernel > Restart`
2. Limpar outputs: `Cell > All Output > Clear`
3. Executar célula por célula para identificar o problema

### ❌ Problema: Kernel não conecta

**Solução:**
```bash
# Reinstalar Jupyter
pip install --upgrade jupyter

# Ou usar ambiente virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
venv\Scripts\activate     # Windows
pip install jupyter pandas scikit-learn
```

---

## 6. Resultados Inesperados

### ❌ Problema: F1-Score = 0.00 ou muito baixo

**Causas possíveis:**
1. Target mal definido
2. Modelo não convergiu
3. Class weights não aplicados

**Diagnóstico:**
```python
# Verificar distribuição do target
print(y_train.value_counts())
print(y_test.value_counts())

# Verificar se modelo convergiu (Regressão Logística)
print(f'Convergiu: {lr_model.n_iter_}')

# Verificar predições
print(pd.Series(lr_pred).value_counts())
```

**Solução:**
```python
# Garantir class_weight='balanced'
lr_model = LogisticRegression(class_weight='balanced', max_iter=2000)
```

### ❌ Problema: AUC-ROC = 0.50 (modelo aleatório)

**Causa:** Modelo não está aprendendo

**Solução:**
1. Verificar se features têm variância
2. Aumentar `max_iter` na Regressão Logística
3. Verificar se há data leakage invertido

```python
# Verificar variância das features
print(X_train.var())

# Features com variância zero devem ser removidas
X_train = X_train.loc[:, X_train.var() > 0]
```

### ❌ Problema: Recall = 0.00 (não detecta nenhum eleito)

**Causa:** Modelo muito conservador (prediz tudo como 0)

**Solução:**
```python
# Ajustar threshold de decisão
from sklearn.metrics import precision_recall_curve

precision, recall, thresholds = precision_recall_curve(y_test, lr_proba)
# Escolher threshold que maximize F1
f1_scores = 2 * (precision * recall) / (precision + recall)
best_threshold = thresholds[np.argmax(f1_scores)]

# Aplicar threshold customizado
lr_pred_custom = (lr_proba >= best_threshold).astype(int)
```

---

## 7. Performance Lenta

### ❌ Problema: Validação cruzada muito lenta (>30 minutos)

**Solução 1:** Usar `n_jobs=-1` (paralelização)
```python
scores = cross_val_score(model, X, y, cv=5, scoring='f1', n_jobs=-1)
```

**Solução 2:** Reduzir complexidade do modelo
```python
# Random Forest: reduzir n_estimators
rf_model = RandomForestClassifier(n_estimators=50)  # Em vez de 100

# Gradient Boosting: reduzir n_estimators ou max_depth
gb_model = GradientBoostingClassifier(n_estimators=50, max_depth=3)
```

**Solução 3:** Reduzir folds do CV
```python
# 3-fold em vez de 5-fold
skf = StratifiedKFold(n_splits=3, shuffle=True, random_state=42)
```

---

## 8. Problemas Específicos do Projeto

### ❌ Problema: VR_BEM_CANDIDATO não converte para float

**Causa:** Valores estão como string com aspas e vírgula decimal

**Solução:**
```python
bens['VR_BEM_CANDIDATO'] = (
    bens['VR_BEM_CANDIDATO']
    .str.replace('"', '')
    .str.replace(',', '.')
    .astype(float)
)
```

### ❌ Problema: Muitos valores #NULO

**Solução:** Substituir antes de processar
```python
df = df.replace(['#NULO', '#NE', '"#NULO"', '"#NE"'], np.nan)
```

### ❌ Problema: Merge não retorna registros esperados

**Diagnóstico:**
```python
# Verificar quantidade de SQ_CANDIDATO únicos
print(f'Candidatos únicos em cand: {cand["SQ_CANDIDATO"].nunique()}')
print(f'Candidatos únicos em cand_comp: {cand_comp["SQ_CANDIDATO"].nunique()}')

# Verificar intersecção
comum = set(cand['SQ_CANDIDATO']) & set(cand_comp['SQ_CANDIDATO'])
print(f'Candidatos em comum: {len(comum)}')
```

**Solução:** Usar `how='left'` no merge
```python
df = cand.merge(cand_comp, on='SQ_CANDIDATO', how='left')
```

---

## 9. Erros em Gráficos

### ❌ Problema: `UserWarning: Matplotlib is currently using agg`

**Solução:**
```python
# Adicionar no início do notebook
%matplotlib inline
import matplotlib
matplotlib.use('inline')
```

### ❌ Problema: Gráficos não aparecem

**Solução:**
```python
import matplotlib.pyplot as plt
%matplotlib inline

# No final de cada plot
plt.show()
```

---

## 10. Validação Final

### ✅ Checklist de Verificação

Antes de considerar o notebook pronto:

```python
# 1. Verificar se target está balanceado no split
print('Treino:', y_train.value_counts(normalize=True))
print('Teste:', y_test.value_counts(normalize=True))

# 2. Verificar se há NaN nas features
print('NaN em X_train:', X_train.isna().sum().sum())
print('NaN em X_test:', X_test.isna().sum().sum())

# 3. Verificar forma dos dados
print(f'X_train shape: {X_train.shape}')
print(f'X_test shape: {X_test.shape}')
print(f'y_train shape: {y_train.shape}')
print(f'y_test shape: {y_test.shape}')

# 4. Verificar range das features (após scaling)
print(f'X_train_scaled mean: {X_train_scaled.mean():.6f}')
print(f'X_train_scaled std: {X_train_scaled.std():.6f}')

# 5. Verificar predições fazem sentido
print('Predições LR:', pd.Series(lr_pred).value_counts())
print('Probabilidades LR:', lr_proba.min(), '-', lr_proba.max())
```

---

## 11. Contatos e Suporte

### Recursos Oficiais
- **Documentação TSE:** https://dadosabertos.tse.jus.br/
- **Scikit-learn Docs:** https://scikit-learn.org/
- **Pandas Docs:** https://pandas.pydata.org/

### Stack Overflow
- Tag: `[python] [scikit-learn] [machine-learning]`
- Sempre incluir código mínimo reproduzível

---

## 12. Dicas Gerais

### 🔍 Debug Eficiente

```python
# Imprimir informações detalhadas
def debug_dataset(df, name='Dataset'):
    print(f'\n=== {name} ===')
    print(f'Shape: {df.shape}')
    print(f'Columns: {list(df.columns)}')
    print(f'Dtypes:\n{df.dtypes}')
    print(f'Missing:\n{df.isna().sum()}')
    print(f'Memory: {df.memory_usage().sum() / 1024**2:.2f} MB')

debug_dataset(cand, 'Candidatos')
```

### 📊 Monitorar Memória

```python
import psutil
import os

def print_memory_usage():
    process = psutil.Process(os.getpid())
    mem = process.memory_info().rss / 1024**2
    print(f'Memória em uso: {mem:.2f} MB')

print_memory_usage()
```

### ⏱️ Medir Tempo de Execução

```python
import time

inicio = time.time()
# ... código ...
fim = time.time()
print(f'Tempo de execução: {fim - inicio:.2f} segundos')
```

---

## ✅ Tudo Funcionando?

Se seguiu este guia e ainda há problemas:

1. ✅ Reinicie o kernel e execute tudo novamente
2. ✅ Verifique se tem espaço em disco suficiente (>5GB)
3. ✅ Confirme versões das bibliotecas: `pip list`
4. ✅ Teste em ambiente limpo (venv)

---

**Última atualização:** Janeiro 2026  
**Versão:** 1.0  
**Status:** ✅ Completo
