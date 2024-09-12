
# README

Este arquivo `docker-compose.yml` define dois serviços principais: **Nifi** e **MinIO**, além de uma rede personalizada chamada `otmzsp-network`. Abaixo, explicamos os serviços e como acessá-los.

## Serviços

### 1. **Nifi (Apache NiFi)**

- **Função**: O Apache NiFi é uma ferramenta poderosa para automação de fluxos de dados, facilitando a ingestão, roteamento, transformação e entrega de dados. Neste caso, ele será utilizado como o componente de **ingestão de dados**.
  
- **Acesso**:
  - **URL**: http://localhost:49090
  - **Porta**: 9090 (mapeada para a porta 49090 no host)
  
- **Configurações**:
  - Volume montado para persistência de dados e logs (ex.: repositórios de banco de dados, arquivos de fluxo, conteúdo, etc.).
  - Definido para rodar no fuso horário de São Paulo.

### 2. **MinIO**

- **Função**: MinIO é uma solução de armazenamento de objetos compatível com o protocolo S3 da Amazon. Ele será utilizado como um **Data Lake**, onde os dados serão armazenados e categorizados em dois diretórios principais: `raw` (dados brutos) e `trusted` (dados confiáveis).

- **Acesso**:
  - **Console**: http://localhost:49001
  - **Serviço de Armazenamento**: http://localhost:49000
  - **Portas**: 
    - 9000 (serviço de armazenamento) mapeada para a porta 49000 no host
    - 9001 (console) mapeada para a porta 49001 no host
  
- **Credenciais de Acesso**:
  - **Usuário**: `admin`
  - **Senha**: `minioadmin`

- **Configurações**:
  - Diretórios de dados:
    - `/minio_data/raw` (dados brutos)
    - `/minio_data/trusted` (dados confiáveis)
  - Volumes montados para persistência dos dados.

## Rede

- **Rede Criada**: `otmzsp-network`
  - **Driver**: bridge
  - Conecta os serviços definidos, permitindo que eles se comuniquem entre si.

## Comandos

Ao utilizar o Docker Compose, você pode rodar os seguintes comandos:

- **Iniciar os serviços**:
  ```bash
  docker-compose up -d
  ```

- **Parar os serviços**:
  ```bash
  docker-compose down
  ```

## Observações

- Certifique-se de ter as versões corretas do NiFi e MinIO configuradas nas variáveis de ambiente `${NIFI_VERSION}` e `${MINIO_VERSION}` no arquivo `.env`.
- O NiFi está configurado para ser acessado via HTTP na porta 49090.
- O MinIO está disponível tanto para o armazenamento de dados quanto para gerenciamento pelo console.