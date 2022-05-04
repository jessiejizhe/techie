# Pyspark

reference: [PySpark SQL Module](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html)

## libraries

```python
from pyspark.sql import SparkSession
from pyspark.sql import Row
from pyspark.sql.types import *
import pyspark.sql.functions as F
from pyspark.sql import Window
from datetime import datetime, timedelta
from pyspark.mllib.stat import Statistics
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.regression import LabeledPoint
from collections import OrderedDict
```

## data I/O

### read data

hive

```python
sql_query = """
SELECT ad_call_id, ad_call_time, publisher_id, no_bid_reason, bid_price
FROM kite_prod.bid_sample
WHERE datestamp = '%s'
"""%(utc_hour)
df = spark.sql(sql_query)
```

avro

```python
pt_node = 'hdfs://phazontan-nn1.tan.ygrid.yahoo.com:8020'
db_node = 'hdfs://dilithiumblue-nn1.blue.ygrid.yahoo.com:8020'

bid_path = db_node + '/projects/kite/prod/internal/core/bid_sample/5m/' + utc_hour + '*'
df_bid = spark.read.format("com.databricks.spark.avro").load(bid_path)

imp_path = db_node + '/projects/kite/prod/internal/core/revenue_fact/5m/' + utc_hour + '*'
df_imp = spark.read.format("com.databricks.spark.avro").load(imp_path)
```

csv

```python
df = spark.read.csv('/user/ad.csv', header=True).drop('index')

# equivalently

crative_file_path = "/user/ad.csv"
creative = spark.read.csv(crative_file_path, header=True, sep='\t')
creative = spark.read.format("com.databricks.spark.csv").option("delimiter", "\t").option("header", "true").load(crative_file_path)
```

special delimiter (i.e. read MDQ files mapping tables)

```python
# configs
--py-files mdq_reader.py # https://git.vzbuilders.com/junsong/line_delivery_and_recommendation/blob/master/py/mdq_reader.py
--conf spark.executorEnv.PYTHONPATH="$PYTHONPATH:."
--conf spark.yarn.appMasterEnv.PYTHONPATH="$PYTHONPATH:."

# scripts
from mdq_reader import *
pt_node = 'hdfs://phazontan-nn1.tan.ygrid.yahoo.com:8020'
ad_path = pt_node + '/projects/kite/prod/internal/mdq/mdq_certified/5m/201906231000'
table_name = 'ad'
df_ad = MdqReader(spark).readData(mdq_path, table_name)
df_ad.show(5)
```

parquet

```python
df = spark.read.orc()
```

orc

```python
df = spark.read.orc()
```

bz2

```python
df = spark.read.format('json').load()
```

create UDFs to read hourly and aggregate to daily

```python
def read_bucket(utc_hour):
    url = "hdfs://dilithiumblue-nn1.blue.ygrid.yahoo.com:8020/projects/advserving_pbp/prod/dsp_agg/cache/Advertiser/"+utc_hour+"/ExperimentBucket.csv"
    mapping = spark.read.csv(url, header=False).drop('index')
    mapping = mapping.withColumnRenamed('_c0','id').withColumnRenamed('_c1','bucket_id')\
                     .withColumnRenamed('_c2','experiment_id').withColumnRenamed('_c4','traffic_share')\
                     .withColumnRenamed('_c6','is_active').withColumnRenamed('_c7','is_control')
    mapping = mapping.select('id','bucket_id','experiment_id','traffic_share','is_active','is_control')
    mapping = mapping.filter(mapping.is_active=='true').drop('is_active')
    mapping = mapping.filter(mapping.experiment_id=='42')
    mapping = mapping.withColumn('hour', lit(utc_hour))
    mapping = mapping.withColumn('id', mapping.id.cast('integer'))
    mapping = mapping.withColumn('traffic_share_p', mapping.traffic_share.cast('integer'))
    return mapping

def read_bucekt_day(utc_date):
    field = [StructField("id",IntegerType(), True),
    	     StructField("bucket_id", StringType(), True),\
             StructField("experiment_id", StringType(), True),
	     StructField("traffic_share", StringType(), True),\
             StructField("is_control", StringType(), True),
	     StructField("hour", StringType(), True)]
    schema = StructType(field)
    mapdf = sqlContext.createDataFrame(sc.emptyRDD(), schema)

    for i in range(0,24):
        j = str(i).zfill(2)
        utc_hour = utc_date + j
        mapping = read_bucket(utc_hour)
        mapdf = mapdf.union(mapping)
    return mapdf
```

