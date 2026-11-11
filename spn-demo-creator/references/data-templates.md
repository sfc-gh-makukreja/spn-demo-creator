# Data Templates for CoWork Demos

## Structured Data Patterns

### Table Archetypes

**Financial/Revenue Table:**
```sql
CREATE TABLE <name> (
    id INT AUTOINCREMENT,
    period_date DATE,
    business_unit VARCHAR,
    region VARCHAR,
    product_category VARCHAR,
    revenue DECIMAL(12,2),
    cost DECIMAL(12,2),
    units_sold INT,
    target DECIMAL(12,2)
);
```
- Time grain: monthly or weekly
- Include YoY comparable periods
- Embed a revenue drop/spike in recent months for "why" analysis

**Operational/Pipeline Table:**
```sql
CREATE TABLE <name> (
    id INT AUTOINCREMENT,
    record_date DATE,
    entity_id VARCHAR,
    stage VARCHAR,
    status VARCHAR,
    start_date DATE,
    completion_date DATE,
    days_in_stage INT,
    owner VARCHAR,
    priority VARCHAR
);
```
- Track progression through stages
- Include bottlenecks (long days_in_stage) for diagnosis
- Mix of completed and in-progress records

**Customer/Market Table:**
```sql
CREATE TABLE <name> (
    id INT AUTOINCREMENT,
    date DATE,
    segment VARCHAR,
    channel VARCHAR,
    metric_name VARCHAR,
    metric_value DECIMAL(10,2),
    benchmark DECIMAL(10,2),
    competitor VARCHAR
);
```
- Include competitor comparisons
- Show market share shifts
- Include seasonal patterns

**KPI/Summary Table:**
```sql
CREATE TABLE <name> (
    period DATE,
    kpi_name VARCHAR,
    actual DECIMAL(12,2),
    target DECIMAL(12,2),
    prior_period DECIMAL(12,2),
    variance_pct DECIMAL(5,2)
);
```
- Executive-level aggregated metrics
- Clear red/green indicators (actual vs target)
- Quarter-over-quarter trends

### Data Volume Guidelines

| Table Type | Rows | Time Range |
|-----------|------|-----------|
| Transaction/detail | 500-1000 | 12-24 months |
| Monthly summary | 100-300 | 24 months |
| Weekly metrics | 200-500 | 12 months |
| Reference/dimension | 20-50 | Static |
| Documents (unstructured) | 50-250 | 12-18 months |

### Pattern Embedding Rules

1. **The "What" pattern**: A clear metric change visible in aggregations (e.g., 20-40% drop/increase in a key metric over 2-3 months)
2. **The "Why" pattern**: 2-3 contributing factors that explain the "What" (e.g., marketing spend cut + competitor entry + seasonal effect)
3. **The "How" pattern**: Data that supports specific recommendations (e.g., channel ROI differences, segment performance gaps)

### Realistic Data Tips

- Use actual product/brand names from the customer (found via web research)
- Use real geographic regions where they operate
- Scale numbers to realistic ranges (match company revenue/volume)
- Add noise/variance (not perfectly smooth trends)
- Include outliers and edge cases
- Use business-appropriate date patterns (no weekends for B2B, seasonal for retail)

### Engineering the drivers (threshold pattern)

Do not use random values for the driver columns. Draw one roll per row (0 to 99) and compare it to a threshold for each segment. This makes the target rates hold.

```sql
-- Example: outcome depends on customer segment and time slot.
-- Targets: segment A 45%, segment B 12%, baseline 20%, Monday morning 40%.
WITH raw AS (
  SELECT ROW_NUMBER() OVER (ORDER BY SEQ4()) AS id,
         UNIFORM(1, 5000, RANDOM()) AS customer_id,
         DATEADD(DAY, -UNIFORM(0, 90, RANDOM()), CURRENT_DATE()) AS event_date,
         UNIFORM(8, 17, RANDOM()) AS event_hour,
         MOD(ABS(RANDOM()), 100) AS roll
  FROM TABLE(GENERATOR(ROWCOUNT => 25000))
)
SELECT r.*,
  CASE
    WHEN c.segment = 'A' AND r.roll < 45 THEN 'Lost'
    WHEN DAYNAME(r.event_date) = 'Mon' AND r.event_hour BETWEEN 8 AND 10 AND r.roll < 40 THEN 'Lost'
    WHEN c.segment = 'B' AND r.roll < 12 THEN 'Lost'
    WHEN r.roll < 20 THEN 'Lost'
    ELSE 'Won'
  END AS outcome
FROM raw r LEFT JOIN customers c ON r.customer_id = c.customer_id;
```

Pre-compute helper columns (an age group, a day of week) so the agent can find the drivers without date maths. Keep the dimension you want to rule out (region, department, staffing) on a flat rate with no thresholds, and assign it round-robin within each driver segment (for example `MOD(ROW_NUMBER() OVER (PARTITION BY lead_time_band ORDER BY id), 6)`). Random assignment on a few hundred rows gives noisy rates, such as 18 to 32 percent, and the "no effect" act fails.

Order: create dimension tables first, load them (in parallel is fine), then load the fact tables. `USE DATABASE` before `CREATE SCHEMA`.

## Verification queries

Write one query per act and compare to the targets:
1. Overall split (outcome by count and percent)
2. Rate by the dimension to rule out (expect near-equal)
3. Rate by each driver (expect the targets)

If the numbers are far off, adjust the thresholds and reload.

## Unstructured Data Patterns

### Customer Feedback Template
```sql
CREATE TABLE <name> (
    doc_id INT AUTOINCREMENT,
    date DATE,
    source VARCHAR,          -- survey, email, support_ticket, social
    category VARCHAR,        -- product, service, delivery, pricing
    sentiment VARCHAR,       -- positive, negative, neutral
    score INT,              -- 1-10 or NPS (-100 to 100)
    author VARCHAR,
    content VARCHAR(5000),   -- The actual text for Cortex Search
    metadata VARIANT         -- Additional structured attributes
);
```

### Market Intelligence Template
```sql
CREATE TABLE <name> (
    doc_id INT AUTOINCREMENT,
    date DATE,
    doc_type VARCHAR,        -- analyst_report, news_article, competitor_update, internal_memo
    source VARCHAR,
    title VARCHAR,
    region VARCHAR,
    content VARCHAR(10000),  -- Full text for Cortex Search
    tags VARIANT
);
```

### Unstructured Content Guidelines

- Vary document length (50-500 words)
- Mix formal/informal tone based on source type
- Include specific names, dates, numbers in text
- Reference themes that correlate with structured data patterns
- For sentiment data: 60% positive, 25% negative, 15% neutral (baseline), shift to more negative in problem periods
- Include 3-5 recurring themes that the search can surface

### Cortex Search Configuration

```sql
CREATE OR REPLACE CORTEX SEARCH SERVICE <schema>.<service_name>
  ON content                    -- Main text column
  ATTRIBUTES date, category, sentiment, source   -- Filterable
  WAREHOUSE = <warehouse>
  TARGET_LAG = '1 hour'
  AS (
    SELECT doc_id, date, source, category, sentiment, content
    FROM <unstructured_table>
  );
```
