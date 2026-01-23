#  Online Shop — Docker, Docker Compose, HAProxy & Docker Swarm Demo
Учебный DevOps-проект, демонстрирующий путь от одиночных контейнеров к масштабируемому и отказоустойчивому приложению.
Проект показывает:
-	контейнеризацию frontend и backend
-	сетевое взаимодействие сервисов
-	health checks и автоматический рестарт
-	балансировку нагрузки
-	переход от Docker Compose к Docker Swarm
________________________________________
## Режимы запуска
Проект поддерживает два независимых режима:
Docker Compose + HAProxy (локальная разработка и демо балансировки)
-	Явная L7-балансировка через HAProxy
-	Масштабирование backend-сервиса
-	HTTP health checks
-	Удобен для обучения и отладки
Docker Swarm (оркестрация и отказоустойчивость)
-	Встроенный DNS и load balancing Swarm
-	Масштабирование сервисов на уровне оркестратора
-	Self-healing контейнеров
-	Production-подход
________________________________________
## Цели проекта
-	Понять, как работает многосервисное приложение
-	Научиться контейнеризировать frontend и backend
-	Связать сервисы через Docker network
-	Использовать healthcheck и автоматический рестарт
-	Понять, зачем нужен load balancer
-	Перейти от Docker Compose к Docker Swarm
-	Понять принципы оркестрации контейнеров
________________________________________
## Архитектура
Docker Compose + HAProxy
Browser
   ↓
Frontend (NGINX, :80)
   ↓ /api
HAProxy (:8080)
   ↓
Backend (Spring Boot, 1..N)
   ↓
PostgreSQL / Elasticsearch

Docker Swarm
Browser
   ↓
Frontend (NGINX, :80)
   ↓ /api
Backend (Spring Boot, Swarm LB)
   ↓
PostgreSQL / Elasticsearch
В Docker Swarm отдельный HAProxy не используется,
так как балансировка и DNS встроены в сам оркестратор.
________________________________________
## Сервисы
frontend
-	Nginx
-	Отдаёт статический UI
-	Проксирует /api во внутренний backend или HAProxy
backend
-	Spring Boot REST API
-	Работает с PostgreSQL и Elasticsearch
-	HTTP healthcheck (/actuator/health)
-	Масштабируется горизонтально
haproxy (только Docker Compose)
-	L7 load balancer
-	Round-robin балансировка
-	HTTP health checks
-	Динамическое обнаружение backend-реплик через Docker DNS
db
-	PostgreSQL
-	Хранение данных приложения
-	Использует volume для сохранности данных
search
-	Elasticsearch
-	Используется для поиска товаров
________________________________________
## Используемые технологии
-	Docker
-	Docker Compose
-	Docker Swarm
-	HAProxy
-	Nginx
-	Spring Boot
-	PostgreSQL
-	Elasticsearch
________________________________________
## Контейнеризация
Каждый сервис запускается в отдельном контейнере:
-	Backend — multi-stage Dockerfile (Maven → JRE)
-	Frontend — сборка frontend + Nginx
-	PostgreSQL / Elasticsearch — официальные Docker-образы
________________________________________
## Переменные окружения
Все настройки вынесены в .env файл:
DB_NAME=shop
DB_USER=shop
DB_PASSWORD=secret
Для Docker Swarm:
set -a
source .env
set +a
________________________________________
## Docker Compose + HAProxy
Запуск
docker compose up -d --build
docker compose up -d --scale backend=2
Что даёт Docker Compose:
-	простой запуск всех сервисов
-	общая bridge-сеть контейнеров
-	явная демонстрация балансировки через HAProxy
Проверка балансировки
-	HAProxy stats page: http://<host>:8404
-	Health check backend: /actuator/health
________________________________________
## Docker Swarm (оркестрация)
Инициализация Swarm
docker swarm init
Деплой стека
docker stack deploy -c docker-stack.yml shop
Возможности Docker Swarm
-	Автоматический перезапуск контейнеров
-	Масштабирование сервисов
-	Встроенный DNS и load balancing
-	Self-healing (самовосстановление)
Пример масштабирования backend
docker service scale shop_backend=2
Если один контейнер упадёт — Swarm автоматически поднимет новый.
________________________________________
## Сеть и доступ
-	Frontend доступен на 80 порту
-	Backend не публикуется наружу
-	Все сервисы общаются через внутренние Docker-сети
-	В Swarm используется overlay-сеть
________________________________________
## Итог
Проект демонстрирует:
-	как собрать микросервисное приложение
-	как связать сервисы между собой
-	разницу между Docker Compose и Docker Swarm
-	как работает load balancing
-	как реализуется отказоустойчивость контейнеров
-	production-подход к контейнеризации
