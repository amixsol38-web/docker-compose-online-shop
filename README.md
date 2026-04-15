# Online Shop — Docker Compose, Nginx, HAProxy Demo

Учебный DevOps-проект интернет-магазина, показывающий, как собрать многосервисное приложение в Docker Compose, отдать frontend через Nginx и вынести балансировку API-запросов в HAProxy.

Проект демонстрирует:
- контейнеризацию frontend и backend
- сетевое взаимодействие сервисов внутри Docker network
- health checks и автоматический рестарт контейнеров
- балансировку нагрузки между backend-репликами
- работу frontend, backend, PostgreSQL и Elasticsearch в одной compose-схеме

________________________________________
## Что есть в проекте

Проект поддерживает один режим запуска:

Docker Compose + HAProxy
- frontend работает в Nginx и отдаёт React-сборку
- запросы `/api` проксируются через HAProxy
- backend можно масштабировать несколькими репликами
- PostgreSQL хранит данные
- Elasticsearch используется для поиска товаров

________________________________________
## Архитектура

Browser
   ↓
Frontend (Nginx, :80)
   ↓ /api
HAProxy (:8080)
   ↓
Backend (Spring Boot, 1..N)
   ↓
PostgreSQL / Elasticsearch

________________________________________
## Сервисы

frontend
- React-приложение, собранное в статические файлы
- отдаётся через Nginx
- проксирует `/api` во внутренний HAProxy
- использует `try_files` для SPA-маршрутов

backend
- Spring Boot REST API
- читает товары из PostgreSQL
- использует Elasticsearch для поиска
- публикует health endpoint `/actuator/health`

haproxy
- L7 load balancer для backend-сервиса
- round-robin балансировка
- health checks backend-реплик
- обнаружение backend-контейнеров через Docker DNS

db
- PostgreSQL
- хранение данных приложения
- данные сохраняются в Docker volume

search
- Elasticsearch
- индекс товаров для поиска
- данные сохраняются в Docker volume

________________________________________
## Используемые технологии

- Docker
- Docker Compose
- HAProxy
- Nginx
- React
- Spring Boot
- PostgreSQL
- Elasticsearch
- Flyway

________________________________________
## Контейнеризация

Каждый сервис запускается в отдельном контейнере:
- backend — multi-stage Dockerfile (`Maven -> JRE`)
- frontend — сборка React-приложения и запуск через Nginx
- PostgreSQL и Elasticsearch — официальные образы

________________________________________
## Переменные окружения

Настройки базы данных берутся из `.env`:

```env
DB_NAME=shop
DB_USER=shop_user
DB_PASSWORD=shop_password
```

________________________________________
## Запуск

Собрать и запустить проект:

```bash
docker compose up -d --build
```

Запустить backend в нескольких репликах:

```bash
docker compose up -d --scale backend=2
```

Остановить проект:

```bash
docker compose down
```

Остановить проект с удалением томов:

```bash
docker compose down -v
```

________________________________________
## Доступ

- frontend: `http://localhost/`
- HAProxy API entrypoint: `http://localhost:8080/`
- HAProxy stats: `http://localhost:8404/`
- backend healthcheck: `http://localhost:8080/actuator/health`

Backend напрямую наружу не публикуется: доступ к API идёт через frontend и HAProxy.

________________________________________
## Что показывает проект

- как контейнеризировать frontend и backend
- как использовать Nginx для раздачи frontend и проксирования API
- как связать сервисы через Docker Compose network
- как использовать reverse proxy и load balancer
- как масштабировать backend в compose-окружении
- как использовать health checks для зависимых сервисов

________________________________________
## Структура репозитория

```text
.
├── backend/
├── frontend/
├── haproxy/
└── docker-compose.yaml
```
