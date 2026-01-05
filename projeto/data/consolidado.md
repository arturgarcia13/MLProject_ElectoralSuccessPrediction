# CONSOLIDAÇÃO DE DOCUMENTOS - ARQUIVOS PDF

---

## ARQUIVO 1: leiamebc.pdf

### LEIAUE - INSTRUÇÕES DE LEITURA

Este arquivo contém o leiaute das tabelas existentes no repositório de dados eleitorais. Antes de trabalhar os dados é importante ler as seguintes considerações:

- A codificação de caracteres dos arquivos é "Latin 1";
- Os campos estão entre aspas e separados por ponto e vírgula, inclusive os campos numéricos;
- Campos preenchidos com #NULO significam que a informação está em branco no banco de dados. O correspondente para #NULO nos campos numéricos é -1;
- Campos preenchidos com #NE significam que naquele ano a informação não era registrada em banco de dados pelos sistemas eleitorais. O correspondente para #NE nos campos numéricos é -3;
- O campo UF, além das unidades da federação, pode conter alguma das seguintes situações:
  - BR: quando se tratar de informação a nível nacional;
  - VT: quando se tratar de voto em trânsito;
  - ZZ: quando se tratar de Exterior.
- Os arquivos estão em constante processo de atualização e aperfeiçoamento. Alguns arquivos podem estar em branco ou com mensagem de erro devido à indisponibilidade temporária na base de algum estado ou à inexistência daquele arquivo para a época pretendida.

Agradecemos todas as críticas e sugestões recebidas de pesquisadores e usuários que estão colaborando para a melhoria na qualidade da prestação das informações. Em especial à Associação Brasileira de Ciência Política (ABCP) que, por meio de acordo de cooperação técnica firmado com o TSE, está auxiliando na verificação dos arquivos gerados e informando o TSE quanto às necessidades de dados dos pesquisadores.

Qualquer sugestão ou dúvida deve ser encaminhada ao e-mail estatistica@tse.jus.br.

### BEM_CANDIDATO

**NOTAÇÃO:**
- BEM_CANDIDATO_<ANO>_<UF>
- BEM_CANDIDATO_<ANO>_BRASIL

**Variáveis e Descrições:**

