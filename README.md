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
- **Apache Spark:** É utilizado para o processamento e transformação dos dados de posição dos ônibus armazenados nos buckets do S3.
- **Apache Hive:** O Hive é utilizado para consultas em grandes conjuntos de dados armazenados no data warehouse.
- **Presto:** Um mecanismo de consulta SQL distribuído que executa consultas interativas nos dados.
- **ElasticSearch & Kibana:** Esses componentes são usados para indexar, pesquisar e visualizar dados, ajudando os usuários a analisar tendências, identificar padrões e obter insights acionáveis a partir dos dados.

O sistema integra dados de posição de ônibus em tempo real dos sistemas **SPTrans** e **Olho Vivo** e os armazena em um data lake, onde são processados e disponibilizados para análise por meio de plataformas de visualização como o Kibana.



