# Оптимизация производительности

## Текущие проблемы

### API (uchet-api)
- ⚠️ Медленные запросы (7.46s)
- ⚠️ Предупреждения о медленных запросах
- ⚠️ Низкий процент кэширования (75-77%)

### Frontend (uchet-frontend)
- ✅ Работает стабильно
- ✅ Быстрая загрузка

## Рекомендации по оптимизации

### 1. API Оптимизация

#### Кэширование
```python
# Увеличить время кэширования
CACHE_TTL = 3600  # 1 час вместо текущего значения

# Добавить кэширование для часто запрашиваемых данных
@cache(ttl=1800)  # 30 минут
def get_categories():
    # ...
```

#### Google Sheets API
```python
# Использовать batch запросы
def get_sheet_data_batch():
    # Получать данные одним запросом
    # Разбивать на части только при необходимости
```

#### База данных (если используется)
```sql
-- Добавить индексы для часто запрашиваемых полей
CREATE INDEX idx_date ON transactions(date);
CREATE INDEX idx_category ON transactions(category);
```

### 2. Frontend Оптимизация

#### Ленивая загрузка
```javascript
// Загружать данные по мере необходимости
const loadData = async (page = 1) => {
    // ...
};
```

#### Кэширование на клиенте
```javascript
// Использовать localStorage для кэширования
const cacheData = (key, data, ttl = 1800000) => {
    localStorage.setItem(key, JSON.stringify({
        data,
        timestamp: Date.now(),
        ttl
    }));
};
```

### 3. Docker Оптимизация

#### Многоэтапная сборка
```dockerfile
# Оптимизировать размер образов
FROM node:20-alpine AS builder
# ...

FROM node:20-alpine AS production
# Копировать только необходимые файлы
```

#### Ресурсы контейнеров
```yaml
# docker-compose.yml
services:
  uchet-api:
    deploy:
      resources:
        limits:
          memory: 512M
          cpus: '0.5'
        reservations:
          memory: 256M
          cpus: '0.25'
```

### 4. Мониторинг

#### Логирование
```python
# Добавить детальное логирование
import logging

logger = logging.getLogger(__name__)
logger.info(f"Request processed in {duration:.2f}s")
```

#### Метрики
```python
# Добавить метрики производительности
from prometheus_client import Counter, Histogram

request_duration = Histogram('request_duration_seconds', 'Request duration')
request_count = Counter('request_total', 'Total requests')
```

### 5. Конфигурация

#### Environment Variables
```env
# Оптимизированные настройки
LOG_LEVEL=INFO
CACHE_TTL=3600
BATCH_SIZE=100
MAX_CONNECTIONS=10
```

#### Nginx (если используется)
```nginx
# Кэширование статических файлов
location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

## План внедрения

### Этап 1: Быстрые исправления
1. Увеличить время кэширования
2. Добавить индексы в базу данных
3. Оптимизировать запросы к Google Sheets

### Этап 2: Мониторинг
1. Добавить детальное логирование
2. Настроить метрики производительности
3. Создать дашборд мониторинга

### Этап 3: Оптимизация
1. Внедрить batch запросы
2. Оптимизировать Docker образы
3. Добавить кэширование на клиенте

## Команды для проверки

```bash
# Проверка производительности API
curl -w "@curl-format.txt" -o /dev/null -s "http://localhost:4401/api/v1/sheets/categories"

# Мониторинг контейнеров
docker stats uchet-api uchet-frontend

# Проверка логов
docker logs uchet-api --tail 50 | grep "SLOW\|WARNING"

# Тест нагрузки (если установлен ab)
ab -n 100 -c 10 http://localhost:4401/api/v1/sheets/categories
```

## Файл curl-format.txt
```
     time_namelookup:  %{time_namelookup}\n
        time_connect:  %{time_connect}\n
     time_appconnect:  %{time_appconnect}\n
    time_pretransfer:  %{time_pretransfer}\n
       time_redirect:  %{time_redirect}\n
  time_starttransfer:  %{time_starttransfer}\n
                     ----------\n
          time_total:  %{time_total}\n
``` 