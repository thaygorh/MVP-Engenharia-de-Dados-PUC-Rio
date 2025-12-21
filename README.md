# MVP – Engenharia de Dados – PUC-Rio

## Objetivo

O objetivo deste MVP é projetar e implementar um pipeline de dados em nuvem para analisar o desempenho histórico de ações negociadas na B3, a partir de dados públicos.
Com base nos dados históricos coletados e tratados, o trabalho visa avaliar o retorno, o risco e a consistência de desempenho dos ativos de renda variável ao longo do tempo, apoiando análises comparativas entre diferentes ações e períodos.
De forma complementar, utilizar indicadores macroeconômicos como variáveis de contexto, permitindo observar o comportamento dos ativos em distintos cenários econômicos.

Com base nesse objetivo, o MVP busca responder às seguintes questões:

1. Considerando os últimos 3 anos, quais são as 5 ações que apresentaram o maior retorno acumulado?
2. Considerando os últimos 12 meses, qual foi o retorno médio das ações negociadas na B3?
3. Em qual ano a inflação medida pelo IPCA foi mais elevada e em qual ano foi mais baixa?
4. Qual é a volatilidade dos retornos das ações ao longo do tempo e como ela se comporta em diferentes períodos, destacando as 5 ações mais estáveis e as 5 mais voláteis?
5. Qual é a relação entre o índice de desemprego e a taxa Selic e IPCA ao longo do tempo?

---

## Plataforma e Coleta de Dados

Para a implementação do pipeline de dados deste MVP, foi utilizada a plataforma **Databricks Community Edition**, uma solução em nuvem gratuita amplamente adotada em contextos acadêmicos e alinhada às ferramentas abordadas na disciplina.

A plataforma foi empregada em todas as etapas do trabalho, incluindo coleta, transformação, carga e análise dos dados, possibilitando a construção de pipelines de ETL e a execução de consultas analíticas em SQL, conforme os objetivos definidos.

