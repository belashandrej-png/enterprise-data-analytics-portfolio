# Service Monitoring & Metrics

##  Описание

Раздел охватывает настройку мониторинга сервисов, сбор метрик и создание 
информативных дашбордов для отслеживания здоровья систем.

## Цели раздела

- Настройка сбора метрик (Prometheus, Telegraf)
- Создание алертов на критические события
- Визуализация метрик в Grafana
- Анализ производительности систем

## Структура папки

02_service_monitoring_and_metrics/
├── configs/ # Prometheus, Grafana, Alertmanager конфиги
├── sql/ # SQL запросы для метрик
├── dashboards/ # JSON экспорты дашбордов Grafana
├── assets/ # Скриншоты, диаграммы
── README.md


## Примеры проектов (в разработке)

### 2.1 Мониторинг PostgreSQL базы данных
**Метрики:**
- Количество активных соединений
- Размер базы данных и таблиц
- Количество медленных запросов (>1s)
- Replication lag

**Инструменты:**
- Prometheus + postgres_exporter
- Grafana для визуализации
- Alertmanager для уведомлений

**SQL запрос для метрики:**
```sql
-- Количество активных соединений
SELECT count(*) as active_connections
FROM pg_stat_activity
WHERE state = 'active';

-- Топ-10 медленных запросов
SELECT query, mean_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;

2.2 Мониторинг веб-приложения
Метрики:
Response time (p50, p95, p99)
Error rate (4xx, 5xx)
RPS (requests per second)
Uptime
2.3 Business Metrics Dashboard
Метрики:
DAU/MAU (Daily/Monthly Active Users)
Conversion rate
Revenue metrics
Churn rate

Пример алерта в Prometheus:

groups:
  - name: database_alerts
    interval: 30s
    rules:
      - alert: HighConnectionCount
        expr: pg_stat_activity_count > 100
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High number of DB connections"
          description: "PostgreSQL has {{ $value }} active connections"
