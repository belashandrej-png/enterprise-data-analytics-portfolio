4. `03_product_analytics_and_bi/README.md`

```bash
nano 03_product_analytics_and_bi/README.md

# Product Analytics & BI

## Описание

Раздел посвящен продуктовой аналитике, построению дашбордов и визуализации 
данных для принятия бизнес-решений.

## Цели раздела

- Анализ пользовательского поведения
- Построение воронко и когорт
- Создание интерактивных дашбордов
- A/B тестирование и эксперименты

##  Структура папки

03_product_analytics_and_bi/
├── dashboards/ # BI дашборды (Tableau, Power BI, Metabase)
├── sql/ # SQL запросы для аналитики
├── notebooks/ # Jupyter notebooks с анализом
── assets/ # Скриншоты, визуализации
└── README.md


## Примеры проектов 

### 3.1 E-commerce Analytics Dashboard
**Метрики:**
- Выручка (Revenue) по дням/неделям/месяцам
- Средний чек (AOV - Average Order Value)
- Количество заказов и пользователей
- Топ товаров по продажам

**Инструменты:** Tableau / Power BI / Metabase

**SQL запрос:**
```sql
-- Daily Revenue
SELECT 
    DATE(order_date) as date,
    COUNT(DISTINCT order_id) as total_orders,
    SUM(amount) as total_revenue,
    AVG(amount) as avg_order_value
FROM orders
GROUP BY DATE(order_date)
ORDER BY date DESC;

-- Top 10 Products
SELECT 
    product_name,
    SUM(quantity) as total_sold,
    SUM(amount) as revenue
FROM order_items
GROUP BY product_name
ORDER BY revenue DESC
LIMIT 10;

# 3.2 User Cohort Analysis
Анализ:
Retention rate по когортам (D1, D7, D30)
Churn rate
LTV (Lifetime Value)
SQL запрос:

-- Cohort Retention
WITH user_cohorts AS (
    SELECT 
        user_id,
        DATE_TRUNC('month', MIN(created_at)) as cohort_month
    FROM users
    GROUP BY user_id
),
user_activity AS (
    SELECT 
        u.user_id,
        u.cohort_month,
        DATE_TRUNC('month', a.activity_date) as activity_month,
        EXTRACT(months FROM AGE(a.activity_date, u.cohort_month)) as months_since_signup
    FROM user_cohorts u
    JOIN activities a ON u.user_id = a.user_id
)
SELECT 
    cohort_month,
    months_since_signup,
    COUNT(DISTINCT user_id) as active_users
FROM user_activity
GROUP BY cohort_month, months_since_signup
ORDER BY cohort_month, months_since_signup;

# 3.3 Marketing Funnel Analysis
Этапы воронки:
Page View
Sign Up
Activation (первое действие)
Conversion (первая покупка)
Retention (повторная покупка)
# 3.4 A/B Test Analysis
Анализ:
Статистическая значимость (p-value)
Доверительные интервалы
Power analysis
Типы дашбордов
Executive Dashboard
Аудитория: C-level, менеджмент
Метрики: KPI, выручка, рост
Обновление: Daily/Weekly
Operational Dashboard
Аудитория: Operations team
Метрики: Real-time метрики, алерты
Обновление: Real-time (1-5 min)
Analytical Dashboard
Аудитория: Analysts, Data Scientists
Метрики: Детальная аналитика, drill-down
Обновление: On-demand
Требования к каждому проекту
Описание бизнес-задачи
SQL запросы для извлечения данных
Скриншоты дашбордов
Интерпретация результатов
Рекомендации на основе данных
Best Practices для дашбордов
Простота: Не более 5-9 ключевых метрик на экране
Иерархия: Важные метрики сверху/слева
Контекст: Показывай trend lines и сравнения (WoW, MoM)
Цвет: Используй цвет осмысленно (красный = плохо, зеленый = хорошо)
Интерактивность: Добавляй фильтры (дата, сегменты, регионы)
