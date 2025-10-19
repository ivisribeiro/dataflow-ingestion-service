# DataFlow - Microserviço de Ingestão

Microserviço de ingestão de dados usando **Airbyte** para orquestração de pipelines de dados batch.

## 📋 Visão Geral

Este repositório contém a configuração Docker para subir o Airbyte completo, permitindo conectar e ingerir dados de 200+ fontes diferentes (APIs, bancos de dados, arquivos, etc.) de forma automatizada.

### Componentes

- **Airbyte Web UI** - Interface web para configuração (porta 8000)
- **Airbyte API** - API REST para automação (porta 8001)
- **PostgreSQL** - Banco de dados para metadados (porta 5432)
- **Temporal** - Motor de workflows
- **Worker** - Executor de jobs de sincronização

## 🚀 Início Rápido

### Pré-requisitos

- Docker 20.10+
- Docker Compose 2.0+
- 4GB RAM disponível
- 10GB espaço em disco

### 1. Clonar o Repositório

```bash
git clone <repository-url>
cd dataflow-ingestion-service
```

### 2. Configurar Variáveis de Ambiente

```bash
cp .env.example .env
```

O arquivo `.env` já vem com valores padrão que funcionam out-of-the-box.

### 3. Iniciar os Serviços

```bash
docker-compose up -d
```

### 4. Aguardar Inicialização

Aguarde aproximadamente 2-3 minutos para todos os serviços ficarem prontos:

```bash
# Ver logs em tempo real
docker-compose logs -f

# Verificar status dos containers
docker-compose ps
```

### 5. Acessar Airbyte

Abra seu navegador em: **http://localhost:8000**

Na primeira vez, você verá a tela de configuração inicial:
1. Defina seu email
2. Configure o nome da organização
3. Escolha preferências (pode pular)
4. Pronto! 🎉

## 📊 Acessos

| Serviço | URL | Credenciais |
|---------|-----|-------------|
| **Airbyte Web UI** | http://localhost:8000 | - |
| **Airbyte API** | http://localhost:8001 | - |
| **PostgreSQL** | localhost:5432 | airbyte / airbyte123 |

## 🔌 Configurar Primeira Conexão

### Exemplo: Ingerir dados de uma API REST

#### 1. Criar Source (Fonte de Dados)

1. No Airbyte UI, clique em **"Sources"** → **"+ New source"**
2. Busque por **"HTTP API"** ou **"REST API"**
3. Configure:
   - **Source name**: `API de Exemplo`
   - **URL**: `https://api.exemplo.com/data`
   - **HTTP Method**: GET
4. Clique em **"Set up source"**

#### 2. Criar Destination (Destino)

1. Clique em **"Destinations"** → **"+ New destination"**
2. Busque por **"Local JSON"** (para testes) ou **"S3"** (para produção)
3. Configure conforme o destino escolhido
4. Clique em **"Set up destination"**

#### 3. Criar Connection (Conexão)

1. Clique em **"Connections"** → **"+ New connection"**
2. Selecione o **Source** e **Destination** criados
3. Configure:
   - **Replication frequency**: Manual ou Scheduled
   - **Sync mode**: Full Refresh ou Incremental
4. Clique em **"Set up connection"**

#### 4. Executar Sync

1. Na página da Connection, clique em **"Sync now"**
2. Acompanhe o progresso na aba **"Job History"**
3. Verifique os dados no destino configurado

## 🗄️ Conectores Disponíveis

O Airbyte possui **200+ conectores** prontos para uso:

### Bancos de Dados
- PostgreSQL, MySQL, MongoDB
- SQL Server, Oracle, MariaDB
- Redshift, BigQuery, Snowflake

### APIs e SaaS
- REST API, GraphQL API
- Google Sheets, Google Analytics
- Salesforce, HubSpot, Stripe
- Shopify, Zendesk, Jira

### Arquivos
- CSV, JSON, Parquet
- Excel, Google Drive
- S3, Azure Blob, GCS

### Streaming
- Kafka, Kinesis
- RabbitMQ, Pulsar

