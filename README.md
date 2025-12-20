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
9. Com base nos dados históricos analisados, qual tipo de investimento se mostrou mais adequado para perfis mais conservadores e mais arrojados, considerando retorno e risco?

## Plataforma e Coleta de Dados

Para a implementação do pipeline de dados deste MVP, foi utilizada a plataforma Databricks Community Edition, uma solução em nuvem gratuita amplamente adotada em contextos acadêmicos e alinhada às ferramentas abordadas na disciplina. A plataforma foi empregada em todas as etapas do trabalho, incluindo a coleta, transformação, carga e análise dos dados, possibilitando a construção de pipelines de ETL e a execução de consultas analíticas em SQL, conforme os objetivos definidos.

Os dados utilizados neste MVP foram obtidos a partir da plataforma Kaggle, por meio do dataset "Brazilian Stock Market" (https://www.kaggle.com/datasets/andrewmvd/brazilian-stock-market). Trata-se de um dataset de uso público para fins educacionais e de pesquisa.

A estratégia de coleta adotada consistiu no download direto de arquivos em formato CSV, disponibilizados publicamente pelo Kaggle. Por se tratar de um conjunto de dados já consolidado, não foi necessária a utilização de técnicas de web scraping ou a construção de robôs de coleta, garantindo conformidade com aspectos éticos e legais relacionados ao uso dos dados. Os arquivos foram inicialmente armazenados em sua forma original, preservando a estrutura fornecida pela fonte, sendo as transformações necessárias, como padronização de nomes de colunas realizadas posteriormente durante a etapa de ETL no ambiente do Databricks.

## Carga

## Modelagem
<img width="1044" height="512" alt="image" src="https://github.com/user-attachments/assets/5569cd4c-839e-4a7b-84b8-96f4d42f6394" />

**Autor:** Thaygor Gonçalves