sc.parallelize(...) spread the data amongst all executors

sc.broadcast(...) copy the data in the jvm of each executor

### write data

hive

```python
res.write.format("orc").saveAsTable("$db_name.$table_name")
res.coalesce(10).write.partitionBy("dt", "hour").saveAsTable("$db_name.$table_name", format = "orc", mode = "overwrite")

# insert partitions into hive table
if tb_name not in sql_context.tableNames(db_name):
    res.write.partitionBy("datestamp").saveAsTable("%s.%s"%(db_name, tb_name), format="orc", mode="append")
else:
    spark.conf.set("spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation", "true")
    spark.sql("ALTER TABLE %s.%s DROP IF EXISTS PARTITION (datestamp=%s)"%(db_name, tb_name, utc_hour))
    res.write.partitionBy("datestamp").saveAsTable("%s.%s"%(db_name, tb_name), format="orc", mode="append")
```

avro

```python
res_path = "hdfs://jetblue-nn1.blue.ygrid.yahoo.com:8020/user/res/" + mydate
res.write.format("com.databricks.spark.avro").save(res_path)
```

csv

```python
res_path = '/user/res'
res.coalesce(1).write.csv(res_path, header=True)
```

## generate table

```python
## 1. random table

df = sqlContext.range(0, 10)
df = df.select("id", rand(seed=10).alias("uniform"), randn(seed=27).alias("normal"))

## 2. empty table

from pyspark.sql import SQLContext
sc = spark.sparkContext
sqlContext = SQLContext(sc)
# need the above for PySpark Shell

field = [StructField("id",StringType(), True), StructField("bucket_id", StringType(), True),\
         StructField("traffic_share", StringType(), True), StructField("is_control", StringType(), True),\
         StructField("hour", StringType(), True)]
schema = StructType(field)
mapdf = sqlContext.createDataFrame(sc.emptyRDD(), schema)

# rename columns
for c,n in zip(bid_imp_daily.columns, newcolnames):
    bid_imp_daily = bid_imp_daily.withColumnRenamed(c,n)

## 3. create from empty Pandas dataframe

empty_dict = dict()
pd_empty = pd.DataFrame.from_dict(df_perf_dict, orient='index', columns=['score'])
empty_schema = StructType([StructField('score', DoubleType(), False)])
spark_empty = spark.createDataFrame(pd_empty, schema= empty_schema)

## 4. create empty column in dataframe
empty_schema = StructType([StructField('pubDealId',StringType(),True),
                           StructField('reservedPrice',DoubleType(),True),
                           StructField('dealId',LongType(),True),
                           StructField('adgroup_id',LongType(),True),
                           StructField('deal_type',StringType(),True),
                           StructField('wseats_bag',ArrayType(StructType([StructField('wseats',StringType(),True)]),True),True),
                           StructField('adx_publisher_blocks_overridden',StringType(),True),
                           StructField('must_bid_level',StringType(),True)])

df = df.withColumn('deal_infos_exp', lit(None).cast(empty_schema))

## 5.  create dataframe from list
schema = StructType([StructField('id', StringType()), StructField('cnt', IntegerType())])
val = [[1, 13234121], [2, 233], [3, 13132]]
df = spark.createDataFrame(val, schema=schema)
```

## glance data

```python
print df.printSchema()
print df.dtypes
print df.first()
print df.collect()
print df.limit(5).show()
print df.show(5)
print df.count()

df.columns
df.schema.names

cols = [i for i in df if i[:8]==date]
cols.append('line_id')
```

## data transformation

### pandas

it is encouraged to use native PySpark against Pandas

```python
dfpd = df.toPandas()
```

### filters

```python
df = df.filter((df.device_type_id==2) | (df.device_type_id==3))
df = df.filter(F.col('ad_layout_id')==19)
df = df.filter(df.app_name.isNotNull()).filter(df.app_name !="")

df.filter("region='US' and media_type='Video' and app_tld in ('com.sling','96041','tv.pluto.android')").show()
```

### join tables

```python
# side by side

df_join = df1.join(df2, df1.source == df2.source, 'outer')
df_join = df1.join(df2, 'source', 'outer')

skewdf = bigdf.join(F.broadcast(smalldf), key)

# stack
tablenew = table1.union(table2)
```