| Variável | Descrição |
|----------|-----------|
| DT_GERACAO | Data da extração dos dados para geração do arquivo. |
| HH_GERACAO | Hora da extração dos dados para geração do arquivo com base no horário de Brasília. |
| ANO_ELEICAO | Ano de referência da eleição para geração do arquivo. Observação: para eleições suplementares, o ano de referência da eleição é o ano da eleição ordinária correspondente. Por exemplo: em 2016 houve eleições ordinárias. Após a data desta eleição ordinária e antes da próxima, houve eleições suplementares em 2017, 2018 e 2019. As informações destas eleições suplementares estarão divulgadas no arquivo gerado para as eleições 2016. |
| CD_TIPO_ELEICAO | Código do tipo de eleição. Pode assumir os valores: 1 (Eleição Suplementar), 2 (Eleição Ordinária), 3 (Consulta Popular). |
| NM_TIPO_ELEICAO | Nome do tipo de eleição. Observação: as eleições ordinárias são previstas em Lei, possuem data certa para serem realizadas, ocorrem em anos pares e possuem a periodicidade de 04 em 04 anos. Nas eleições gerais ordinárias são eleitos os cargos de Presidente, Governador, Deputado (Federal e Estadual) e Senador. Nas eleições ordinárias municipais são eleitos os cargos de Prefeito e Vereador. As eleições suplementares são aquelas que não têm periodicidade pré-determinada ou definida e ocorrem quando, eventualmente, se fizerem necessárias. As consultas populares ocorrem sempre que a população é convocada a opinar diretamente sobre um assunto específico e importante. Ela pode ser realizada de duas formas: plebiscito (quando o cidadão opina previamente sobre a possível criação de uma lei) e referendo (quando uma lei aprovada por um órgão legislativo é submetida à aceitação ou não das eleitoras e dos eleitores). |
| CD_ELEICAO | Código único da eleição no âmbito da Justiça Eleitoral. Observação: este código é único por eleição e por turno, ou seja, cada turno possui seu código de eleição. |
| DS_ELEICAO | Descrição da eleição. |
| DT_ELEICAO | Data da ocorrência da eleição. |
| SG_UF | Sigla da unidade da federação na qual a candidata ou candidato concorre na eleição. |
| SG_UE | Sigla da unidade eleitoral na qual a candidata ou candidato concorre na eleição. A unidade eleitoral representa a unidade da federação ou o município em que a candidata ou o candidato concorre na eleição e é relacionada à abrangência territorial desta candidatura. Em caso de abrangência federal (cargo de Presidente e Vice-Presidente) a sigla é BR. Em caso de abrangência estadual (cargos de Governador, Vice-Governador, Senador, Deputado Federal, Deputado Estadual e Deputado Distrital) a sigla é a UF da candidatura. Em caso de abrangência municipal (cargos de Prefeito, Vice-Prefeito e Vereador) é o código TSE de identificação do município da candidatura. |
| NM_UE | Nome da unidade eleitoral da candidata ou candidato. Em caso de abrangência nacional, é igual a "Brasil". Em caso de abrangência estadual, é o nome da UF em que a candidata ou candidato concorre. Em caso de abrangência municipal, é o nome do município em que a candidata ou candidato concorre. |
| SQ_CANDIDATO | Número sequencial da candidata ou candidato, gerado internamente pelos sistemas eleitorais para cada eleição. Observação: não é o número de campanha da candidata ou candidato. |
| NR_ORDEM_BEM_CANDIDATO | Número de ordenação do bem patrimonial material da candidata ou candidato de acordo com a sequência de bens declarados pela(o) mesma(o). |
| CD_TIPO_BEM_CANDIDATO | Código do tipo do bem patrimonial material declarado pela candidata ou candidato. |
| DS_TIPO_BEM_CANDIDATO | Tipo do bem patrimonial material declarado pela candidata ou candidato. |
| DS_BEM_CANDIDATO | Descrição detalhada do bem material patrimonial da candidata ou candidato. |
| VR_BEM_CANDIDATO | Valor, em reais, do bem patrimonial material declarado pela candidata ou candidato. |
| DT_ULT_ATUAL_BEM_CANDIDATO | Data da última atualização, no sistema, do bem material patrimonial declarado pela candidata ou candidato. |
| HH_ULT_ATUAL_BEM_CANDIDATO | Hora da última atualização, no sistema, do bem material patrimonial declarado pela candidata ou candidato. |

---

## ARQUIVO 2: projeto-depfed-relatorio.pdf

### Proposta de Projeto Final - Aprendizagem de Máquina (2025.2)

**Instituição:** Universidade Federal do Ceará (UFC)
**Professor:** César Lincoln Cavalcante Mattos
**Equipe:** Artur Garcia, Artur Saraiva

#### 1. Título do Projeto

Predição de Sucesso Eleitoral para Deputado Federal: Uma Abordagem Comparativa de Classificação Supervisionada em Dados Desbalanceados

#### 2. Definição do Problema

O sistema eleitoral brasileiro gera uma grande quantidade de dados públicos, permitindo a análise de quais fatores influenciam o sucesso de uma candidatura. O problema abordado neste projeto é de Classificação Binária Supervisionada.

O objetivo é treinar modelos de Aprendizagem de Máquina capazes de prever se um candidato ao cargo de Deputado Federal será "Eleito" (1) ou "Não Eleito" (0), com base exclusivamente em atributos declarados ao Tribunal Superior Eleitoral (TSE) antes do pleito.

Este problema apresenta um desafio clássico de ML: o desbalanceamento de classes, visto que a proporção de candidatos eleitos é significativamente menor do que a de não eleitos (geralmente menos de 10% de sucesso).

#### 3. Fonte de Dados (Dados Reais)

