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
CREATE TABLE jj_db.bid_imp_daily (
	region string,
	media_type string,
	region_id int,
	country_id int,
	postal_code_id int,
	layout_id int,
	publisher_id bigint,
	app_tld string,
	responses int,
	advertiser_cost double,
	inventory_cost double,
	impression int)
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
INSERT INTO TABLE jj_db.bid_imp_daily PARTITION (dt)
SELECT * FROM supply_scoring.bid_imp_daily WHERE dt='20190727';

ALTER TABLE jj_db.vec drop IF EXISTS PARTITION (dt='20190727');
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
ALTER TABLE bid_imp_daily ADD COLUMNS IF NOT EXISTS (request_publisher_id INT);
```
- once new column is added, we cannot add data w/o the new column
- one trick is to SELECT NULL as `request_publisher_id `
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
SELECT ad_call_id, ad_call_time, bid_price
FROM bid_sample
WHERE datestamp = "2020012520"
LIMIT 30; 

SELECT ad_call_id, ad_call_time, bid_price
FROM bid_sample
WHERE datestamp BETWEEN "2020012520" AND "2020012522"
LIMIT 30
```

**explode array**

```sql
SELECT COUNT(1) as mx3_clk_event
FROM urs_prd.tgt_gup
LATERAL VIEW EXPLODE (tgt_gup.mx3_events.mx3_click_events) M as mx3
WHERE dt='${hiveconf:pre_date}'
	AND status='active' AND id=${hiveconf:id}
	AND mx3.timestamp>=${hiveconf:cutoff}
```

**use group by instead of countDistinct**

i.e. count distinct devices per publisher

```sql
SELECT A.publisher_id, COUNT(1) as device_cnt
FROM
	(SELECT publisher_id, device_id, COUNT(1)
	FROM kite_prod.bid_sample
	WHERE datestamp='2019010100'
	GROUP BY publisher_id, device_id) A
GROUP BY A.publisher_id
```

**case when**

```sql
SELECT CASE
	WHEN layout_id = 19 THEN 'video'
	WHEN layout_id = 78 THEN 'native_display'
	WHEN layout_id = 122 THEN 'native_video'
	WHEN layout_id = 148 THEN 'vertical_video'
	ELSE 'display' END as media_type, count(1) as bid_req
FROM kite_prod.bid_sample
WHERE datestamp='2019010100'
GROUP BY CASE
	WHEN layout_id = 19 THEN 'video'
	WHEN layout_id = 78 THEN 'native_display'
	WHEN layout_id = 122 THEN 'native_video'
	WHEN layout_id = 148 THEN 'vertical_video'
	ELSE 'display' END
```

## Working with Variables

```sql
SET start_hour=2021060700;
SET end_hour=2021060723;

SELECT ROUND(SUM(is_fcap_imp_violation) * 100.0 / SUM(impressions), 2) AS imp_vlt_rate
FROM kite_prod.revenue_fact_orc
WHERE impressions = 1 AND revenue_valid = 1
AND datestamp >= '${hiveconf:start_hour}' AND datestamp <= '${hiveconf:end_hour}';
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

## [Transfer data across Grid](http://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

**step 1 (PhazonTan)**

Get the table schema and location from the original table

```sql
SHOW CREATE TABLE original_db.transfer_table;
```

**step 2 (JetBlue)**

Copy table (with the partitions) to the new location

```bash
hadoop distcp hdfs://phazontan-nn1.tan.ygrid.yahoo.com:8020/projects/advserving_pbp/prod/dspp_metrics/original_db/transfer_table/dt=20191001/hr=2019100100 /tmp/jessiej/datacopy/transfer_table/dt=20191001/hr=2019100100
```

**step 3 (JetBlue)**

```sql
USE jj_db;

CREATE TABLE `transfer_table`(
  `region` string,
  `media_type` string,
  `entity_type` string,
  `entity_value` string,
  `score` double)
PARTITIONED BY (
  `dt` string,
  `hr` string)
ROW FORMAT SERDE
  'org.apache.hadoop.hive.ql.io.orc.OrcSerde'
STORED AS INPUTFORMAT
  'org.apache.hadoop.hive.ql.io.orc.OrcInputFormat'
OUTPUTFORMAT
  'org.apache.hadoop.hive.ql.io.orc.OrcOutputFormat'
LOCATION
  'hdfs://jetblue-nn1.blue.ygrid.yahoo.com:8020/tmp/jessiej/datacopy/transfer_table'
```

**step 4 (JetBlue)**

```sql
LOAD DATA INPATH 'hdfs://jetblue-nn1.blue.ygrid.yahoo.com:8020/tmp/jessiej/datacopy/transfer_table/dt=20191001/hr=2019100100' INTO TABLE transfer_table PARTITION (dt='20191001', hr='2019100100');
```
