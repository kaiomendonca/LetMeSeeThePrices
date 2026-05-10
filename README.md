# LetMeSeeThePrices

# MVP — Monitor de Preços de Memória RAM

## Objetivo

Construir um sistema backend capaz de:

* coletar preços de memória RAM em lojas online
* armazenar histórico de preços
* detectar variações
* expor uma API REST
* preparar a base para futuras notificações e dashboards

---

# Escopo do MVP

## Lojas monitoradas

Inicialmente:

* KaBuM!

Expansão futura:

* Pichau
* Terabyte
* Amazon

---

# Produtos monitorados

Inicialmente apenas:

* memória RAM DDR4
* memória RAM DDR5

Exemplos:

* 16GB 3200MHz
* 32GB 6000MHz

---

# Objetivos técnicos do MVP

O MVP deve demonstrar:

* scraping HTTP
* parsing HTML
* persistência de dados
* API REST
* arquitetura organizada
* automação de coleta
* normalização de dados

---

# Stack Principal

## Backend

* Python 3.12+
* FastAPI

---

## Scraping

* httpx
* BeautifulSoup4
* lxml

---

## Banco de Dados

* PostgreSQL
* SQLAlchemy
* Alembic

---

## Scheduler

* APScheduler

---

## Ambiente

* Docker
* Docker Compose

---

# Arquitetura Inicial

```txt
[ Scheduler ]
      ↓
[ Scraper ]
      ↓
[ Normalizador ]
      ↓
[ Banco PostgreSQL ]
      ↓
[ API REST FastAPI ]
```

---

# Fluxo da Aplicação

## 1. Scheduler executa coleta

A cada intervalo definido:

* 30 minutos
* 1 hora

O scheduler chama os scrapers.

---

## 2. Scraper realiza requisição

O scraper:

* faz request HTTP
* obtém HTML da página
* extrai dados do produto

Exemplo:

* nome
* preço
* link
* disponibilidade

---

## 3. Dados são normalizados

Cada loja possui nomes diferentes.

O sistema transforma tudo em um padrão único.

Exemplo:

Entrada:

```txt
Memória Corsair Vengeance RGB 32GB DDR5 6000MHz
```

Saída:

```json
{
  "brand": "Corsair",
  "capacity": "32GB",
  "frequency": "6000MHz",
  "type": "DDR5"
}
```

---

## 4. Dados são persistidos

O sistema salva:

* produto
* preço
* data da coleta
* loja
* disponibilidade

---

## 5. API REST expõe os dados

A API permite:

* listar produtos
* consultar histórico
* consultar menor preço
* filtrar por frequência
* filtrar por tipo

---

# Estrutura de Pastas

```txt
app/
├── api/
│   ├── routes/
│   └── dependencies/
│
├── core/
│   ├── config.py
│   └── database.py
│
├── scrapers/
│   ├── base.py
│   ├── kabum.py
│   └── parser_utils.py
│
├── services/
│   ├── price_service.py
│   └── normalization_service.py
│
├── repositories/
│   └── product_repository.py
│
├── models/
│   ├── product.py
│   └── price_history.py
│
├── schemas/
│   ├── product_schema.py
│   └── history_schema.py
│
├── scheduler/
│   └── jobs.py
│
├── utils/
│   ├── logger.py
│   └── headers.py
│
└── main.py
```

---

# Modelagem Inicial

## Tabela: products

```sql
id
name
brand
capacity
frequency
memory_type
store
url
created_at
```

---

## Tabela: price_history

```sql
id
product_id
price
available
captured_at
```

---

# Endpoints Iniciais

## Listar produtos

```http
GET /products
```

---

## Histórico de preços

```http
GET /products/{id}/history
```

---

## Menor preço

```http
GET /products/{id}/lowest-price
```

---

## Coleta manual

```http
POST /scraping/run
```

---

# Estratégia de Scraping

## Primeira abordagem

Usar:

* requests assíncronos com httpx
* BeautifulSoup

Sem navegador automatizado inicialmente.

---

# Sobre Anti-bot

Muitas lojas utilizam mecanismos de proteção.

Exemplos:

* Cloudflare
* bloqueio por excesso de requests
* validação de JavaScript
* rate limiting

---

# Estratégias permitidas e comuns

## Headers realistas

Utilizar:

* User-Agent
* Accept-Language
* Referer

Sem comportamento abusivo.

---

## Intervalo entre requests

Evitar:

* múltiplas requisições por segundo

Aplicar delays.

---

## Retry com backoff

Quando houver falha:

* esperar alguns segundos
* tentar novamente

---

## Cache

Evitar coletar páginas repetidamente.

---

## Navegador automatizado

Caso necessário futuramente:

* Playwright

Usado apenas quando o site depende de JavaScript.

---

# O que evitar

Não utilizar:

* bypass agressivo de CAPTCHA
* exploração de falhas
* negação de serviço
* flood de requests

O objetivo do projeto é coleta responsável.

---

# MVP — Fases de Desenvolvimento

# Fase 1

## Estrutura base

Implementar:

* FastAPI
* PostgreSQL
* Docker Compose
* SQLAlchemy

---

# Fase 2

## Primeiro scraper

Implementar:

* scraper KaBuM!
* parser HTML
* persistência

---

# Fase 3

## Histórico de preços

Implementar:

* tabela history
* comparação de preço

---

# Fase 4

## API REST

Implementar:

* endpoints
* filtros
* paginação

---

# Fase 5

## Scheduler automático

Implementar:

* APScheduler
* coleta recorrente

---

# Evoluções Futuras

## Dashboard Web

* gráficos
* métricas
* comparação de lojas

---

## Alertas

* Discord
* Telegram
* Email

---

## Workers Assíncronos

* Celery
* Redis

---

## Observabilidade

* logs estruturados
* métricas
* monitoramento

---

# Diferenciais para currículo

Esse projeto demonstra:

* backend real
* scraping real
* arquitetura de software
* persistência de dados
* automação
* APIs REST
* tratamento de falhas
* engenharia de dados
* Docker

---

# Convenções Recomendadas

## Commits

Exemplos:

```bash
feat(scraper): add initial kabum ram scraper
```

```bash
feat(api): implement product listing endpoint
```

```bash
fix(parser): handle missing price values
```

---

# Branches

```txt
feature/kabum-scraper
feature/products-endpoint
feature/scheduler
```

---

# Próximo Passo Recomendado

Implementar primeiro:

1. Docker Compose
2. PostgreSQL
3. FastAPI
4. scraper KaBuM!
5. salvar dados no banco

Somente depois:

* histórico
* scheduler
* alertas
* dashboard
