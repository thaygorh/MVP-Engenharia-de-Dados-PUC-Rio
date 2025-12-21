# MVP – Engenharia de Dados – PUC-Rio

## Objetivo

O objetivo deste MVP é projetar e implementar um pipeline de dados em nuvem para analisar e comparar o desempenho histórico de ativos financeiros do mercado brasileiro, a partir de dados públicos.

A partir dos dados históricos coletados e tratados, o trabalho tem como finalidade avaliar retorno, risco e consistência de desempenho dos diferentes tipos de investimento ao longo do tempo, oferecendo uma visão comparativa clara entre renda fixa e renda variável, com foco em apoiar a compreensão do comportamento desses ativos, especialmente por pessoas sem conhecimento prévio em investimentos.

Com base nesse objetivo, o MVP busca responder às seguintes questões:

1. Qual é o retorno médio histórico dos ativos analisados, considerando diferentes períodos de tempo (curto, médio e longo prazo)?
2. Qual é a variabilidade (volatilidade) dos retornos dos ativos ao longo do tempo e como ela se comporta em diferentes períodos, ordenando os ativos do mais estável ao mais volátil?
3. Considerando os últimos 5 anos, qual tipo de investimento (renda variável ou renda fixa) apresentou o maior retorno acumulado?
4. Considerando os últimos 5 anos, qual tipo de investimento apresentou a maior média de retorno?
5. Considerando os últimos 5 anos, qual tipo de investimento apresentou a menor volatilidade média?
6. Ao longo de todo o período histórico disponível, qual tipo de investimento apresentou maior consistência de retorno, considerando a menor variação ao longo do tempo?
7. Qual foi o melhor e o pior período (por exemplo, ano ou mês) de retorno para cada tipo de investimento ao longo do histórico disponível?
8. Com base nos dados históricos analisados, qual tipo de investimento se mostrou mais adequado para perfis mais conservadores e mais arrojados, considerando retorno e risco?

---

## Plataforma e Coleta de Dados

Para a implementação do pipeline de dados deste MVP, foi utilizada a plataforma **Databricks Community Edition**, uma solução em nuvem gratuita amplamente adotada em contextos acadêmicos e alinhada às ferramentas abordadas na disciplina.

A plataforma foi empregada em todas as etapas do trabalho, incluindo coleta, transformação, carga e análise dos dados, possibilitando a construção de pipelines de ETL e a execução de consultas analíticas em SQL, conforme os objetivos definidos.

