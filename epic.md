# Epic 1 - Foundation

Objetivo:

> Ter uma API rodando localmente e persistindo notificações.

## CARD-001 - Criar estrutura inicial do projeto

### Descrição

Criar o esqueleto do projeto utilizando FastAPI.

### Critérios de aceite

* FastAPI configurado
* Poetry configurado
* Dockerfile criado
* Docker Compose criado
* Endpoint `/health`

---

## CARD-002 - Configuração centralizada

### Descrição

Implementar gerenciamento de configurações via env.

### Critérios de aceite

* `.env.example`
* Settings utilizando Pydantic Settings
* Validação de variáveis obrigatórias

---

## CARD-003 - Configurar SQLite

### Descrição

Adicionar persistência utilizando SQLite.

### Critérios de aceite

* SQLAlchemy configurado
* Migration inicial
* Banco criado automaticamente

---

# Epic 2 - Notification Core

Objetivo:

> Receber notificações e armazená-las.

## CARD-004 - Criar entidade Notification

### Campos

```text
id
channel
recipient
payload
status
retry_count
created_at
updated_at
```

### Critérios de aceite

* Model criado
* Migration criada

---

## CARD-005 - Endpoint de criação de notificações

### Endpoint

```http
POST /api/v1/notifications
```

### Payload

```json
{
  "channel": "email",
  "recipient": "john@example.com",
  "payload": {
    "subject": "Welcome",
    "body": "Hello"
  }
}
```

### Critérios de aceite

* Registro salvo no banco
* Status inicial = pending

---

## CARD-006 - Consultar notificação

### Endpoint

```http
GET /api/v1/notifications/{id}
```

### Critérios de aceite

* Retornar dados completos
* Retornar status atual

---

# Epic 3 - Idempotência

Objetivo:

> Evitar notificações duplicadas.

## CARD-007 - Criar tabela de idempotência

### Campos

```text
key
notification_id
expires_at
```

### Critérios de aceite

* Migration criada
* Repository implementado

---

## CARD-008 - Implementar header Idempotency-Key

### Exemplo

```http
POST /notifications

Idempotency-Key: abc123
```

### Critérios de aceite

* Não criar registros duplicados
* Retornar notificação existente

---

## CARD-009 - Job de limpeza de chaves expiradas

### Critérios de aceite

* Executar periodicamente
* Remover registros expirados

---

# Epic 4 - Worker

Objetivo:

> Processar notificações assíncronas.

## CARD-010 - Criar Worker

### Descrição

Implementar processo responsável por buscar notificações pendentes.

### Critérios de aceite

* Executar continuamente
* Buscar lote configurável

---

## CARD-011 - Atualizar status da notificação

### Fluxo

```text
pending
processing
sent
failed
```

### Critérios de aceite

* Atualização correta dos estados

---

# Epic 5 - Email Provider

Objetivo:

> Enviar e-mails reais.

## CARD-012 - Criar interface EmailProvider

### Critérios de aceite

```python
class EmailProvider:
    async def send(...)
```

---

## CARD-013 - Implementar SMTP Provider

### Critérios de aceite

* Envio funcional via SMTP

### Motivação

Permite testes sem AWS.

---

## CARD-014 - Implementar SES Provider

### Critérios de aceite

* Utilizar boto3
* Provider configurável via env

---

## CARD-015 - Seleção dinâmica de provider

### Exemplo

```env
EMAIL_PROVIDER=ses
```

### Critérios de aceite

* Factory implementada

---

# Epic 6 - Retry

Objetivo:

> Recuperar falhas temporárias.

## CARD-016 - Adicionar retry_count

### Critérios de aceite

* Persistir tentativas

---

## CARD-017 - Implementar backoff exponencial

### Estratégia

```text
1 min
5 min
15 min
30 min
```

### Critérios de aceite

* Retry automático

---

## CARD-018 - Limite máximo de tentativas

### Critérios de aceite

```env
MAX_RETRIES=5
```

---

# Epic 7 - Observabilidade

Objetivo:

> Facilitar troubleshooting.

## CARD-019 - Logs estruturados

### Exemplo

```json
{
  "notification_id": "...",
  "provider": "ses",
  "status": "sent"
}
```

### Critérios de aceite

* JSON logs

---

## CARD-020 - Correlation ID

### Critérios de aceite

* Gerado automaticamente
* Propagado nos logs

---

## CARD-021 - Métricas Prometheus

### Métricas

```text
notifications_sent_total

notifications_failed_total

notifications_pending_total
```

---

# Epic 8 - Docker Experience

Objetivo:

> Usuário conseguir rodar em menos de 5 minutos.

## CARD-022 - Criar .env.example

### Critérios de aceite

* Todas as variáveis documentadas

---

## CARD-023 - Docker Compose funcional

### Comando

```bash
docker compose up -d
```

### Critérios de aceite

* API sobe corretamente

---

## CARD-024 - Persistência de dados

### Critérios de aceite

* Volume configurado

---

# Epic 9 - Open Source

Objetivo:

> Tornar o projeto atraente para contribuidores.

## CARD-025 - Criar README

### Conteúdo

* Instalação
* Configuração
* Arquitetura
* Roadmap

---

## CARD-026 - Adicionar licença MIT

### Critérios de aceite

* LICENSE criada

---

## CARD-027 - Configurar GitHub Actions

### Pipeline

```text
lint
tests
build
```

### Critérios de aceite

* Pipeline executando automaticamente

---

# Epic 10 - Release v1.0.0

Objetivo:

> Primeira versão pública.

## CARD-028 - Criar documentação OpenAPI

### Critérios de aceite

* Swagger funcional

---

## CARD-029 - Publicar imagem Docker

### Critérios de aceite

* GitHub Container Registry

---

## CARD-030 - Criar release v1.0.0

### Critérios de aceite

* Tag criada
* Changelog publicado

---