### create new columns and assign values

```python
df = df.na.fill('0')
df.na.fill({'age': 50, 'name': 'unknown'})

df = df.withColumn('if_device', F.when(df.device_id=='00000000-0000-0000-0000-000000000000',-1)\
			       .F.when(df.device_id=='',0)\
			       .otherwise(1))
df = df.withColumn('click_label', F.when(df.click_label==-1,0)\
				   .otherwise(df.click_label)\
				   .alias('click_label'))
df = df.select(*(F.when((col(c))=='', None).otherwise(col(c)).alias(c) for c in df.columns))

df = df.withColumn('gemini_recent_app_id_ct',regexp_replace('gemini_recent_app_id_ct','APPIDC:',''))
df = df.withColumn('gemini_recent_app_categories', explode(split('gemini_recent_app_categories','APPC:')))
df = df.select(F.substring(df.label,6,100).alias('label'))
```

### change data type

```python
df = df.withColumn('id', df.id.cast('integer'))

df_num = df_num.select(*(col(c).cast('integer') for c in df_num.columns))
df_num_rdd = df_num.rdd.map(lambda data: Vectors.dense([int(c) for c in data]))

OrderedDict(sorted(result_dic.items(), key=lambda t: t[1],reverse=True))
```

### unique value to list

```python
browser_list = df.select('browser').rdd.flatMap(lambda x: x).collect()
```

### use SQL syntax

```python
df.registerTempTable("people")
a = sqlContext.sql("select * from people")
a.show()
```

### regular expression

```python
vista = vista.withColumn('v_version', F.when(vista.debug_metrics.like('%0.0.%'),1)\  
                                     .F.when(vista.debug_metrics.like('%2.0.%'),2)\  
                                     .otherwise(0))  
```

### distinct

```python
df.groupBy('click_label').count().show()

df.groupby('user_id_type').agg(countDistinct('user_id').alias('u_user_id')).orderBy('u_user_id').show()

df.select( [ countDistinct(cn).alias(cn) for cn in df.columns ] ).show()
df.select('partner_user_id').distinct().show()
```

### substring values

```python
dd2 = [('device1','APPC:1'),
       ('device2','APPC:2'),
       ('device3','APPC:3')]  
test2 = spark.createDataFrame(dd2, ['id','label'])  
replace1 = test2.select(F.substring(test2.label,6,100).alias('label'))  
replace2 = test2.select(F.regexp_replace('label','APPC:','').alias('label'))  
```

### explode values

```python
dd3 = [('device1','APPC:lifeAPPC:sports'),
       ('device2','APPC:lifeAPPC:gameAPPC:sports'),
       ('device3','APPC:sports'),
       ('device4','nah')]  
test3 = spark.createDataFrame(dd3, ['id','group'])  
explode = test3.withColumn('group', F.explode(split('group','APPC:'))))  
explode = explode.filter(F.explode.group !="")  
```

### extract values

```python
dd4 = [('device1','APPIDC:1'),
       ('device2','APPIDC:2'),
       ('device3','APPIDC:3'),
       ('device4','')]  
test4 = spark.createDataFrame(dd4, ['id','group'])  
extract0 = test4.withColumn('group', F.when(test4.group=='', None).otherwise(test4.group))  
extract00 = extract0.na.fill('0')  
extract = extract00.withColumn('group', F.regexp_replace('group','APPIDC:',''))  
```

### hashing

```python
dd5 = [('device1','level1'),
       ('device2','level2'),
       ('device3','level1'),
       ('device4','')]
test5 = spark.createDataFrame(dd5, ['id','group'])
test5.select(F.hash('group')).show()
```

### column intersection with list

```python
dd6 = [('d1',[1,2,3]),
       ('d2',[1,2]),
       ('d3',[4]),
       ('d4',[3])]
test6 = spark.createDataFrame(dd6, ['id','labels'])
test_list = [2,3]
intersect_udf = lambda y: (F.udf(
                lambda x: (
                    list(set(x) & set(y)) if x is not None and y is not None else None),
                ArrayType(IntegerType())))
intersect_with_bucket = intersect_udf(test_list)
test6 = test6.withColumn('intersect', intersect_with_bucket(test6.labels))
test6 = test6.withColumn('size', F.size(intersect_with_bucket(test6.labels)))
test6.show()
```

### dates and datetime