Os dados utilizados neste MVP foram obtidos a partir da plataforma **Kaggle**, por meio do dataset **Brazilian Stock Market**  
(https://www.kaggle.com/datasets/andrewmvd/brazilian-stock-market).  
Trata-se de um dataset de uso público para fins educacionais e de pesquisa.

A estratégia de coleta adotada consistiu no **download direto de arquivos em formato CSV**, disponibilizados publicamente pelo Kaggle. Por se tratar de um conjunto de dados já consolidado, não foi necessária a utilização de técnicas de web scraping ou a construção de robôs de coleta, garantindo conformidade com aspectos éticos e legais relacionados ao uso dos dados.

Os arquivos foram inicialmente armazenados em sua forma original, preservando a estrutura fornecida pela fonte, sendo as transformações necessárias, como padronização de nomes de colunas, realizadas posteriormente durante a etapa de ETL no ambiente do Databricks.

**Evidência – Upload/armazenamento dos CSVs no Databricks**

<img width="1159" height="331" alt="image" src="https://github.com/user-attachments/assets/d98083bf-64da-4983-bc9f-3ab9744c792e" />

**Evidência – Leitura dos arquivos CSV e criação das views na camada Bronze**

<img width="401" height="711" alt="image" src="https://github.com/user-attachments/assets/2fb06360-d5bb-4e2c-ab03-f27a37037ffd" />

---

## Carga

Após a aplicação das transformações descritas na etapa de ETL, os dados foram carregados e persistidos na camada Gold do Databricks, seguindo o modelo analítico em Esquema Estrela definido neste MVP.
Como resultado desse processo, foram disponibilizadas tabelas dimensão (`dim_date` e `dim_acoes_b3`) e tabelas fato (`fact_acoes_b3` e `fact_indicadores_economicos`), armazenadas de forma persistente na plataforma e prontas para consultas analíticas e para a execução das análises apresentadas nas seções seguintes.

**Evidência – Tabelas persistidas na camada Gold (dimensões e fatos)**

<img width="947" height="614" alt="image" src="https://github.com/user-attachments/assets/3f85b8e5-4137-4cd0-99f3-80f934993afc" />

**Evidência – Verificação de consistência temporal (intervalo comum)**

<img width="427" height="242" alt="image" src="https://github.com/user-attachments/assets/8c533097-cf01-489a-8702-9e9c0a481c5b" />

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

### Indicadores Macroeconômicos como Contexto Analítico

Neste MVP, o foco principal da análise está nos ativos de renda variável, representados pelas ações negociadas na B3.

- **taxa_selic**: taxa básica de juros da economia brasileira;
- **ipca**: índice oficial de inflação;
- **igpm**: índice geral de preços;
- **inpc**: índice nacional de preços ao consumidor;
- **desemprego_pnad**: taxa de desemprego.

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

### Transformações dos Dados (ETL)

Após a ingestão e organização dos dados nas camadas Raw e Bronze, foi implementado o processo de transformação dos dados com o objetivo de adequá-los ao modelo analítico em Esquema Estrela.

As principais transformações aplicadas foram:

- **Padronização temporal**: foi definido um intervalo de datas comum entre os conjuntos de dados de ações e indicadores econômicos, garantindo consistência analítica. O período considerado vai de **2010-01-04** até **2025-02-17**.
- **Criação de chaves substitutas**: foram criadas chaves numéricas (`date_id`, `symbol_id`) para representar datas e ativos, reduzindo dependências de campos textuais e facilitando junções analíticas.
- **Junções entre dados e dimensões**: as tabelas fato foram construídas a partir de junções entre os dados da camada Bronze e as dimensões `dim_date` e `dim_acoes_b3`, seguindo o padrão 1:N característico do Esquema Estrela.
- **Aplicação de filtros e tratamentos**: foram aplicados filtros temporais, conversão explícita de tipos de dados e padronização de nomes de colunas. Campos auxiliares de rastreabilidade (`rescued_data`) foram mantidos para auditoria e validações futuras.


---

### Análise de Qualidade dos Dados

A análise de qualidade dos dados foi realizada de forma incremental ao longo das camadas **Bronze** e **Gold**, com o objetivo de apoiar decisões técnicas do pipeline e garantir consistência para as análises finais.

#### Camada Bronze

Na etapa de exploração inicial dos dados brutos, foram realizadas verificações estruturais e estatísticas.

- A tabela `indices_b3` não apresentou registros após a leitura dos dados brutos.  
  O conjunto foi mantido apenas para fins de documentação, mas não foi utilizado nas etapas seguintes do MVP.
  
**Evidência:**
<img width="1832" height="403" alt="image" src="https://github.com/user-attachments/assets/bc876c4b-cf9a-4651-b395-8b06ebb4a857" />

- A verificação temporal permitiu identificar o intervalo comum (min e max) entre os conjuntos de dados, utilizado posteriormente como referência para a carga na camada Gold.

**Estatísticas Descritivas Iniciais – Ações da B3:**

<img width="875" height="224" alt="image" src="https://github.com/user-attachments/assets/be5be937-2ba4-4e1e-88e3-4e488a10c82b" />

**Estatísticas descritivas iniciais - Indicadores Econômicos:**

<img width="693" height="251" alt="image" src="https://github.com/user-attachments/assets/16b0a22f-2ea0-431a-b35d-ee5abb57128d" />

Essas análises orientaram a definição do escopo utilizado no MVP e a parametrização das etapas de transformação.

#### Camada Gold

Após a aplicação do modelo em Esquema Estrela, foram realizadas validações de qualidade na camada Gold, incluindo:

- **Valores nulos**:  
  Não foram identificados valores nulos nas colunas críticas das tabelas fato (`fact_acoes_b3` e `fact_indicadores_economicos`).

**Evidências:**

<img width="662" height="281" alt="image" src="https://github.com/user-attachments/assets/26c8648c-4da5-4e83-8b5d-d6984bb4a5bc" />

<img width="650" height="255" alt="image" src="https://github.com/user-attachments/assets/eb4baea6-31cc-4f29-919d-17b50c50f5a8" />

- **Valores inválidos em preços**:  
Foram identificados registros com valores iguais a zero ou negativos nas colunas de preços (open, high e low). Para as análises propostas neste MVP, será priorizado o uso do preço de fechamento (close), uma vez que apresentou apenas um registro inválido em todo o conjunto de dados, reduzindo o possíveis inconsistências.

**Evidência:**

<img width="572" height="264" alt="image" src="https://github.com/user-attachments/assets/124609fd-a297-4c68-94ef-a7fd244ad7d9" />

- **Preço ajustado (`adj_close`)**:  
  Não foram identificados valores nulos nessa coluna.

**Evidência:**

<img width="542" height="246" alt="image" src="https://github.com/user-attachments/assets/4d836ba1-306e-4e7c-b383-63ae859e67df" />

- **Consistência temporal**:  
  As tabelas fato compartilham um intervalo temporal comum entre **2010-01-04** e **2025-02-17**, garantindo coerência para análises comparativas.

**Evidência:**

<img width="447" height="234" alt="image" src="https://github.com/user-attachments/assets/1f886054-38c6-4f19-a82b-cc29d9bf7e2b" />

As consultas detalhadas, estatísticas descritivas e validações aplicadas encontram-se documentadas no notebook do Databricks, referenciado neste repositório.

---

## Análises e Resultados

### Pergunta 1  
**Considerando os últimos 3 anos, quais são as 5 ações que apresentaram o maior retorno acumulado?**

### Resultado:

<img width="870" height="239" alt="image" src="https://github.com/user-attachments/assets/0676ceaf-4f96-4626-821e-7bd38757c726" />

- Período analisado: últimos 36 meses, com base na data máxima disponível (2025-02-17);
- Ativos analisado: ações negociadas na B3 (`fact_acoes_b3`);
- Métrica utilizada: retorno acumulado no período;
- Preço inicial e preço final expressos em reais (R$);
- Retorno em múltiplo (X): indica quantas vezes o preço final superou o inicial;
- Retorno percentual (%): representa a valorização total do ativo no período.

---

### Pergunta 2  
**Considerando os últimos 12 meses, qual foi o retorno médio das ações negociadas na B3?**

### Resultado:

<img width="836" height="164" alt="image" src="https://github.com/user-attachments/assets/5ee3e26a-ff60-4272-a8b8-3710f75248e5" />

- Período analisado: últimos 12 meses, com base na data máxima disponível (2025-02-17);
- Ativos analisados: ações negociadas na B3 (`fact_acoes_b3`);
- Métrica utilizada: retorno acumulado em 12 meses;
- Retorno médio (%): representa a média dos retornos individuais das ações no período;
- Retorno mediano (%): indica o retorno central da distribuição, reduzindo o impacto de outliers;
- Quantidade de ações consideradas: total de ativos com dados válidos no período analisado.

---

### Pergunta 3  
**Em qual ano a inflação medida pelo IPCA foi mais elevada e em qual ano foi mais baixa?**

### Resultado:

<img width="384" height="154" alt="image" src="https://github.com/user-attachments/assets/88960ba4-b41d-43e6-972e-6c4601e11859" />

- Indicador analisado: IPCA (`fact_indicadores_economicos`);
- Tabela de datas utilizada: `dim_date`;
- Agregação: soma anual do IPCA (valores mensais somados por ano);
- Métrica utilizada: `ipca_anual_pct` (% acumulado no ano);
- A análise permite identificar os anos com maior e menor inflação anual no período disponível na base.

---

### Pergunta 4  
**Qual é a volatilidade dos retornos das ações ao longo do tempo, destacando as 5 ações mais estáveis e as 5 mais voláteis?**

### Resultado:

<img width="820" height="285" alt="image" src="https://github.com/user-attachments/assets/7d6d032c-c8c8-4c84-8f2e-ad98d7e49f9f" />

- Período analisado: últimos 36 meses, com base na data máxima disponível;
- Ativos analisados: ações negociadas na B3 (`fact_acoes_b3`);
- Métrica utilizada: volatilidade dos retornos (desvio padrão);
- A volatilidade mede a intensidade das oscilações dos preços ao longo do tempo, independentemente da direção (alta ou queda);
- Ações menos voláteis são mais estáveis, mas também podem indicar menor variação dos retornos;
- Ações mais voláteis podem indicar maior risco e maior oscilação dos preços, podendo refletir tanto ganhos elevados quanto perdas acentuadas.

---

### Pergunta 5  
** Qual é a relação entre o índice de desemprego e a taxa Selic e IPCA ao longo do tempo?**

### Resultado:

<img width="775" height="459" alt="image" src="https://github.com/user-attachments/assets/a7f21829-c2f0-46a6-a152-31503a6261a1" />

- Período analisado: anos disponíveis na base de dados, com base na data máxima registrada;
- Indicadores analisados: IPCA, taxa Selic e desemprego (PNAD Contínua);
- Fonte dos dados: `fact_indicadores_economicos`;
- Métricas utilizadas:
- IPCA acumulado anual (%);
- Taxa Selic média anual (%);
- desemprego médio anual (%);
- A análise não indica uma relação linear consistente entre Selic, inflação e desemprego ao longo do período analisado;
- Observa-se que, em determinados anos, a Selic baixa coexistiu com níveis elevados de desemprego, enquanto em outros a Selic elevada ocorreu simultaneamente à redução do desemprego.

---
## Discussão Geral

A solução desenvolvida neste MVP demonstrou a viabilidade da construção de um pipeline de dados em nuvem capaz de integrar dados de mercado financeiro e indicadores macroeconômicos de forma
estruturada, consistente e analítica.

A utilização do modelo em Esquema Estrela possibilitou a padronização temporal dos dados, facilitando análises comparativas entre ativos de renda variável e variáveis macroeconômicas.
O pipeline implementado no Databricks, organizado nas camadas Bronze e Gold, mostrou-se apropriado para realizar a ingestão, transformação, validação e disponibilização dos dados para análise.

As análises realizadas evidenciaram diferenças significativas no desempenho das ações analisadas, com forte heterogeneidade tanto nos retornos acumulados quanto nos níveis de volatilidade ao longo do período observado. Enquanto alguns ativos apresentaram elevada valorização no intervalo analisado, outros demonstraram comportamento mais estável, com menores oscilações de preço, evidenciando perfis distintos de risco e retorno entre as ações negociadas na B3.

No contexto macroeconômico, a análise conjunta dos indicadores de inflação (IPCA), taxa Selic e desemprego indicou que essas variáveis não apresentam uma relação linear estável ao longo do tempo. Observou-se que, em determinados anos, movimentos de alta ou baixa da taxa Selic ocorreram simultaneamente a variações distintas nos níveis de inflação e desemprego, reforçando a necessidade de análises contextualizadas para interpretação do ambiente econômico.

De forma geral, os resultados obtidos permitiram responder às perguntas de negócio definidas no objetivo do MVP, evidenciando que o propósito do trabalho foi cumprido e que os conceitos abordados nas disciplinas foram aplicados de forma prática.

---

## Autoavaliação

O desenvolvimento deste MVP permitiu a aplicação prática dos conceitos de Engenharia de Dados, abrangendo desde a coleta e organização dos dados até a modelagem, carga e análise final. Os objetivos definidos no início do trabalho foram atingidos, sendo possível responder às principais perguntas de negócio propostas a partir dos dados disponibilizados.

Embora já atue profissionalmente com dados, este MVP representou minha primeira experiência prática na construção de um pipeline utilizando o Databricks, bem como na modelagem e documentação de dados no formato de esquema estrela. Ao longo do trabalho, assimilei novos conceitos de SQL aplicados a consultas analíticas, os quais poderão ser utilizados no meu dia a dia profissional.

Durante a execução do MVP, as principais dificuldades estiveram relacionadas à padronização temporal entre diferentes conjuntos de dados, à identificação de datasets aderentes ao tema definido no objetivo, ao aprendizado de novas ferramentas, como o Databricks, e à validação da qualidade dos atributos. Adicionalmente, a construção de consultas SQL mais extensas e complexas exigiu atenção especial, principalmente na organização e depuração de queries longas necessárias para responder a algumas das perguntas definidas no objetivo, demandando ajustes ao longo do desenvolvimento.

Como limitações do MVP, destaca-se a dependência de dados públicos previamente consolidados. Como trabalhos futuros, devo incluir novos indicadores, como ativos de renda fixa e fundos imobiliários, e desenvolver dashboards para apoio e facilitar a explicação.


---

**Autor:** Thaygor Gonçalves
