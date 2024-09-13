
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


---

## **Configuração de Certificado e SSL para NiFi**

Para configurar a comunicação segura com a API `api.olhovivo.sptrans.com.br`, você precisará gerar um certificado e configurá-lo no NiFi. Siga os passos abaixo para garantir que o NiFi possa se comunicar com a API via HTTPS.

### **1. Gerar o Certificado**

Caso o certificado esteja inválido ou ausente, execute os seguintes comandos para obter e converter o certificado da API:

1. **Obter o Certificado:**
   ```bash
   echo | openssl s_client -connect api.olhovivo.sptrans.com.br:443 -servername api.olhovivo.sptrans.com.br | openssl x509 -outform PEM > server-cert.pem
   ```

2. **Importar o Certificado para um Keystore Java:**
   ```bash
   keytool -import -file server-cert.pem -alias mycert -keystore util/jks/truststore.jks -storepass otimizasp
   ```

   Certifique-se de que o diretório `util/jks` exista antes de executar o comando. Caso não exista, crie-o com:
   ```bash
   mkdir -p util/jks
   ```

### **2. Configurar o `StandardSSLContextService` no NiFi**

Após gerar e armazenar o certificado no `util/jks/truststore.jks`, configure o `StandardSSLContextService` no NiFi para usar o keystore gerado:

1. **Acesse a interface web do NiFi em**: [http://localhost:49090](http://localhost:49090)

2. **Adicione e configure o `StandardSSLContextService`:**
   - Navegue até a aba de serviços e adicione um novo `StandardSSLContextService`.
   - Configure as propriedades de acordo com o seu keystore:
     - **Keystore File**: `/path/to/util/jks/truststore.jks`
     - **Keystore Password**: `otimizasp`
     - **Keystore Type**: `JKS`

3. **Aplique a configuração e reinicie o serviço se necessário.**

### **Observações**

- Certifique-se de que o NiFi esteja configurado para utilizar o `StandardSSLContextService` em seus processadores que se comunicam via HTTPS.
- Mantenha o arquivo `truststore.jks` seguro e não compartilhe a senha em locais públicos.

---
