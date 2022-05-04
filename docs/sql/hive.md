# [Hive](https://hive.apache.org/)

[Hive Language Manual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

run code: ``hive -e "[sql command]" > res.txt``

run file: ``hive -f [file_name.sql] > res.csv``

## Configurations (optional)

```sql
SET hive.cli.print.header=true;
SET mapred.reduce.tasks=10000;
SET mapreduce.job.reduces=10;
```


## Create/Delete Database

```sql
CREATE DATABASE $db_name LOCATION '/tmp/$user_name/hive/jj_db';
DROP DATABASE $db_name;         -- if $db_name is empty
DROP DATABASE $db_name CASCADE; -- if $db_name is not empty, this will drop tables inside
```

## Create/Delete Table

```sql
USE jj_db;
CREATE TABLE jj_db.tb (
	category string,
	cnt int)
partitioned by(dt string);

DROP TABLE $table_name;

ALTER TABLE table_name RENAME TO new_table_name;
```

**Create a "smaller" table***

```sql
USE jj_db;
SET mapreduce.job.reduces=10;
CREATE TABLE tmp_overlap STORED AS orc AS 
SELECT * FROM overlap
CLUSTER BY int(rand() * 10);
```

## Insert/Delete Partition

```sql
SET hive.exec.dynamic.partition.mode=nonstrict;
INSERT INTO TABLE jj_db.tb PARTITION (dt)
SELECT * FROM global_db.tb WHERE dt='20190727';

ALTER TABLE jj_db.res drop IF EXISTS PARTITION (dt='20190727');
```

## Insert/Delete Row

```sql
INSERT INTO $table (col1, col2, col3)
VALUES (val1, val2, val3);

DELETE FROM $table
WHERE $condition;
```

## Insert/Delete Column

```sql
ALTER TABLE tb ADD COLUMNS IF NOT EXISTS (category_id INT);
```
- once new column is added, we cannot add data w/o the new column
- one trick is to SELECT NULL as `category_id `
- make sure the order of columns are the same

```sql
ALTER TABLE emp REPLACE COLUMNS (name string, dept string);
```
- cannot do drop column directly but the following:
	- old table = {id, name, dept}
	- new table = {name, dept}
- not sure how it works with partition : (

## Check table

```sql
USE jj_db;
SHOW TABLES;
SHOW PARTITIONS $datatable;

SHOW CREATE TABLE $tablename;
```
organized format: `SELECT * FROM creative WHERE creative_id = 951866 \G`

## Query Examples

**IMPORTANT**: every Hive query executed against a partitioned table must have a partition filter.<br>
If you run a query that does not have a partition filter, the query will fail with the following error:

`FAILED: SemanticException [Error 30020]: Partition filter is enforced for partition tables. Partition filter is missing for Table: concat_test<your table>`


**explore data**

```sql
SELECT id, time, price
FROM db
WHERE datestamp = "2020012520"
LIMIT 30; 

SELECT id, time, price
FROM db
WHERE datestamp BETWEEN "2020012520" AND "2020012522"
LIMIT 30
```

**explode array**

```sql
SELECT COUNT(1) as cnt
FROM db.tb
LATERAL VIEW EXPLODE (tb.events.sub_events) M as sub_event
WHERE dt='${hiveconf:pre_date}'
	AND status='active' AND id=${hiveconf:id}
	AND mx3.timestamp>=${hiveconf:cutoff}
```

**use group by instead of countDistinct**

i.e. count distinct devices per publisher

```sql
SELECT A.customer_id, COUNT(1) as cnt
FROM
	(SELECT customer_id, device_id, COUNT(1)
	FROM db.tb
	WHERE datestamp='2019010100'
	GROUP BY customer_id, device_id) A
GROUP BY A.customer_id
```

**case when**

```sql
SELECT CASE
	WHEN product_id = 1 THEN 'inhouse'
	WHEN product_id = 2 THEN 'outsource'
	ELSE 'unknown' END AS src, count(1) AS cnt
FROM db.tb
WHERE dt='20220101'
GROUP BY 1
```

## Working with Variables

```sql
SET start_hour=2021060700;
SET end_hour=2021060723;

SELECT ROUND(SUM(is_true) * 100.0 / SUM(num), 2) AS imp_vlt_rate
FROM db.tb
WHERE datestamp >= '${hiveconf:start_hour}' AND datestamp <= '${hiveconf:end_hour}';
```

## Run Hive in Loop from Shell

```shell
#!/bin/sh

if [ $# -lt 2 ]
then
    echo "Usage: ./run_hive.sh start_date(YYYYMMDD) end_date(YYYYMMDD, non-inclusive)"
    exit
fi
start_date=$1
end_date=$2

while [ $start_date -lt $end_date ]; do

    echo $start_date
    hive dt=$start_hour -f generate_data.hql
    tmp="${start_date:0:8} 00:00:00"
    start_date=$(date +"%Y%m%d" -d "$tmp  1 day")
done
```

execution

```bash
./run_hive.sh 20210101 20210102
```