```python
from datetime import datetime, timedelta

df = df.withColumn('date', from_unixtime(df.utc_ms/1000,format='yyyyMMdd'))

start_time = time.time()
end_time = time.time()
print end_time - start_time

preday = (datetime.today()-timedelta(days=-)).strftime('%Y%m%d')
df = spark.sql("SELECT ad_call_time FROM kive.impression_hourly WHERE hour=2017122000 limit 10")
df = df.withColumn('new', from_unixtime(test.ad_call_time/1000, format='yyyy-MM-dd HH:mm:ss'))
df = df.withColumn('new', from_unixtime(test.ad_call_time, format='yyyy-MM-dd HH:mm:ss'))
df = df.withColumn('dom', dayofmonth(test.new))
df = df.withColumn('dow1', date_format('new', 'u'))
df = df.withColumn('dow2', date_format('new', 'E'))
df = df.withColumn('hour', hour(test.new))

today_date_str = datetime.datetime.utcnow().strftime('%Y%m%d')
yesterday_date_str = (datetime.datetime.utcnow() + datetime.timedelta(days=-1)).strftime('%Y%m%d')

curHour_str = '2019010100'
curDate_obj = datetime.date(int(curHour_str[:4]), int(curHour_str[4:6]), int(curHour_str[6:8]))
tmrDate_obj = curDate_obj + timedelta(days=1)
tmrHour_str = tmrDate_obj.strftime('%Y%m%d') + curHour_str[-2:]

def compute_hours(curHour_str, interval_int):

    curHour_obj = datetime.strptime(curHour_str, '%Y%m%d%H')
    end_hour_obj = curHour_obj + timedelta(hours=interval_int)
    end_hour_str = end_hour_obj.strftime('%Y%m%d%H')
    
    return end_hour_str
```

### pivot table

```python
df.groupBy('no_bid_reason').pivot('user_id_type').agg(F.sum('bids_represented'))\
  .na.fill(0)\
  .orderBy('no_bid_reason').show(500)
```

### basic statistics

```python
df.groupBy().agg(F.min('value'),F.avg('value'),F.max('value')).show()
```

### cross tabulation / contigency table

```python
names = ["Alice", "Bob", "Mike"]
items = ["milk", "bread", "butter", "apples", "oranges"]
df = sqlContext.createDataFrame([(names[i % 3], items[i % 5]) for i in range(100)], ["name", "item"])
df.stat.crosstab("name", "item").show()
```

### (cumulative) percentages

```python
from pyspark.sql import Window
df = df.withColumn('total_req', F.sum('req_cnt').over(Window.partitionBy(key_col)))\
       .withColumn('req_p', F.round(100*F.col('req_cnt')/F.col('total_req'),1))

windowval = (Window.partitionBy('bucket').orderBy('fallout_bucket')
             .rangeBetween(Window.unboundedPreceding, 0))
df = df.withColumn('cum_load', F.sum('load').over(windowval).cast('integer'))
df = df.withColumn('adj_cum_load',F.round(df.cum_load/df.traffic_p,0).cast('integer'))

```

### row difference

```python
my_window = Window.partitionBy().orderBy('ad_call_time')

# get value from the previous row
df = df.withColumn('prev_value', F.lead(df.value).over(my_window))
df = df.withColumn('diff', F.when(F.isnull(df.value - df.prev_value), 0).otherwise(df.value - df.prev_value))

# get value from the following row
df = df.withColumn('prev_value', F.lag(df.value).over(my_window))
df = df.withColumn('diff', F.when(F.isnull(df.value - df.prev_value), 0).otherwise(df.value - df.prev_value))
```

### bucketizer

```python
from pyspark.ml.feature import Bucketizer

splits = [-float("inf"), -0.5, 0.0, 0.5, float("inf")]

data = [(-999.9,), (-0.5,), (-0.3,), (0.0,), (0.2,), (999.9,)]
ddf = spark.createDataFrame(data, ["features"])

bucketizer = Bucketizer(splits=splits, inputCol="features", outputCol="bucketedFeatures")

print splits
print ddf.show()

# Transform original data into its bucket index.
bucketedData = bucketizer.transform(ddf)

print("Bucketizer output with %d buckets" % (len(bucketizer.getSplits())-1))
bucketedData.show()
```

### pearson / spearman R correlation test

