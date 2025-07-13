# Инструкции по развертыванию

## Предварительные требования

1. **Docker и Docker Compose** установлены на системе
2. **GitHub токен** с правами на чтение приватных репозиториев
3. **Google Sheets API credentials** для работы с таблицами

## Пошаговое развертывание

### 1. Клонирование репозитория

```bash
git clone https://github.com/mk-rn/uchet-service.git
cd uchet-service
```

### 2. Настройка переменных окружения

```bash
cp env.example .env
```

Отредактируйте `.env` файл:
```env
# GitHub токен для доступа к приватным репозиториям
GITHUB_TOKEN=your_actual_github_token

# Ветки репозиториев
UCHET_BRANCH=main
UCHET_API_BRANCH=main

# Настройки API
VITE_API_BASE_URL=http://uchet-api:8000

# Google Sheets настройки
SHEET_ID=your_actual_sheet_id

# Логирование
LOG_LEVEL=INFO
```

### 3. Настройка Google Sheets

1. Создайте проект в Google Cloud Console
2. Включите Google Sheets API
3. Создайте Service Account
4. Скачайте JSON файл с credentials
5. Переименуйте в `credentials.json` и поместите в корень проекта

### 4. Запуск приложения

```bash
# Первый запуск (сборка образов)
docker-compose up --build

# Последующие запуски
docker-compose up -d
```

### 5. Проверка работоспособности

- Frontend: http://localhost:4402
- API: http://localhost:4401
- API Docs: http://localhost:4401/docs

## Обновление приложения

```bash
# Остановка
docker-compose down

# Обновление кода
git pull

# Пересборка и запуск
docker-compose up --build -d
```

## Устранение неполадок

### Проблемы с GitHub токеном
- Убедитесь, что токен имеет права на чтение приватных репозиториев
- Проверьте, что токен не истек

### Проблемы с Google Sheets
- Проверьте правильность SHEET_ID
- Убедитесь, что Service Account имеет доступ к таблице
- Проверьте, что Google Sheets API включен

### Проблемы с Docker
- Убедитесь, что Docker и Docker Compose установлены
- Проверьте, что порты 4401 и 4402 свободны
- Попробуйте пересобрать образы: `docker-compose build --no-cache`

## Безопасность

- Никогда не коммитьте `.env` файл или `credentials.json`
- Используйте сильные пароли для GitHub токенов
- Регулярно обновляйте токены и credentials
- Используйте HTTPS для всех соединений

## Мониторинг

```bash
# Просмотр логов
docker-compose logs -f

# Статус контейнеров
docker-compose ps

# Использование ресурсов
docker stats
``` 