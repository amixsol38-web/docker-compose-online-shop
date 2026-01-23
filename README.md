#  Online Shop — Docker & Docker Swarm Demo

Учебный DevOps-проект, показывающий путь от обычного Docker до Docker Swarm.  
Проект демонстрирует контейнеризацию, оркестрацию, сетевое взаимодействие сервисов и самовосстановление приложений.

## Проект поддерживает два режима запуска:

- Docker compose для локальной разработки
- Docker Swarm для оркестрации и отказоустойчивости

##  Цель проекта

- Понять, как работает многосервисное приложение
- Научиться контейнеризировать frontend и backend
- Связать сервисы через Docker network
- Использовать healthcheck и автоматический рестарт
- Перейти от docker-compose к Docker Swarm
- Понять, зачем нужна оркестрация контейнеров

##  Архитектура проекта

Пользователь (Browser)
↓
Frontend (NGINX, :80)
↓ /api
Backend (Spring Boot, :8080)
↓
PostgreSQL Elasticsearch

## Сервисы

### frontend

- Nginx
- Отдаёт статический UI
- Проксирует запросы `/api` в backend

### backend

- Spring Boot REST API
- Работает с PostgreSQL и Elasticsearch
- Имеет healthcheck для оркестратора

### db

- PostgreSQL
- Хранит данные магазина

### search

- Elasticsearch
- Используется для поиска товаров

##  Используемые технологии

- Docker
- Docker Compose
- Docker Swarm
- Nginx
- Spring Boot
- PostgreSQL
- Elasticsearch

##  Контейнеризация

Каждый сервис запускается в отдельном контейнере:

- **Backend** — multi-stage Dockerfile (Maven → JRE)
- **Frontend** — сборка frontend + Nginx
- **PostgreSQL** и **Elasticsearch** — официальные Docker-образы

##  Переменные окружения

Все настройки вынесены в `.env` файл:

DB_NAME=shop
DB_USER=shop
DB_PASSWORD=secret

###  Для Docker Swarm переменные загружаются в shell перед деплоем:

set -a
source .env
set +a
## Docker Compose (локальная разработка)

Для локального запуска используется Docker Compose:
- docker compose up -d --build
Что даёт Docker Compose:
- простой запуск всех сервисов
- общая сеть контейнеров
- удобен для разработки и отладки

## Docker Swarm (оркестрация)

Docker Swarm используется для демонстрации production-подхода.

Инициализация Swarm
- docker swarm init
- Деплой стека
- docker stack deploy -c docker-stack.yml shop

## Возможности Docker Swarm

- Автоматический перезапуск контейнеров при падении
- Масштабирование сервисов
- Встроенный DNS и load balancing
- Self-healing (самовосстановление)

Пример масштабирования backend:

docker service scale shop_backend=2
Если один контейнер упадёт — Swarm автоматически поднимет новый.

## Сеть и доступ

- Frontend доступен на 80 порту
- Backend не публикуется наружу
- Все сервисы общаются через overlay-сеть Docker Swarm

## Итог
Проект демонстрирует:
- как собрать микросервисное приложение
- как связать сервисы между собой
- зачем нужен Docker Swarm
- как работает отказоустойчивость контейнеров