```python
df = df.select(*(col(c).cast('float') for c in df.columns))
df_rdd = df.rdd.map(lambda data: Vectors.dense([c for c in data]))

result_matrix = Statistics.corr(df_rdd, method="pearson")
result_matrix = Statistics.corr(df_rdd, method="spearman")

result_dic = dict(zip(df_num.columns, result_matrix[0].tolist()))
result_num = pd.DataFrame({'column': result_dic.keys(), 'corr': result_dic.values()})
```

### chi-square test

```python
df = df.na.fill('nah')
df = df.select(*(hash(col(c)).cast('integer') for c in df.columns))
df_rdd = df.rdd.map(lambda data: Vectors.dense([c for c in data]))
df_rdd = df_rdd.map(lambda row: LabeledPoint(row[0],row[1:]))

obs = sc.parallelize(
    [LabeledPoint(1.0, [1.0,3.0]),
     LabeledPoint(1.0, [1.0,0.0]),
     LabeledPoint(1.0, [-1.0,-0.5])]
)

featureTestResults = Statistics.chiSqTest(obs)

for i, result in enumerate(featureTestResults_all_1):
    print("Click correlation with %s:\n%s\n" % (df_rdd.columns[i+1], result))

res = []
for i, result in enumerate(featureTestResults):
    row = []
    row.append(i)
    row.append(result.degreesOfFreedom)
    row.append(result.pValue)
    row.append(result.statistic)
    res.append(row)
res = pd.DataFrame(res,columns=['col','dof','p','chi-sq'])
res
```

## UDFs and UDAFs

```python
def convert_big_num_smart(num):
    if num >= 1e9:
        big_num = str(round(num / 1e9, 1)) + 'B'
    elif num >= 1e6:
        big_num = str(round(num / 1e6, 1)) + 'M'
    elif num >= 1e3:
        big_num = str(round(num / 1e3, 1)) + 'K'
    else:
        big_num = str(round(num, 1))
    return big_num

convertBigNumSmartUDF = F.udf(convert_big_num_smart, StringType())
```

device_id format validation

```python
import re

def device_format(device_id):
    
    """
    :input: device_id, string
    :output: zeros(36), idfa(36), aaid(22), potential_md5(32), potential_sha1(40), len_X, len_X_NoNumber, len_X_NoLetter
    """
    
    len_device_id = len(device_id)
    
    if len_device_id == 36:
        
        pattern_idfa = re.compile(r'[0-9A-F]{8}-[0-9A-F]{4}-[0-9A-F]{4}-[0-9A-F]{4}-[0-9A-F]{12}', re.MULTILINE)
        pattern_aaid = re.compile(r'[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}', re.MULTILINE)
        
        if device_id == '00000000-0000-0000-0000-000000000000':
            output_format = 'zeros'
        elif len(pattern_idfa.findall(device_id))>0:
            output_format = 'idfa'
        elif len(pattern_aaid.findall(device_id))>0:
            output_format = 'aaid'
        else:
            output_format = 'len_' + str(len_device_id)
            
    elif len_device_id == 32:
        
        pattern_md5 = re.compile(r'[0-9a-fA-F]{32}', re.MULTILINE)
        if len(pattern_md5.findall(device_id))>0:
            output_format = 'potential_md5'
        else:
            output_format = 'len_' + str(len_device_id)
    
    elif len_device_id == 40:
        
        pattern_sha1 = re.compile(r'[0-9a-fA-F]{40}', re.MULTILINE)
        if len(pattern_sha1.findall(device_id))>0:
            output_format = 'potential_sha1'
        else:
            output_format = 'len_' + str(len_device_id)
    
    else:
        
        clean_string = re.sub(r'[^a-zA-Z0-9]+', '', device_id)
        pattern_string = re.compile(r'[A-Za-z]', re.MULTILINE)
        pattern_number = re.compile(r'[0-9]', re.MULTILINE)
        
        if len(pattern_string.findall(device_id))==len(clean_string):
            output_format = 'len_' + str(len_device_id) + '_NoNumber'
        elif len(pattern_number.findall(device_id))==len(clean_string):
            output_format = 'len_' + str(len_device_id) + '_NoLetter'
        else:
            output_format = 'len_' + str(len_device_id)
        
    return output_format

deviceFormatUDF = udf(device_format, StringType())
```

## plot

### Plot in matplotlib

