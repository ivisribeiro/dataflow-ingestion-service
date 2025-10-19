# 🔧 Troubleshooting - Soluções para Problemas Comuns

## ❌ Erro: "service init didn't complete successfully"

### Problema
```
✘ Container airbyte-init  service "init" didn't complete successfully: exit 1
```

### Solução
Este erro foi **CORRIGIDO** na versão mais recente. Atualize seu repositório:

```bash
# Parar containers
docker-compose down

# Atualizar código
git pull origin master

# Subir novamente
docker-compose up -d
```

**O que foi corrigido:**
- ✅ Removido container `airbyte-init` problemático
- ✅ Removida versão obsoleta do docker-compose
- ✅ Simplificadas variáveis de ambiente

---

## ⚠️ Warnings sobre variáveis de ambiente

### Problema
```
WARN[0000] The "HACK_LOCAL_ROOT_PARENT" variable is not set
WARN[0000] The "LOCAL_ROOT" variable is not set
```

### Solução
Esses warnings foram **ELIMINADOS** na versão corrigida. Todas as variáveis agora estão hardcoded no docker-compose.yml.

---

## 🔴 Porta já em uso

### Problema
```
Error: bind: address already in use
```

### Solução

**Opção 1: Parar o serviço que está usando a porta**
```bash
# Ver o que está usando a porta 8000
lsof -i :8000

# Parar o processo (substitua PID pelo número que apareceu)
kill -9 PID
```

**Opção 2: Mudar a porta no docker-compose.yml**
```yaml
# Trocar de:
ports:
  - "8000:80"

# Para:
ports:
  - "8080:80"  # Agora acesse em http://localhost:8080
```

---

## 🐌 Containers não iniciam ou ficam reiniciando

### Problema
Containers ficam em loop de restart ou não sobem

### Diagnóstico
```bash
# Ver status de todos os containers
docker-compose ps

# Ver logs de um container específico
docker-compose logs postgres
docker-compose logs server
docker-compose logs worker
```

### Soluções

**1. Aguardar mais tempo**
O Airbyte pode levar 2-3 minutos para iniciar completamente.

**2. Verificar recursos do Docker**
```bash
# Ver uso de recursos
docker stats

# Aumentar memória do Docker Desktop:
# Settings → Resources → Memory (mínimo 4GB recomendado)
```

**3. Limpar e reiniciar**
```bash
# Parar tudo
docker-compose down

# Remover volumes (⚠️ apaga dados)
docker-compose down -v

# Subir novamente
docker-compose up -d
```

---

## 🌐 Airbyte UI não carrega (página em branco)

### Problema
http://localhost:8000 não abre ou mostra página em branco

### Soluções

**1. Aguardar inicialização completa**
```bash
# Acompanhar logs do webapp
docker-compose logs -f webapp

# Aguardar mensagem: "Listening on port 80"
```

**2. Verificar se o container está rodando**
```bash
docker ps | grep webapp
```

**3. Reiniciar o webapp**
```bash
docker-compose restart webapp
```

**4. Limpar cache do navegador**
- Pressione Ctrl+Shift+R (Windows/Linux)
- Ou Cmd+Shift+R (Mac)

---

## 💾 PostgreSQL não conecta

### Problema
Erro ao conectar no banco de dados

### Solução

**1. Verificar se está rodando**
```bash
docker ps | grep postgres
```

**2. Testar conexão**
```bash
docker exec -it airbyte-postgres psql -U airbyte -d airbyte -c "SELECT 1;"
```

**3. Ver logs**
```bash
docker-compose logs postgres
```

**4. Recriar banco**
```bash
docker-compose down
docker volume rm dataflow-ingestion-service_postgres_data
docker-compose up -d
```

---

## 🔄 Sync falha ao executar

### Problema
Job de sincronização falha com erro

### Diagnóstico
```bash
# Ver logs do worker (quem executa os syncs)
docker-compose logs -f worker

# Ver logs em tempo real durante o sync
docker-compose logs -f worker server
```

### Causas Comuns

**1. Credenciais incorretas**
- Verifique usuário, senha, host da fonte de dados

**2. Firewall bloqueando**
- Verifique se o Airbyte consegue acessar a fonte
- Teste conectividade manualmente

**3. Formato de dados incompatível**
- Verifique se o schema está correto
- Tente com um subset menor de dados

**4. Falta de memória**
- Aumente memória do Docker
- Reduza batch size no sync

---

## 🧹 Limpeza Completa (Reset Total)

### Quando usar
- Quando nada mais funciona
- Para começar do zero
- Após updates que deram problema

### Comandos
```bash
# 1. Parar todos os containers
docker-compose down

# 2. Remover volumes (⚠️ APAGA TODOS OS DADOS)
docker-compose down -v

# 3. Remover imagens antigas (opcional)
docker-compose down --rmi all

# 4. Limpar sistema Docker (opcional)
docker system prune -a

# 5. Subir novamente
docker-compose up -d
```

---

## 📊 Verificar Saúde do Sistema

### Health Check Completo
```bash
# Status de todos os containers
docker-compose ps

# Uso de recursos
docker stats --no-stream

# Logs gerais
docker-compose logs --tail=50

# Testar API do Airbyte
curl http://localhost:8001/api/v1/health
```

---

## 🆘 Ainda com problemas?

### Passos finais

1. **Coletar informações**
```bash
# Versão do Docker
docker --version
docker-compose --version

# Sistema operacional
uname -a  # Linux/Mac
systeminfo  # Windows

# Logs completos
docker-compose logs > airbyte-logs.txt
```

2. **Verificar documentação oficial**
- https://docs.airbyte.com/troubleshooting

3. **Community Slack**
- https://slack.airbyte.io/

4. **Abrir Issue**
- https://github.com/airbytehq/airbyte/issues

---

## ✅ Checklist de Verificação

Antes de reportar um problema, verifique:

- [ ] Docker está rodando (`docker ps`)
- [ ] Aguardou 2-3 minutos após `docker-compose up`
- [ ] Verificou logs (`docker-compose logs`)
- [ ] Tentou reiniciar (`docker-compose restart`)
- [ ] Tentou limpar e recriar (`docker-compose down -v && docker-compose up -d`)
- [ ] Verificou portas disponíveis (`lsof -i :8000`)
- [ ] Tem recursos suficientes (4GB RAM, 10GB disco)

---

**Última atualização**: Versão corrigida sem container `init`