Utilizam-se os dados abertos disponibilizados pelo Tribunal Superior Eleitoral (TSE) através do Portal de Dados Abertos.

**Fonte:** Repositório de Dados Eleitorais do TSE (https://dados.gov.br/ ou https://sig.tse.jus.br/)
**Recorte Temporal:** Eleições Gerais de 2022 (e possivelmente 2018 para aumento do dataset, se necessário)
**Volume Estimado:** Aproximadamente 10.000 a 15.000 instâncias (candidatos) por eleição

##### 3.1. Atributos (Features) Previstos

As variáveis independentes (X) incluirão dados socioeconômicos e políticos:

1. **Dados Pessoais:** Idade, Gênero, Estado Civil, Grau de Instrução, Ocupação.
2. **Dados Financeiros:** Total de Bens Declarados (numérico), Teto de Gastos de Campanha.
3. **Dados Políticos:** Partido, Coligação (se houver), Situação de Reeleição (bool), Estado (UF).
4. **Target (Y):** Situação final (Eleito/Eleito por média/Eleito por QP = 1; Suplente/Não Eleito = 0).

#### 4. Metodologia Proposta

##### 4.1. Pré-processamento

- **Limpeza:** Tratamento de valores nulos (ex: candidatos sem bens declarados).
- **Codificação:** Utilização de One-Hot Encoding para variáveis categóricas (Estado, Gênero) e Target Encoding para variáveis de alta cardinalidade (Partido, Ocupação).
- **Tratamento de Desbalanceamento:** Como a classe positiva (Eleito) é minoritária, aplicam-se técnicas como:
  - Undersampling da classe majoritária.
  - SMOTE (Synthetic Minority Over-sampling Technique).
  - Ajuste de Class Weights nos algoritmos.

##### 4.2. Modelos Selecionados

Para atender ao requisito de comparação de desempenho, utilizam-se três abordagens distintas:

1. **Regressão Logística:** Servirá como baseline (modelo base) e permitirá alta interpretabilidade (analisar quais coeficientes/atributos pesam mais na vitória).
2. **Random Forest:** Modelo de ensemble robusto, capaz de capturar relações não-lineares e menos sensível a outliers.
3. **Gradient Boosting (XGBoost ou LightGBM):** Estado da arte para dados tabulares, focado em maximizar a performance preditiva.

##### 4.3. Estratégia de Avaliação

Dado o desbalanceamento, a Acurácia não será uma métrica confiável. A avaliação será feita utilizando Validação Cruzada (k-fold cross-validation) com k = 5 ou k = 10, observando as seguintes métricas:

- **F1-Score:** Média harmônica entre precisão e recall (crucial para classes desbalanceadas).
- **AUC-ROC:** Para avaliar a capacidade do modelo de distinguir entre as classes.
- **Matriz de Confusão:** Para visualizar falsos positivos e falsos negativos.

#### 5. Cronograma Macro

- **09/12 a 15/12:** Coleta e Limpeza de Dados (ETL).
- **16/12 a 22/12:** Análise Exploratória (EDA) e Engenharia de Atributos.
- **23/12 a 05/01:** Treinamento, Ajuste de Hiperparâmetros e Validação dos Modelos.
- **06/01:** Finalização da Escrita do Artigo e Preparação da Apresentação.
- **07/01:** Entrega Final.

#### 6. Ferramentas

- **Linguagem:** Python 3.x
- **Bibliotecas:** Pandas, Scikit-Learn, Imbalanced-learn, Matplotlib/Seaborn.

---

## ARQUIVO 3: leiameccc.pdf

### LEIAUE - INSTRUÇÕES DE LEITURA

Este arquivo contém o leiaute das tabelas existentes no repositório de dados eleitorais. Antes de trabalhar os dados é importante ler as seguintes considerações:

- A codificação de caracteres dos arquivos é "Latin 1";
- Os campos estão entre aspas e separados por ponto e vírgula, inclusive os campos numéricos;
- Campos preenchidos com #NULO significam que a informação está em branco no banco de dados. O correspondente para #NULO nos campos numéricos é -1;
- Campos preenchidos com #NE significam que naquele ano a informação não era registrada em banco de dados pelos sistemas eleitorais. O correspondente para #NE nos campos numéricos é -3;
- O campo UF, além das unidades da federação, pode conter alguma das seguintes situações:
  - BR: quando se tratar de informação a nível nacional;
  - VT: quando se tratar de voto em trânsito;
  - ZZ: quando se tratar de Exterior.
- Os arquivos estão em constante processo de atualização e aperfeiçoamento. Alguns arquivos podem estar em branco ou com mensagem de erro devido à indisponibilidade temporária na base de algum estado ou à inexistência daquele arquivo para a época pretendida.

O § 2º do art. 33 da Resolução TSE nº 23.609/2019 estabelece que:

"Os endereços informados para atribuição de CNPJ, comunicações processuais e do Comitê Central de Campanha, telefone pessoal, e-mail pessoal, número do CPF e o documento pessoal de identificação não serão divulgados no DivulgaCandContas e serão juntados como documento sigiloso no processo de registro de candidatura no PJe. (incluído pela Resolução nº 23.729/2024)"

Portanto, são considerados como não divulgáveis, para as Eleições 2024, as informações registradas na variável NR_CPF_CANDIDATO dos arquivos de candidaturas do Portal de Dados Abertos.

Agradecemos todas as críticas e sugestões recebidas de pesquisadores e usuários que estão colaborando para a melhoria na qualidade da prestação das informações.

Qualquer sugestão ou dúvida deve ser encaminhada ao e-mail estatistica@tse.jus.br.

### CONSULTA_CAND_COMPLEMENTAR

**NOTAÇÃO:**
- CONSULTA_CAND_COMPLEMENTAR_<ANO>_<UF>
- CONSULTA_CAND_COMPLEMENTAR_<ANO>_BRASIL

Para o caso de candidatos não divulgáveis, seus dados pessoais são preenchidos com:
- -4, em caso de campos numéricos, exceto campo de idade;
- "NÃO DIVULGÁVEL", em caso de campos textuais;
- "", no caso de campos relativos à idade do candidato e sua data de nascimento.

**Variáveis e Descrições:**

| Variável | Descrição |
|----------|-----------|
| DT_GERACAO | Data da extração dos dados para geração do arquivo. |
| HH_GERACAO | Hora da extração dos dados para geração do arquivo com base no horário de Brasília. |
| ANO_ELEICAO | Ano de referência da eleição para geração do arquivo. |
| CD_ELEICAO | Código único da eleição no âmbito da Justiça Eleitoral. |
| SQ_CANDIDATO | Número sequencial da candidata ou candidato. |
| CD_DETALHE_SITUACAO_CAND | Código do detalhe da situação do registro da candidatura (até 2022). |
| DS_DETALHE_SITUACAO_CAND | Descrição do detalhe da situação do registro de candidatura (até 2022). |
| CD_NACIONALIDADE | Código da nacionalidade: 1 (Brasileira nata), 2 (Brasileira naturalizada), 3 (Portuguesa com igualdade de direitos), 4 (Estrangeiro). |
| DS_NACIONALIDADE | Descrição da nacionalidade. |
| CD_MUNICIPIO_NASCIMENTO | Código de identificação do município de nascimento. |
| NM_MUNICIPIO_NASCIMENTO | Nome do município de nascimento. |
| NR_IDADE_DATA_POSSE | Idade na data da posse. |
| ST_QUILOMBOLA | Indica se considera-se quilombola (S/N). |
| CD_ETNIA_INDIGENA | Código da etnia indígena autodeclarada. |
| DS_ETNIA_INDIGENA | Descrição da etnia indígena autodeclarada. |
| VR_DESPESA_MAX_CAMPANHA | Valor máximo de despesas de campanha declarada pelo partido. |
| ST_REELEICAO | Indica se está concorrendo à reeleição (S/N). |
| ST_DECLARAR_BENS | Indica se possui bens a declarar (S/N). |
| NR_PROTOCOLO_CANDIDATURA | Número do protocolo de registro de candidatura. |
| NR_PROCESSO | Número do processo de registro de candidatura. |
| CD_SITUACAO_CANDIDATO_PLEITO | Código da situação da candidatura no dia do pleito (até 2022). |
| DS_SITUACAO_CANDIDATO_PLEITO | Descrição da situação da candidatura no dia do pleito (até 2022). |
| CD_SITUACAO_CANDIDATO_URNA | Código da situação da candidatura na urna (até 2022). |
| DS_SITUACAO_CANDIDATO_URNA | Descrição da situação da candidatura na urna (até 2022). |
| ST_CANDIDATO_INSERIDO_URNA | Indica se foi inserido na urna eletrônica (S/N). |
| NM_TIPO_DESTINACAO_VOTOS | Nome do tipo da destinação dos votos. |
| CD_SITUACAO_CANDIDATO_TOT | Código da situação no banco de totalização (até 2022). |
| DS_SITUACAO_CANDIDATO_TOT | Descrição da situação no banco de totalização (até 2022). |
| ST_PREST_CONTAS | Indica se realizou prestação de contas eleitorais (S/N). |
| ST_SUBSTITUIDO | Indica se foi substituída(o) (Sim/Não). |
| SQ_SUBSTITUIDO | Sequencial do candidato que foi substituído. |
| SQ_ORDEM_SUPLENCIA | Ordem da suplência para cargos proporcionais. |
| DT_ACEITE_CANDIDATURA | Data em que foi aceito o registro de candidatura pela Justiça Eleitoral. |
| CD_SITUACAO_JULGAMENTO | Código da situação com relação ao julgamento (a partir de 2024). |
| DS_SITUACAO_JULGAMENTO | Descrição da situação com relação ao julgamento (a partir de 2024). |
| CD_SITUACAO_JULGAMENTO_PLEITO | Código da situação no dia do pleito (a partir de 2024). |
| DS_SITUACAO_JULGAMENTO_PLEITO | Descrição da situação no dia do pleito (a partir de 2024). |
| CD_SITUACAO_JULGAMENTO_URNA | Código da situação na urna (a partir de 2024). |
| DS_SITUACAO_JULGAMENTO_URNA | Descrição da situação na urna (a partir de 2024). |
| CD_SITUACAO_CASSACAO | Código da situação com relação à cassação do mandato (a partir de 2024). |
| DS_SITUACAO_CASSACAO | Descrição da situação com relação à cassação do mandato (a partir de 2024). |
| CD_SITUACAO_CASSACAO_MIDIA | Código da situação com relação à cassação no momento do fechamento do sistema (a partir de 2024). |
| DS_SITUACAO_CASSACAO_MIDIA | Descrição da situação com relação à cassação no momento do fechamento (a partir de 2024). |
| CD_SITUACAO_DCONST_DIPLOMA | Código da situação com relação ao processo de desconstituição do diploma (a partir de 2024). |
| DS_SITUACAO_DCONST_DIPLOMA | Descrição da situação com relação ao processo de desconstituição do diploma (a partir de 2024). |
| DT_DIPLOMACAO | Data da diplomação (a partir de 2018). |
| CD_SITUACAO_DIPLOMACAO | Código da situação da diplomação (a partir de 2018). |
| DS_SITUACAO_DIPLOMACAO | Descrição da situação da diplomação (a partir de 2018). |

---

## ARQUIVO 4: leiamecc.pdf

### LEIAUE - INSTRUÇÕES DE LEITURA

Este arquivo contém o leiaute das tabelas existentes no repositório de dados eleitorais. Antes de trabalhar os dados é importante ler as seguintes considerações:

- A codificação de caracteres dos arquivos é "Latin 1";
- Os campos estão entre aspas e separados por ponto e vírgula, inclusive os campos numéricos;
- Campos preenchidos com #NULO significam que a informação está em branco no banco de dados. O correspondente para #NULO nos campos numéricos é -1;
- Campos preenchidos com #NE significam que naquele ano a informação não era registrada em banco de dados pelos sistemas eleitorais. O correspondente para #NE nos campos numéricos é -3;
- O campo UF, além das unidades da federação, pode conter alguma das seguintes situações:
  - BR: quando se tratar de informação a nível nacional;
  - VT: quando se tratar de voto em trânsito;
  - ZZ: quando se tratar de Exterior.
- Os arquivos estão em constante processo de atualização e aperfeiçoamento. Alguns arquivos podem estar em branco ou com mensagem de erro devido à indisponibilidade temporária na base de algum estado ou à inexistência daquele arquivo para a época pretendida.

O § 2º do art. 33 da Resolução TSE nº 23.609/2019 estabelece que:

"Os endereços informados para atribuição de CNPJ, comunicações processuais e do Comitê Central de Campanha, telefone pessoal, e-mail pessoal, número do CPF e o documento pessoal de identificação não serão divulgados no DivulgaCandContas e serão juntados como documento sigiloso no processo de registro de candidatura no PJe. (incluído pela Resolução nº 23.729/2024)"

Portanto, são considerados como não divulgáveis, para as Eleições 2024, as informações registradas na variável NR_CPF_CANDIDATO dos arquivos de candidaturas do Portal de Dados Abertos.

Agradecemos todas as críticas e sugestões recebidas de pesquisadores e usuários que estão colaborando para a melhoria na qualidade da prestação das informações.

Qualquer sugestão ou dúvida deve ser encaminhada ao e-mail estatistica@tse.jus.br.

### CONSULTA_CAND

**NOTAÇÃO:**
- CONSULTA_CAND_<ANO>_<UF>
- CONSULTA_CAND_<ANO>_BRASIL

Para o caso de candidatos não divulgáveis, seus dados pessoais são preenchidos com:
- -4, em caso de campos numéricos, exceto campo de idade;
- "NÃO DIVULGÁVEL", em caso de campos textuais;
- "", no caso de campos relativos à idade do candidato e sua data de nascimento.

**Variáveis e Descrições:**

| Variável | Descrição |
|----------|-----------|
| DT_GERACAO | Data da extração dos dados para geração do arquivo. |
| HH_GERACAO | Hora da extração dos dados para geração do arquivo com base no horário de Brasília. |
| ANO_ELEICAO | Ano de referência da eleição para geração do arquivo. |
| CD_TIPO_ELEICAO | Código do tipo de eleição: 1 (Suplementar), 2 (Ordinária), 3 (Consulta Popular). |
| NM_TIPO_ELEICAO | Nome do tipo de eleição. |
| NR_TURNO | Número do turno da eleição. |
| CD_ELEICAO | Código único da eleição no âmbito da Justiça Eleitoral. |
| DS_ELEICAO | Descrição da eleição. |
| DT_ELEICAO | Data da ocorrência da eleição. |
| TP_ABRANGENCIA | Abrangência da eleição: M (Municipal), E (Estadual), F (Federal). |
| SG_UF | Sigla da unidade da federação na qual a candidata ou candidato concorre. |
| SG_UE | Sigla da unidade eleitoral na qual a candidata ou candidato concorre. |
| NM_UE | Nome da unidade eleitoral. |
| CD_CARGO | Código do cargo ao qual a candidata ou candidato concorre. |
| DS_CARGO | Descrição do cargo ao qual a candidata ou candidato concorre. |
| SQ_CANDIDATO | Número sequencial da candidata ou candidato. |
| NR_CANDIDATO | Número da candidata ou candidato na urna. |
| NM_CANDIDATO | Nome completo da candidata ou candidato. |
| NM_URNA_CANDIDATO | Nome da candidata ou candidato que aparece na urna. |
| NM_SOCIAL_CANDIDATO | Nome social da candidata ou candidato (identidade de gênero). |
| NR_CPF_CANDIDATO | Número do CPF da candidata ou candidato (não divulgável em 2024). |
| DS_EMAIL | Endereço de e-mail da candidata ou candidato. |
| CD_SITUACAO_CANDIDATURA | Código da situação do registro de candidatura (até 2022). |
| DS_SITUACAO_CANDIDATURA | Descrição da situação do registro de candidatura (até 2022). |
| TP_AGREMIACAO | Tipo de agremiação: Coligação, Federação, Partido Isolado. |
| NR_PARTIDO | Número do partido da candidata ou candidato. |
| SG_PARTIDO | Sigla do partido da candidata ou candidato. |
| NM_PARTIDO | Nome do partido da candidata ou candidato. |
| NR_FEDERACAO | Número da federação pela qual o partido concorre. |
| NM_FEDERACAO | Nome da federação pela qual o partido concorre. |
| SG_FEDERACAO | Sigla da federação pela qual o partido concorre. |
| DS_COMPOSICAO_FEDERACAO | Composição da federação (concatenação de siglas intercaladas com "/"). |
| SQ_COLIGACAO | Sequencial da coligação pela qual o partido concorre. |
| NM_COLIGACAO | Nome da coligação pela qual o partido concorre. |
| DS_COMPOSICAO_COLIGACAO | Composição da coligação (concatenação de siglas intercaladas com ","). |
| SG_UF_NASCIMENTO | Sigla da unidade da federação de nascimento. |
| DT_NASCIMENTO | Data de nascimento da candidata ou candidato. |
| NR_TITULO_ELEITORAL_CANDIDATO | Número do título eleitoral. |
| CD_GENERO | Código do gênero: 2 (Masculino), 4 (Feminino). |
| DS_GENERO | Descrição do gênero. |
| CD_GRAU_INSTRUCAO | Código do grau de instrução: 1 (Analfabeto), 2 (Lê e escreve), 3 (Ens. fundamental incompleto), 4 (Ens. fundamental completo), 5 (Ens. médio incompleto), 6 (Ens. médio completo), 7 (Superior incompleto), 8 (Superior completo). |
| DS_GRAU_INSTRUCAO | Descrição do grau de instrução. |
| CD_ESTADO_CIVIL | Código do estado civil: 1 (Solteiro/a), 3 (Casado/a), 5 (Viúvo/a), 7 (Separado/a judicialmente), 9 (Divorciado/a). |
| DS_ESTADO_CIVIL | Descrição do estado civil. |
| CD_COR_RACA | Código da cor/raça: 01 (Branca), 02 (Preta), 03 (Parda), 04 (Amarela), 05 (Indígena), 06 (Não Informado). |
| DS_COR_RACA | Descrição da cor/raça. |
| CD_OCUPACAO | Código da ocupação da candidata ou candidato. |
| DS_OCUPACAO | Descrição da ocupação da candidata ou candidato. |
| CD_SIT_TOT_TURNO | Código da situação de totalização no turno. |
| DS_SIT_TOT_TURNO | Descrição da situação de totalização no turno. |

---

## RESUMO GERAL

Este arquivo consolidado contém informações de 4 documentos PDF relacionados ao sistema eleitoral brasileiro:

1. **leiamebc.pdf** - Documentação sobre a tabela BEM_CANDIDATO, que armazena informações sobre bens patrimoniais declarados por candidatos.

2. **projeto-depfed-relatorio.pdf** - Proposta de projeto final de aprendizagem de máquina focado em predição de sucesso eleitoral para deputado federal, desenvolvido por Artur Garcia e Artur Saraiva da UFC.

3. **leiameccc.pdf** - Documentação sobre a tabela CONSULTA_CAND_COMPLEMENTAR, com informações complementares sobre candidatos.

4. **leiamecc.pdf** - Documentação sobre a tabela CONSULTA_CAND, com informações gerais sobre candidatos em eleições.

Todos os documentos referem-se ao Tribunal Superior Eleitoral (TSE) e ao Portal de Dados Abertos do Brasil, fornecendo especificações de estrutura de dados, codificação de caracteres (Latin 1) e orientações para uso dos arquivos eleitorais.