[Ver lista completa de conectores](https://docs.airbyte.com/integrations/)

## 🛠️ Comandos Úteis

### Ver status dos serviços
```bash
docker-compose ps
```

### Ver logs
```bash
# Todos os serviços
docker-compose logs -f

# Apenas webapp
docker-compose logs -f webapp

# Apenas worker
docker-compose logs -f worker
```

### Parar serviços
```bash
docker-compose stop
```

### Reiniciar serviços
```bash
docker-compose restart
```

### Parar e remover containers
```bash
docker-compose down
```

### Limpar tudo (⚠️ CUIDADO: apaga todos os dados)
```bash
docker-compose down -v
```

### Atualizar para nova versão
```bash
# Parar serviços
docker-compose down

# Atualizar imagens
docker-compose pull

# Reiniciar
docker-compose up -d
```

## 🔍 Troubleshooting

### Airbyte UI não carrega

**Problema**: Página não abre em http://localhost:8000

**Soluções**:
```bash
# 1. Verificar se todos os containers estão rodando
docker-compose ps

# 2. Ver logs do webapp
docker-compose logs webapp

# 3. Aguardar mais tempo (pode levar até 3 minutos)
# 4. Reiniciar o webapp
docker-compose restart webapp
```

### Erro "port already in use"

**Problema**: Porta 8000 ou 5432 já está em uso

**Soluções**:
```bash
# Ver o que está usando a porta
lsof -i :8000
lsof -i :5432

# Parar o processo ou alterar a porta no docker-compose.yml
# Exemplo: trocar "8000:80" por "8080:80"
```

### Sync falha com erro de conexão

**Problema**: Job de sincronização falha

**Soluções**:
1. Verificar credenciais da fonte de dados
2. Verificar conectividade de rede
3. Ver logs do worker:
   ```bash
   docker-compose logs worker
   ```
4. Testar conexão manualmente

### PostgreSQL não inicia

**Problema**: Container postgres não sobe

**Soluções**:
```bash
# Ver logs do postgres
docker-compose logs postgres

# Remover volume e recriar
docker-compose down -v
docker-compose up -d
```

## 📚 Documentação

- [Documentação Oficial Airbyte](https://docs.airbyte.com/)
- [Guia de Conectores](https://docs.airbyte.com/integrations/)
- [API Reference](https://airbyte-public-api-docs.s3.us-east-2.amazonaws.com/rapidoc-api-docs.html)
- [Tutoriais](https://docs.airbyte.com/tutorials/)

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────────────────────┐
│                    Airbyte Web UI                       │
│                   (localhost:8000)                      │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                   Airbyte Server                        │
│                  (API - port 8001)                      │
└────────────────────┬────────────────────────────────────┘
                     │
         ┌───────────┼───────────┐
         ▼           ▼           ▼
    ┌────────┐  ┌────────┐  ┌──────────┐
    │Worker  │  │Temporal│  │PostgreSQL│
    │(Jobs)  │  │(Engine)│  │(Metadata)│
    └────────┘  └────────┘  └──────────┘
         │
         ▼
    ┌────────────────────┐
    │  Data Sources      │
    │  (APIs, DBs, etc)  │
    └────────────────────┘
```

## 🚦 Status dos Serviços

Você pode verificar o status de saúde dos serviços:

```bash
# Health check do Airbyte API
curl http://localhost:8001/api/v1/health

# Verificar PostgreSQL
docker exec -it airbyte-postgres psql -U airbyte -d airbyte -c "SELECT 1;"
```

## 🔐 Segurança

### Produção

Para ambientes de produção, considere:

1. **Alterar senhas padrão** no arquivo `.env`
2. **Usar HTTPS** com certificados SSL
3. **Configurar autenticação** no Airbyte
4. **Restringir acesso** via firewall
5. **Backups regulares** do PostgreSQL

### Backup do PostgreSQL

```bash
# Fazer backup
docker exec airbyte-postgres pg_dump -U airbyte airbyte > backup.sql

# Restaurar backup
docker exec -i airbyte-postgres psql -U airbyte airbyte < backup.sql
```

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor:

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/nova-feature`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova feature'`)
4. Push para a branch (`git push origin feature/nova-feature`)
5. Abra um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT.

## 📞 Suporte

- **Issues**: Abra uma issue neste repositório
- **Documentação Airbyte**: https://docs.airbyte.com/
- **Community Slack**: https://slack.airbyte.io/

---

**DataFlow Platform** - Transformando dados em insights 🚀

