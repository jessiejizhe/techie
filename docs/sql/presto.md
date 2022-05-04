# Presto

[SQL Statement Syntax](https://prestodb.io/docs/current/sql.html)

## Maths

```sql
APPROX_PERCENTILE(total_amount, 0.90) AS p90
APPROX_DISTINCT()
```

**Row Percentage**

```sql
SUM(revenue) * 100.0 / SUM(SUM(revenue)) OVER() AS pct
ROUND(SUM(revenue) * 100.0 / SUM(SUM(revenue)) OVER(), 2)AS pct
```

## Datetime

```sql
SPLIT(pickup_datetime, '/')[1] AS month
```

## Sampling

```sql
SELECT * FROM $table_name TABLESAMPLE BERNOULLI (50);
```