Os dados utilizados neste MVP foram obtidos a partir da plataforma **Kaggle**, por meio do dataset **Brazilian Stock Market**  
(https://www.kaggle.com/datasets/andrewmvd/brazilian-stock-market).  
Trata-se de um dataset de uso público para fins educacionais e de pesquisa.

A estratégia de coleta adotada consistiu no **download direto de arquivos em formato CSV**, disponibilizados publicamente pelo Kaggle. Por se tratar de um conjunto de dados já consolidado, não foi necessária a utilização de técnicas de web scraping ou a construção de robôs de coleta, garantindo conformidade com aspectos éticos e legais relacionados ao uso dos dados.

Os arquivos foram inicialmente armazenados em sua forma original, preservando a estrutura fornecida pela fonte, sendo as transformações necessárias, como padronização de nomes de colunas, realizadas posteriormente durante a etapa de ETL no ambiente do Databricks.

---

## Carga

*(Seção a ser detalhada após a consolidação do pipeline de ETL no Databricks.)*

---

## Modelagem dos Dados

### Modelo de Dados

Para este MVP, foi adotado o **modelo de dados em Esquema Estrela**, amplamente utilizado em projetos de Data Warehouse por facilitar consultas analíticas, agregações e análises temporais.

A escolha desse modelo se justifica pela necessidade de:
- comparar o desempenho histórico de diferentes tipos de ativos financeiros;
- realizar análises de retorno, volatilidade e consistência ao longo do tempo;
- simplificar consultas analíticas utilizando SQL.

O Esquema Estrela é composto por **tabelas fato**, que armazenam as métricas quantitativas do negócio, e por **tabelas dimensão**, que fornecem contexto temporal e categórico para essas métricas.

---

### Diagrama Lógico

O diagrama lógico do modelo de dados foi construído de acordo com o Esquema Estrela definido e está apresentado abaixo.

<img width="1044" height="512" alt="Diagrama do Esquema Estrela" src="https://github.com/user-attachments/assets/5569cd4c-839e-4a7b-84b8-96f4d42f6394" />

O modelo é composto pelas seguintes tabelas:

**Dimensões**
- **dim_date**: dimensão temporal, utilizada para análises por data, mês e ano;
- **dim_acoes_b3**: dimensão que representa os ativos de renda variável negociados na B3.

**Fatos**
- **fact_acoes_b3**: tabela fato que armazena os preços históricos e volumes negociados das ações;
- **fact_indicadores_economicos**: tabela fato que armazena indicadores macroeconômicos utilizados para análises comparativas.

Os relacionamentos entre as tabelas seguem o padrão **1:N**, partindo das dimensões para as tabelas fato. Esse formato padroniza chaves, reduz redundância e facilita consultas analíticas consistentes, especialmente em análises temporais.

---

### Considerações sobre Renda Fixa e Indicadores Macroeconômicos

Neste MVP, não há dados de instrumentos individuais de **renda fixa** (ex.: Tesouro Direto, CDB, debêntures) no conjunto de dados utilizado.  
Como alternativa, a renda fixa é analisada de forma indireta por meio de **indicadores macroeconômicos** armazenados na tabela `fact_indicadores_economicos`, incluindo:

- **taxa_selic**: taxa básica de juros da economia brasileira;
- **ipca**: índice oficial de inflação;
- **igpm**: índice geral de preços;
- **desemprego_pnad**: taxa de desemprego.

Esses indicadores funcionam como variáveis de contexto macroeconômico e são utilizados como referência para análises comparativas com os ativos de renda variável representados no modelo pelas ações da dimensão `dim_acoes_b3`.

O indicador de desemprego foi mantido no modelo por já estar disponível no conjunto de dados e por representar um fator macroeconômico que pode impactar o comportamento do mercado e dos ativos de renda variável, possibilitando análises exploratórias complementares.

---

### Catálogo de Dados

Foi construído um **Catálogo de Dados** com o objetivo de documentar os principais atributos das tabelas da camada Gold, descrevendo seu significado, tipo de dado e domínio esperado.

#### dim_date

| Atributo | Tipo | Descrição | Domínio Esperado |
|--------|------|-----------|------------------|
| date_id | INT | Identificador único da data | > 0 |
| date | DATE | Data completa | >= 2000-01-01 e <= data atual |
| year | INT | Ano | >= 2000 |
| month | INT | Mês | 1 a 12 |
| day | INT | Dia do mês | 1 a 31 |

---

#### dim_acoes_b3

| Atributo | Tipo | Descrição | Domínio Esperado |
|--------|------|-----------|------------------|
| symbol_id | INT | Identificador único do ativo | > 0 |
| symbol | STRING | Código do ativo negociado na B3 | Texto não vazio |

---

#### fact_acoes_b3

| Atributo | Tipo | Descrição | Domínio Esperado |
|--------|------|-----------|------------------|
| date_id | INT | Chave estrangeira para dim_date | > 0 e existente em dim_date |
| symbol_id | INT | Chave estrangeira para dim_acoes_b3 | > 0 e existente em dim_acoes_b3 |
| open | DOUBLE | Preço de abertura | >= 0 |
| close | DOUBLE | Preço de fechamento | >= 0 |
| high | DOUBLE | Maior preço do período | >= 0 |
| low | DOUBLE | Menor preço do período | >= 0 |
| volume | DOUBLE | Volume negociado | >= 0 |

---

#### fact_indicadores_economicos

| Atributo | Tipo | Descrição | Domínio Esperado |
|--------|------|-----------|------------------|
| date_id | INT | Chave estrangeira para dim_date | > 0 e existente em dim_date |
| taxa_selic | DOUBLE | Taxa básica de juros | >= 0 |
| ipca | DOUBLE | Índice de inflação | >= -50 e <= 100 |
| igpm | DOUBLE | Índice Geral de Preços | >= -50 e <= 100 |
| desemprego_pnad | DOUBLE | Taxa de desemprego | >= 0 e <= 100 |

---

### Linhagem de Dados

A linhagem de dados descreve o fluxo de origem, transformação e disponibilização dos dados ao longo do pipeline implementado neste MVP.

Os dados têm origem em arquivos públicos no formato CSV, obtidos a partir da plataforma Kaggle, e percorrem as seguintes etapas no Databricks:

**Raw**
- Armazenamento dos arquivos CSV em sua forma original.
- Nenhuma transformação aplicada.

**Bronze**
- Leitura dos arquivos CSV utilizando SQL (`read_files`).
- Criação de views para acesso estruturado aos dados:
  - `bronze.acoes_b3`
  - `bronze.indicadores_economicos`
- Preservação da granularidade e estrutura original dos dados.

**Gold**
- Aplicação do modelo analítico em Esquema Estrela.
- Criação das dimensões e tabelas fato:
  - `dim_date`
  - `dim_acoes_b3`
  - `fact_acoes_b3`
  - `fact_indicadores_economicos`
- Relacionamento das tabelas fato às dimensões por meio de chaves substitutas (`date_id`, `symbol_id`).

A dimensão `dim_date` atua como ponto central de integração temporal entre as tabelas fato, permitindo análises comparativas entre dados de mercado (ações) e indicadores macroeconômicos.

### Transformações e Carga dos Dados (ETL)

Após a ingestão e organização dos dados nas camadas Raw e Bronze, foi implementado o processo de transformação e carga para a camada Gold, com o objetivo de disponibilizar dados analíticos consistentes e aderentes ao modelo em Esquema Estrela.

As principais transformações aplicadas foram:

- **Padronização temporal**:  
  Foi definido um intervalo de datas comum entre os conjuntos de dados de ações e indicadores econômicos, garantindo consistência analítica.  
  O período considerado vai de **2010-01-04** até **2025-02-17**.

- **Criação de chaves substitutas**:  
  Foram criadas chaves numéricas (`date_id`, `symbol_id`) para representar datas e ativos, reduzindo dependências de campos textuais e facilitando junções analíticas.

- **Junções entre dados e dimensões**:  
  As tabelas fato foram construídas a partir de junções entre os dados da camada Bronze e as dimensões `dim_date` e `dim_acoes_b3`, seguindo o padrão 1:N característico do Esquema Estrela.

- **Aplicação de filtros e tratamentos**:  
  Foram aplicados filtros temporais, conversão explícita de tipos de dados e padronização de nomes de colunas.  
  Campos auxiliares de rastreabilidade (`rescued_data`) foram mantidos para auditoria e validações futuras.

O resultado desse processo é a disponibilização de tabelas fato e dimensões consistentes, persistidas na camada Gold e prontas para análises analíticas e exploração dos dados.

---

### Análise de Qualidade dos Dados

A análise de qualidade dos dados foi realizada de forma incremental ao longo das camadas **Bronze** e **Gold**, com o objetivo de apoiar decisões técnicas do pipeline e garantir consistência para as análises finais.

#### Camada Bronze

Na etapa de exploração inicial dos dados brutos, foram realizadas verificações estruturais e estatísticas.

- A tabela `indices_b3` não apresentou registros após a leitura dos dados brutos.  
  O conjunto foi mantido apenas para fins de documentação, mas não foi utilizado nas etapas seguintes do MVP.
  **Evidência:**
  <img width="1832" height="403" alt="image" src="https://github.com/user-attachments/assets/bc876c4b-cf9a-4651-b395-8b06ebb4a857" />

  
- A análise exploratória dos dados de ações identificou a presença de valores iguais a zero ou negativos em colunas de preço (`open`, `high`, `low` e `close`).

- A verificação temporal permitiu identificar o intervalo comum entre os conjuntos de dados, utilizado posteriormente como referência para a carga na camada Gold.

Essas análises orientaram a definição do escopo efetivamente utilizado no MVP e a parametrização das etapas de transformação.

#### Camada Gold

Após a aplicação do modelo em Esquema Estrela, foram realizadas validações de qualidade na camada Gold, incluindo:

- **Valores nulos**:  
  Não foram identificados valores nulos nas colunas críticas das tabelas fato (`fact_acoes_b3` e `fact_indicadores_economicos`).

- **Valores inválidos em preços**:  
  Foram identificados registros com valores iguais a zero ou negativos nas colunas `open`, `high` e `low`, já presentes desde a camada Bronze e mantidos para preservar a fidelidade ao conjunto de dados original.

- **Preço de fechamento (`close`)**:  
  A coluna `close` apresentou apenas um registro inválido em todo o conjunto, motivo pelo qual foi priorizada como base principal para as análises de retorno e volatilidade deste MVP.

- **Preço ajustado (`adj_close`)**:  
  Não foram identificados valores nulos nessa coluna.

- **Consistência temporal**:  
  As tabelas fato compartilham um intervalo temporal comum entre **2010-01-04** e **2025-02-17**, garantindo coerência para análises comparativas.

As consultas detalhadas, estatísticas descritivas e validações aplicadas encontram-se documentadas no notebook do Databricks, referenciado neste repositório.


---

**Autor:** Thaygor Gonçalves
