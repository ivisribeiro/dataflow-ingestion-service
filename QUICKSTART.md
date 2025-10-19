# 🚀 Quick Start - Airbyte em 3 Passos

## 1️⃣ Clonar o Repositório

```bash
git clone https://github.com/ivisribeiro/dataflow-ingestion-service.git
cd dataflow-ingestion-service
```

## 2️⃣ Subir o Airbyte

```bash
# Copiar variáveis de ambiente
cp .env.example .env

# Iniciar todos os serviços
docker-compose up -d

# Aguardar 2-3 minutos e ver logs
docker-compose logs -f
```

## 3️⃣ Acessar a Interface

Abra seu navegador em: **http://localhost:8000**

### Primeira Configuração

1. **Email**: Digite seu email
2. **Organization**: Nome da sua empresa
3. **Preferências**: Pode pular
4. **Pronto!** 🎉

---

## 📊 Criar Sua Primeira Conexão

### Opção 1: Testar com Dados de Exemplo

1. **Source**: Escolha "Sample Data (Faker)"
2. **Destination**: Escolha "Local JSON"
3. **Connection**: Configure e clique em "Sync now"
4. **Resultado**: Dados aparecerão em `/tmp/airbyte_local/json_data/`

### Opção 2: Conectar Banco de Dados Real

1. **Source**: PostgreSQL / MySQL / MongoDB
2. **Configure**: Host, porta, credenciais
3. **Destination**: S3 / BigQuery / Snowflake
4. **Sync**: Manual ou agendado

---

## 🛠️ Comandos Essenciais

```bash
# Ver status
docker-compose ps

# Ver logs
docker-compose logs -f

# Parar
docker-compose stop

# Reiniciar
docker-compose restart

# Limpar tudo (⚠️ apaga dados)
docker-compose down -v
```

---

## 🆘 Problemas?

### Airbyte não abre?
```bash
# Aguarde 3 minutos
# Depois reinicie
docker-compose restart webapp
```

### Porta ocupada?
```bash
# Alterar porta no docker-compose.yml
# Trocar "8000:80" por "8080:80"
```

### Sync falha?
```bash
# Ver logs do worker
docker-compose logs worker
```

---

## 📚 Próximos Passos

1. ✅ Configurar múltiplas fontes de dados
2. ✅ Agendar syncs automáticos
3. ✅ Explorar 200+ conectores disponíveis
4. ✅ Integrar com seu data warehouse

---

**Documentação Completa**: [README.md](README.md)

**Repositório**: https://github.com/ivisribeiro/dataflow-ingestion-service

