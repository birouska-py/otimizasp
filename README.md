# OtimizaSP

**OtimizaSP** é um sistema projetado para fornecer insights e informações com o objetivo de melhorar o sistema de transporte público na cidade de São Paulo, a maior cidade da América Latina. Ele faz isso aproveitando os dados coletados das posições dos ônibus que circulam pela cidade. Ao processar e analisar esses dados, o OtimizaSP ajuda a otimizar rotas, monitorar o desempenho e melhorar a experiência de transporte dos passageiros.

### Diagrama

O diagrama da arquitetura que representa o fluxo de dados no sistema!
<div align="center">
  <img src="./documentos/arquitetura/OtimizaSP_Solution.gif" alt="Demonstração do OtimizaSP">
</div>

## Visão Geral do Sistema

A arquitetura do OtimizaSP envolve os seguintes componentes principais:

- **NiFi (RAW e TRUSTED):** O Apache NiFi é utilizado para lidar com a ingestão de dados e o gerenciamento de fluxo. Ele se integra com sistemas externos como o **SPTrans** e a plataforma **Olho Vivo**, que fornecem dados de rastreamento de ônibus em tempo real.
- **S3 (RAW e TRUSTED):** Os buckets do S3 são utilizados para armazenar dados brutos e confiáveis ingeridos de fontes externas.
- **Apache Spark:** É utilizado para o processamento e transformação dos dados de posição dos ônibus armazenados nos buckets do S3 e envio para o ElasticSearch
- **ElasticSearch & Kibana:** Esses componentes são usados para indexar, pesquisar e visualizar dados, ajudando os usuários a analisar tendências, identificar padrões e obter insights acionáveis a partir dos dados.

O sistema integra dados de posição de ônibus em tempo real dos sistemas **SPTrans** e **Olho Vivo** e os armazena em um data lake, onde são processados e disponibilizados para análise por meio de plataformas de visualização como o Kibana.

## Serviços

O arquivo `docker-compose.yml` que está na pasta lab, define diversos serviços essenciais para o pipeline de dados da **OTMZSP**, conectados através da rede personalizada `otmzsp-network`. Abaixo estão os detalhes sobre cada serviço e instruções de acesso.

### 1. **Nifi (Apache NiFi)**

