# Architecture Overview

## Общее описание

Этот документ описывает общую архитектуру data platform, включая потоки данных, 
компоненты системы и их взаимодействие.

## Общая архитектура

┌─────────────────────────────────────────────────────────────┐
│ DATA SOURCES │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│ │ Web │ │ Mobile │ │ APIs │ │
│ │ App │ │ App │ │ (External)│ │
│ └──────────┘ └──────────┘ └──────────┘ │
─────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────┐
│ INGESTION LAYER │
│ ──────────────────────────────────────────────────────┐ │
│ │ Apache Kafka / AWS Kinesis (Streaming) │ │
│ │ Airbyte / Fivetran (Batch) │ │
│ └──────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────┐
│ STORAGE LAYER │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│ │ Data Lake│ │ DW │ │ OLTP │ │
│ │ (S3) │ │(BigQuery)│ │ (DB) │ │
│ │ Raw Zone │ │ Silver │ │PostgreSQL│ │
│ └──────────┘ └──────────┘ └──────────┘ │
└─────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────┐
│ TRANSFORMATION LAYER │
│ ┌──────────────────────────────────────────────────────┐ │
│ │ dbt (Data Build Tool) │ │
│ │ Apache Spark / Databricks │ │
│ │ SQL / Python │ │
│ └──────────────────────────────────────────────────────┘ │
─────────────────────────────────────────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────────────┐
│ SERVING & ANALYTICS LAYER │
│ ┌──────────┐ ┌──────────┐ ┌──────────┐ │
│ │ BI │ │Monitoring│ │ ML │ │
│ │(Tableau) │ │ (Grafana)│ │(Models) │ │
│ └──────────┘ └──────────┘ └──────────┘ │
└─────────────────────────────────────────────────────────────┘


## Data Flow

### Batch Processing (ETL)

1. **Extract:** Данные извлекаются из источников каждые N часов/дней
2. **Load:** Сырые данные загружаются в Data Lake (Raw Zone)
3. **Transform:** 
   - Очистка (удаление дубликатов, обработка NULL)
   - Стандартизация (форматы дат, валют)
   - Обогащение (JOIN с другими таблицами)
4. **Load:** transformed данные в Data Warehouse (Silver/Gold layers)

### Stream Processing (Real-time)

1. **Ingest:** События отправляются в Kafka topics
2. **Process:** Spark Streaming / Flink обрабатывают поток
3. **Store:** Результаты в OLAP БД (ClickHouse) или кэш (Redis)

## Data Layers (Medallion Architecture)

### Bronze Layer (Raw)
- **Что:** Сырые данные "как есть"
- **Формат:** JSON, Avro, Parquet
- **Хранение:** S3 / MinIO
- **Retention:** 90 дней

### Silver Layer (Cleaned)
- **Что:** Очищенные и стандартизированные данные
- **Схема:** enforce schema
- **Качество:** Data quality checks
- **Хранение:** Data Warehouse

### Gold Layer (Business)
- **Что:** Агрегаты и бизнес-метрики
- **Примеры:** daily_revenue, user_retention
- **Оптимизация:** для BI инструментов

## Технологический стек

### Infrastructure
- **Orchestration:** Apache Airflow
- **Containerization:** Docker, Kubernetes
- **IaC:** Terraform

### Data Processing
- **Batch:** Apache Spark, dbt
- **Stream:** Apache Kafka, Flink
- **ETL:** Airbyte, Python

### Storage
- **Data Lake:** AWS S3 / MinIO
- **Data Warehouse:** BigQuery / Snowflake / ClickHouse
- **OLTP:** PostgreSQL

### Monitoring & BI
- **Monitoring:** Prometheus, Grafana
- **BI:** Tableau, Metabase, Superset
- **Alerting:** Alertmanager, PagerDuty

## Security

- **Authentication:** OAuth2 / JWT
- **Encryption:** TLS in transit, AES-256 at rest
- **Access Control:** RBAC (Role-Based Access Control)
- **Secrets Management:** HashiCorp Vault / AWS Secrets Manager

## Scalability

- **Horizontal Scaling:** Kubernetes auto-scaling
- **Data Partitioning:** по дате (daily partitions)
- **Caching:** Redis для частых запросов
- **CDN:** CloudFront для статики

##  Deployment

### Development
```bash
docker-compose up -d
make migrate
airflow db init

Production

terraform apply
kubectl apply -f k8s/
helm install airflow .

 Change Log
2026-07-31: Initial architecture design
TODO: Добавить диаграммы последовательности
TODO: Описать disaster recovery plan
