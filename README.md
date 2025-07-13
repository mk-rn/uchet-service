# Uchet Docker Compose

Этот проект содержит Docker Compose конфигурацию для автоматического клонирования и сборки проектов `uchet` (фронтенд) и `uchet-api` (бэкенд).

## Быстрый старт

### 1. Настройка переменных окружения

Скопируйте пример файла окружения:
```bash
cp env.example .env
```

Отредактируйте `.env` файл, указав ваш GitHub токен:
```env
# GitHub токен для доступа к приватным репозиториям
GITHUB_TOKEN=your_github_token_here

# Ветки репозиториев
UCHET_BRANCH=main
UCHET_API_BRANCH=main

# Настройки API
VITE_API_BASE_URL=http://uchet-api:8000

# Google Sheets настройки
SHEET_ID=your_google_sheet_id_here

# Логирование
LOG_LEVEL=INFO
```

### 2. Настройка Google Sheets

Создайте файл `credentials.json` с вашими Google Sheets credentials в корне проекта.

### 3. Запуск

```bash
# Запуск всех сервисов
docker-compose up --build

# Или в фоновом режиме
docker-compose up --build -d
```

### 4. Доступ к приложениям

- **Фронтенд**: http://localhost:4402
- **API**: http://localhost:4401
- **API документация**: http://localhost:4401/docs

## Структура проекта

```
.
├── docker-compose.yml      # Основной compose файл
├── Dockerfile.api         # Dockerfile для API
├── Dockerfile.frontend    # Dockerfile для фронтенда
├── env.example            # Пример переменных окружения
├── .dockerignore          # Исключения для Docker
├── credentials.json       # Google Sheets credentials (создать)
└── .env                   # Переменные окружения (создать из env.example)
```

## Переменные окружения

### Обязательные
- `GITHUB_TOKEN` - GitHub токен для доступа к приватным репозиториям
- `SHEET_ID` - ID Google Sheets таблицы

### Опциональные
- `UCHET_BRANCH` - ветка фронтенда (по умолчанию: main)
- `UCHET_API_BRANCH` - ветка API (по умолчанию: main)
- `VITE_API_BASE_URL` - URL API для фронтенда (по умолчанию: http://uchet-api:8000)
- `LOG_LEVEL` - уровень логирования (по умолчанию: INFO)

## Команды управления

```bash
# Запуск
docker-compose up --build

# Запуск в фоне
docker-compose up -d

# Остановка
docker-compose down

# Пересборка
docker-compose up --build

# Просмотр логов
docker-compose logs -f

# Просмотр логов конкретного сервиса
docker-compose logs -f uchet
docker-compose logs -f uchet-api

# Очистка
docker-compose down -v --rmi all
```

## Разработка

### Обновление кода
При изменении кода в репозиториях, пересоберите образы:
```bash
docker-compose up --build
```

### Изменение ветки
Измените переменную `UCHET_BRANCH` или `UCHET_API_BRANCH` в `.env` файле и пересоберите:
```bash
docker-compose up --build
```

### Локальная разработка
Для локальной разработки рекомендуется клонировать репозитории локально и использовать отдельные Docker Compose файлы для каждого проекта.

## Устранение неполадок

### Проблемы с клонированием
- Проверьте правильность GitHub токена
- Убедитесь, что токен имеет права на чтение приватных репозиториев

### Проблемы с API
- Проверьте наличие файла `credentials.json`
- Убедитесь, что `SHEET_ID` указан правильно

### Проблемы с фронтендом
- Проверьте, что `VITE_API_BASE_URL` указывает на правильный адрес API
- Убедитесь, что API доступен из контейнера фронтенда

### Просмотр логов
```bash
# Все сервисы
docker-compose logs

# Конкретный сервис
docker-compose logs uchet-api
docker-compose logs uchet
```

## Безопасность

- Образы запускаются от непривилегированных пользователей
- Credentials монтируются как read-only
- Используется изолированная Docker сеть

## Производительность

- Используются slim образы для уменьшения размера
- Клонирование происходит с `--depth 1` для экономии места
- Зависимости кэшируются в слоях Docker 