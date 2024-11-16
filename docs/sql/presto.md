# Presto

[SQL Statement Syntax](https://prestodb.io/docs/current/sql.html)


## Aggregate Functions

```sql
SUM(revenue) * 100.0 / SUM(SUM(revenue)) OVER() AS pct
ROUND(SUM(revenue) * 100.0 / SUM(SUM(revenue)) OVER(), 2)AS pct


SUM(1) FILTER(
    WHERE
        time_span=1 AND is_active=1
) AS active_1d_cnt


APPROX_DISTINCT()
APPROX_DISTINCT(vpv_grouping_key, 0.0040625) AS cnt_distinct


APPROX_PERCENTILE(total_amount, 0.90) AS p90
```

## Data Wrangling
```sql
-- Dedupe
ARBITRARY(age_bucket) AS age_bucket


-- remove prefix
REPLACE(enum.k, 'AD_OPTIMIZATION_GOAL_', '') AS opt_goal
SPILT_PART(event_type_str, '.', 1) AS conv_type
REGEX_PLACE(str, 'hi {{user_name}}.*', '')


-- map_keys(x(K,V)) -> array(K)
map_keys(CAST(JSON_PARSE(conv_pred_map) AS MAP<varchar, double>))


--set_agg(x) -> array<[same as input]>
-- returns an array created from the distinct input x elements
SET_AGG(X)


-- [990, 2, 20 ...]
JSON_EXTRACT(ad_product_types_json, '$.1') -- returns 2, the second value
JSON_EXTRACT(ad_product_types_json, '$[1]') -- returns 2, the second value


-- parse json to array
CAST(JSON_PARSE(ad_product_types_json) AS ARRAY<VARCHAR>)
CONTAINS(CAST(JSON_PARSE(ad_product_types_json) AS ARRAY<VARCHAR>), '4') AS is_video


-- split_to_map(string, entryDelimiter, keyValueDelimiter) -> map<varchar, varchar>
-- populates xxxxx from varchar aggrid:xxxxx, actionType:yyyyy
SPLIT_TO_MAP(aggrid_touchpoints[1], ',', ':')['aggrid']


FILTER(
    CAST(
        JSON_EXTRACT(join_key_to_store, '$.joinKeyVector')
        AS ARRAY<MAP<VARCHAR, VARCHAR>>
    ),
    x -> x['joinKeyType'] = '3'
)[1]['hashedKey'] AS install_id
```


## Datetime
```sql
SPLIT(pickup_datetime, '/')[1] AS month
```

## Sampling
```sql
-- 10% random
SELECT *
FROM $table_name
    TABLESAMPLE BERNOULLI (10)
WHERE ds = '<DATEID>';
```

## Rank
```sql
WITH
tmp AS (
    SELECT
        session_id,
        imp_log_time,
        page_type,
        imp,
        rev
    FROM imp_table
    WHERE
        session_id IS NOT NULL
),
agg AS (
    SELECT
        session_id,
        page_type,
        RANK() OVER(
            PARTITION BY
                session_id
            ORDER BY
                imp_log_time
        ) AS global_position,
        bbr,
        imp
    FROM tmp
)
SELECT
    page_type,
    global_position,
    SUM(imp) AS imp,
    SUM(rev) AS rev
FROM agg
GROUP BY 1, 2
ORDER BY 1, 2 
```