- **Função**: Utilizado para a **ingestão de dados**, automatizando fluxos de dados desde a captura até a entrega.
- **Acesso**:
  - **URL**: [http://localhost:49090](http://localhost:49090)
  - **Porta**: 9090 (mapeada para 49090 no host)
- **Configurações**:
  - Volume montado para persistência de dados e logs.
  - Fuso horário configurado para **America/Sao_Paulo**.

### 2. **MinIO (Data Lake)**

- **Função**: Solução de **armazenamento de objetos**, compatível com S3. Armazena dados brutos em `raw` e dados processados em `trusted`.
- **Acesso**:
  - **Console**: [http://localhost:49001](http://localhost:49001)
  - **Serviço de Armazenamento**: [http://localhost:49000](http://localhost:49000)
  - **Credenciais**:
    - **Usuário**: `admin`
    - **Senha**: `minioadmin`
- **Configurações**:
  - Volumes montados para persistência de dados (`/minio_data/raw` e `/minio_data/trusted`).

### 3. **Spark**

#### **Spark Master**

- **Função**: Componente **Master** do Apache Spark para processamento distribuído de dados.
- **Acesso**:
  - **URL**: [http://localhost:8080](http://localhost:8080) (UI do Spark Master)
  - **Porta**: 7077 (comunicação do Master)
- **Configurações**:
  - Fuso horário configurado para **America/Sao_Paulo**.
  - Volume montado para utilitários (`/util`).

#### **Spark Worker**

- **Função**: Componente **Worker** que executa as tarefas atribuídas pelo Spark Master.
- **Acesso**:
  - **URL**: [http://localhost:8081](http://localhost:8081) (UI do Spark Worker)
- **Configurações**:
  - Conectado ao Spark Master via URL `spark://spark-master-otmzsp:7077`.
  - Volume montado para utilitários (`/util`) e diretório de trabalho (`/home/user`).

### 4. **Jupyter**

- **Função**: Interface do **Jupyter Notebook** para desenvolvimento de notebooks PySpark.
- **Acesso**:
  - **URL**: [http://localhost:8888](http://localhost:8888)
  - **Porta**: 8888 (Jupyter), 4040 a 4043 (Monitoramento de Tarefas Spark)
- **Configurações**:
  - Volume montado para armazenamento de notebooks (`/home/jovyan/work`).
  - Sem token de autenticação para facilitar o uso.

### 5. **Elasticsearch**

- **Função**: Banco de dados para **indexação e busca** de dados estruturados e não estruturados.
- **Acesso**:
  - **URL**: [http://localhost:49200](http://localhost:49200) (API)
- **Configurações**:
  - Ambiente configurado para modo **single-node**.
  - Volume montado para persistência de dados (`/usr/share/elasticsearch/data`).

### 6. **Kibana**

- **Função**: Ferramenta de **visualização de dados** do Elasticsearch.
- **Acesso**:
  - **URL**: [http://localhost:45601](http://localhost:45601)
- **Configurações**:
  - Conectado ao Elasticsearch no endereço `http://elasticsearch-otmzsp:9200`.

## Rede

- **Rede Criada**: `otmzsp-network`
  - **Driver**: bridge
  - Conecta os serviços permitindo comunicação entre eles.

## Comandos

- **Iniciar os serviços**:
  ```bash
  docker-compose up -d
  ```
- **Parar os serviços**:
  ```bash
  docker-compose down
  ```

## Observações

- Verifique se as versões do NiFi, MinIO, Spark, Jupyter, Elasticsearch e Kibana estão configuradas corretamente no arquivo `.env`.
- O NiFi está acessível via HTTP na porta `49090`.
- O MinIO pode ser acessado tanto para o armazenamento quanto para gerenciamento via console.
- Certifique-se de que o serviço Elasticsearch esteja **saudável** antes de iniciar o Kibana.


## Análise de Dados

Foram realizadas diversas análises exploratórias e pré-processamentos com os dados disponibilizados durante o desenvolvimento do projeto. Essas análises incluíram a inspeção dos dados, detecção de padrões, e transformações visando a otimização dos fluxos de dados e a preparação para futuras integrações. No entanto, devido às limitações de tempo, não foi possível implementar todas as melhorias e conclusões dessas análises no pipeline atual.

A imagem abaixo, intitulada **percurso_linha.png**, demonstra um exemplo dos ótimos resultados que conseguimos obter, representando o percurso de uma linha de ônibus com estimativas de chegada para cada parada. Esse é um exemplo das análises que ficarão como próximos passos para serem implementadas em um futuro próximo.

<div align="center">
  <img src="./documentos/analise_dados/percurso_linha.png" alt="Protótipo de percurso">
</div>

O detalhamento das análises e as etapas realizadas podem ser conferidos no notebook interativo, disponível [neste link](https://colab.research.google.com/drive/1kZ4u6iMPOMT-crjTAy5H9D9HVyINoKzM?usp=sharing#scrollTo=HC1BtS2ruWCN).


## Dashboard

O **Dashboard** apresenta uma visão geral das posições dos ônibus em uma área específica, além de informações detalhadas sobre a quantidade de ônibus, padrões de tráfego e acessibilidade. Os dados apresentados são o que temos disponíveis até o momento da apresentação.

<div align="center">
  <img src="./documentos/dashboard/dashboard_picture.png" alt="Dashboard do OtimizaSP">
</div>

### Componentes do Dashboard:

1. **Gráfico de Barras (Posições por Período de 3 horas)**:
   Exibe a distribuição de posições de ônibus ao longo do tempo, dividida em intervalos de 3 horas. O gráfico mostra a variação do número de posições capturadas, refletindo a intensidade do tráfego de ônibus em diferentes períodos ao longo de vários dias.

2. **Total de Posições**:
   Um valor destacado no dashboard que informa o **Total de Posições** capturadas, que até o momento da apresentação é **26.669.669**. Esse número representa o volume total de registros de posições dos ônibus, acumulados durante o período de monitoramento.

3. **Distribuição de Ônibus por Sentido (Centro/Bairro)**:
   Este gráfico de pizza ilustra a divisão percentual dos ônibus em movimento, separados pelo sentido da rota (Centro ou Bairro). No exemplo, a proporção está próxima de 52,9% em direção ao **Centro** e 47,1% em direção ao **Bairro**.

4. **Acessibilidade**:
   Um gráfico de donut que destaca a acessibilidade dos ônibus monitorados. Conforme indicado, 99,91% dos ônibus são **Acessíveis**, enquanto uma pequena fração (0,09%) não o é. Isso reflete a inclusão de veículos com recursos para pessoas com mobilidade reduzida.

5. **Mapa de Calor (Heatmap) da Quantidade de Ônibus por Localização**:
   O mapa de calor mostra a concentração de ônibus em diferentes regiões, com áreas em vermelho indicando maior densidade de veículos. O mapa facilita a visualização de regiões de maior tráfego, permitindo uma análise espacial da distribuição de ônibus na região metropolitana de São Paulo e cidades próximas, como Santo André, Cotia, e Mogi das Cruzes.

Este **Dashboard** é atualizado constantemente e, até o momento da apresentação, reflete os resultados mais recentes obtidos com a análise de dados.
