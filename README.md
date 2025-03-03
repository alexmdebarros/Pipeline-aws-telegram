# Análise de Dados do Telegram com AWS

Este projeto demonstra como coletar, processar e analisar dados de um grupo do Telegram usando a AWS, com foco em otimização e detalhes técnicos.

## Visão Geral

O objetivo deste projeto é criar um pipeline de dados robusto e eficiente para extrair insights valiosos das conversas do Telegram. O pipeline inclui as seguintes etapas:

1.  Coleta de dados do Telegram usando um bot e webhook configurado.
2.  Ingestão de dados no AWS S3 (`ebac-42-exercicio-datalake-raw`).
3.  Processamento de dados usando o AWS Lambda com Python e PyArrow para conversão em formato PARQUET.
4.  Análise de dados usando o AWS Athena com queries SQL detalhadas.
5.  Visualização de dados através de gráficos gerados a partir das queries do Athena.

## Arquitetura

![Arquitetura do Projeto](https://github.com/alexmdebarros/Pipeline-aws-telegram/blob/main/Profissao%20Analista%20de%20dados%20M42%20Material%20de%20apoio%20arch.png?raw=true')


## Componentes

* **Telegram Bot**: Coleta mensagens de um grupo do Telegram através de um webhook configurado no API Gateway.
* **AWS S3 (`ebac-42-exercicio-datalake-raw`)**: Armazena dados brutos em formato JSON.
* **AWS Lambda (Ingestão)**: Recebe e armazena mensagens do Telegram no S3.
* **AWS S3 (`ebac-42-exercicio-datalake-enriched`)**: Armazena dados enriquecidos em formato PARQUET.
* **AWS Lambda (ETL)**: Processa dados brutos, transforma-os em PARQUET e armazena no S3 enriquecido.
* **AWS Athena**: Permite consultar e analisar os dados usando SQL.
* **AWS Event Bridge**: agenda a função Lambda de ETL diariamente.

## Pré-requisitos

* Conta AWS com permissões para criar S3 buckets, Lambda functions, API Gateway e Athena tables.
* Conta do Telegram com um bot criado e adicionado a um grupo.
* Python 3.x com bibliotecas `boto3` e `PyArrow`.
* AWS CLI configurado.

## Configuração

1.  Crie um bot do Telegram usando o BotFather e adicione-o a um grupo.
2.  Crie dois buckets S3: `ebac-42-exercicio-datalake-raw` e `ebac-42-exercicio-datalake-enriched`.
3.  Crie uma função Lambda (Ingestão) com o código fornecido e configure as variáveis de ambiente (`AWS_S3_BUCKET`, `TELEGRAM_CHAT_ID`).
4.  Configure as permissões IAM para a função Lambda acessar o S3.
5.  Crie uma API no API Gateway e configure o webhook do bot para enviar mensagens via POST.
6.  Crie uma função Lambda (ETL) com o código fornecido e configure as variáveis de ambiente (`AWS_S3_BUCKET`, `AWS_S3_ENRICHED`).
7.  Configure as permissões IAM e adicione a layer PyArrow à função Lambda (ETL).
8.  Crie uma regra no EventBridge para agendar a função Lambda (ETL) diariamente.
9.  Crie uma tabela no Athena apontando para os dados no bucket `ebac-42-exercicio-datalake-enriched`.

## Como Usar

1.  Clone este repositório.
2.  Implemente o código das funções Lambda no seu ambiente AWS.
3.  Configure as variáveis de ambiente e permissões IAM.
4.  Aguarde a execução da função Lambda (ETL) pelo EventBridge ou execute manualmente.
5.  Execute as queries do Athena para analisar os dados.
6.  Visualize os resultados diretamente no Athena ou exporte para outras ferramentas.

## Consultas do Athena

* **Contagem de mensagens por dia:**

    ```sql
    SELECT context_date, count(*) FROM telegram
    GROUP BY context_date
    ORDER BY context_date;
    ```

    ![Total de mensagens por dia](https://github.com/alexmdebarros/Pipeline-aws-telegram/blob/main/total-mens-dia.png?raw=true)

* **Frequência de palavras:**

    ```sql
    SELECT word, count(*) FROM telegram, UNNEST(SPLIT(text, ' ')) AS t(word) WHERE text NOT IN ('', '') GROUP BY word ORDER BY count(*) DESC LIMIT 10;
    ```

    ![Frequência de palavras](https://github.com/alexmdebarros/Pipeline-aws-telegram/blob/main/frequencia-palavras-grafico.png?raw=true)

* **Horário com mais mensagens enviadas:**

    ```sql
    SELECT HOUR(FROM_UNIXTIME(date)) AS hora, COUNT(*) AS total_mensagens
    FROM telegram
    GROUP BY HOUR(FROM_UNIXTIME(date))
    ORDER BY total_mensagens DESC;
    ```

    ![Quantidade de mensagens por hora](https://github.com/alexmdebarros/Pipeline-aws-telegram/blob/main/qtd-mensagens-hora.png?raw=true)

## Conclusão

Este projeto demonstra um pipeline completo para coleta, processamento e análise de dados do Telegram usando serviços AWS, destacando a eficiência e escalabilidade da plataforma.

## Contribuição

Contribuições são bem-vindas! Sinta-se à vontade para enviar pull requests ou abrir issues.

## Licença

Este projeto é licenciado sob a licença MIT.

## Conecte-se comigo!

Se você se interessou por este projeto e deseja acompanhar meu trabalho, convido você a visitar meus perfis no [Github](https://github.com/alexmdebarros), [LinkedIn](https://www.linkedin.com/in/alexmdebarros) e no [Kaggle](https://www.kaggle.com/alexmartinsdebarros). Lá, compartilho meus projetos, aprendizados e insights sobre análise de dados e tecnologia.