Please refer to [python plot examples](https://git.vzbuilders.com/dsp-insight/DSP-CPU/blob/master/docs/Techie/python_plot_examples.ipynb)

### Plot in Brunel

step 1: grab data to local

```python
%%spark -o sampleData -n 100000
```

step 2: use **display** or **Brunel**

```python
%%display -d sampleData
```

```python
%%brunel  
    data('sampleData')  
    line  
    x(day)  
    y(rate1, rate2)  
    color(#series)  
    tooltip(#all)  
    :: width=800, height=400
```
[Brunel Documentation](https://brunel.mybluemix.net/docs/Brunel%20Documentation.pdf)

[Brunel Visualization Cookbook](https://github.com/Brunel-Visualization/Brunel/wiki/Brunel-Visualization-Cookbook)


## run PySpark on Jupyter

[PySpark Configurations](http://spark.apache.org/docs/latest/configuration.html)

### change limited spark configs on Jupyter

In Python2, Python3 (not SparkMagic), we can change configs using: `spark.conf.set("spark.sql.arrow.enabled", "true")`

`spark.conf.set("spark.yarn.driver.memoryOverhead", "8g")` does not work as of 9/17/2019.

### set configurations in SparkMagic

```python
%%configure -f
{
    "jars": ["hdfs:///user/jessiej/spark-avro_2.11-3.2.0.jar"],
    "conf": {
        "spark.yarn.access.hadoopFileSystems": "hdfs://phazontan-nn1.tan.ygrid.yahoo.com:8020, hdfs://dilithiumblue-nn1.blue.ygrid.yahoo.com:8020",
        "spark.ui.view.acls": "*",
        "spark.modify.acls": "*",
        "spark.speculation": "true"
    }
}
```

## run PySpark on Shell

### open PySpark directly

`/home/gs/spark/latest/bin/pyspark --conf spark.pyspark.driver.python="/home/y/var/python36/bin/python3.6"`

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
import pyspark.sql.functions as F
from datetime import datetime
import argparse

spark = SparkSession.builder.appName("PythonPi").enableHiveSupport().getOrCreate()

parser = argparse.ArgumentParser()
parser.add_argument("--utc_hour", help="define a UTC hour")
args = parser.parse_args()
utc_hour = args.utc_hour
```

### set configurations to run an existing script

```bash
## in general
/home/gs/spark/current/bin/spark-submit --master yarn --conf spark.pyspark.driver.python="/home/y/var/python36/bin/python3.6" <other spark options, eg --executor-memory 8g>
my_file_to_run.py <arguments for your application>

# mandatory
/home/gs/spark/current/bin/spark-submit --master yarn
# if encounter python3.6 directory not found use
/home/gs/spark/latest/bin/spark-submit --master yarn  --conf spark.pyspark.driver.python="/home/y/var/python36/bin/python3.6"
# To use python2.7
/home/gs/spark/latest/bin/spark-submit --master yarn  --conf spark.pyspark.driver.python="/home/y/var/python27/bin/python2.7"

## spark options
--driver-memory 4g --executor-memory 8g  --executor-cores 1
--jars spark-avro_2.11-3.2.0.jar

## spark configurations
--conf spark.admin.acls=*
--conf spark.ui.view.acls=*
--conf spark.speculation=true
--conf spark.yarn.access.hadoopFileSystems=hdfs://dilithiumblue-nn1.blue.ygrid.yahoo.com:8020
--conf spark.yarn.access.hadoopFileSystems=hdfs://phazontan-nn1.tan.ygrid.yahoo.com:8020

# if you need more memories (please use in caution)
--driver-memory 12g
--executor-memory 12g
--executor-cores 5
--num-executors 100
--conf spark.network.timeout=600
--conf spark.driver.maxResultSize=8g
--conf spark.yarn.driver.memoryOverhead=8g
--conf spark.dynamicAllocation.maxExecutors=1000
```

### kill a pyspark YARN application from terminal

`yarn application -kill <application_id, i.e. application_1593181918474_*******>`

## Custom Python Libraries

configuration

```bash
--py-files ${pysparkLibPath}/mdq_reader.py,${pysparkLibPath}/archive.zip
--conf spark.executorEnv.PYTHONPATH="$PYTHONPATH:."
--conf spark.yarn.appMasterEnv.PYTHONPATH="$PYTHONPATH:."
```

or try this

[Add additional python packages to Spark environment](https://git.vzbuilders.com/pages/developer/MLDS-guide/faq/#how-do-i-add-additional-python-packages-to-my-environment)