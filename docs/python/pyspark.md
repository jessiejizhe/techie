reference: [PySpark SQL Module](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html)

# libraries

```python
from pyspark.sql import SparkSession, Row, Window
from pyspark.sql.types import *
import pyspark.sql.functions as F

from pyspark.mllib.stat import Statistics
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.regression import LabeledPoint
from collections import OrderedDict

from datetime import datetime, timedelta
```

# data I/O

## read data

hive

```python
sql_query = """
    SELECT *
    FROM $db_name.tb_name
    WHERE datestamp = '%s'
    """%(utc_hour)
df = spark.sql(sql_query)
```

avro

```python
df = spark.read.format("com.databricks.spark.avro").load(file_path)
```

csv

```python
file_path = '/user/test.csv'

df = spark.read.csv(file_path, header=True).drop('index')
df = spark.read.csv(file_path, header=True, sep='\t')
df = spark.read.format("com.databricks.spark.csv").option("delimiter", "\t").option("header", "true").load(file_path)
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
def read_snapshot_hourly(utc_hour):
    url = "test.csv"
    mapping = spark.read.csv(url, header=False).drop('index')
    mapping = mapping.withColumnRenamed('_c0','id').withColumnRenamed('_c1','cnt')
    mapping = mapping.select('id','cnt')
    mapping = mapping.withColumn('hour', lit(utc_hour))
    return mapping

def read_snapshot_daily(utc_date):
    field = [StructField("id",IntegerType(), True),
    	     StructField("cnt", StringType(), True),
             StructField("hour", StringType(), True)]
    schema = StructType(field)
    mapdf = sqlContext.createDataFrame(sc.emptyRDD(), schema)

    for i in range(0,24):
        j = str(i).zfill(2)
        utc_hour = utc_date + j
        mapping = read_snapshot_hourly(utc_hour)
        mapdf = mapdf.union(mapping)
    return mapdf
```

sc.parallelize(...) spread the data amongst all executors

sc.broadcast(...) copy the data in the jvm of each executor

## write data

hive

```python
res.write.format("orc").saveAsTable("$db_name.$table_name")
res.coalesce(10).write.partitionBy("dt", "hour").saveAsTable("$db_name.$table_name", format = "orc", mode = "overwrite")
```

insert partitions into hive table

```python
if tb_name not in sql_context.tableNames(db_name):
    res.write.partitionBy("datestamp").saveAsTable("%s.%s"%(db_name, tb_name), format="orc", mode="append")
else:
    spark.conf.set("spark.sql.legacy.allowCreatingManagedTableUsingNonemptyLocation", "true")
    spark.sql("ALTER TABLE %s.%s DROP IF EXISTS PARTITION (datestamp=%s)"%(db_name, tb_name, utc_hour))
    res.write.partitionBy("datestamp").saveAsTable("%s.%s"%(db_name, tb_name), format="orc", mode="append")
```

avro

```python
res_path = "/user/res/" + mydate
res.write.format("com.databricks.spark.avro").save(res_path)
```

csv

```python
res_path = '/user/res'
res.coalesce(1).write.csv(res_path, header=True)
```

# data explore

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

# data process

## toPandas

it is encouraged to use native PySpark against Pandas

```python
dfpd = df.toPandas()
```

## create table

create empty table

```python
from pyspark.sql import SQLContext
sc = spark.sparkContext
sqlContext = SQLContext(sc)
# need the above for PySpark Shell

field = [StructField("id",StringType(), True),
         StructField("cnt", IntergerType(), True),
         StructField("hour", StringType(), True)]
schema = StructType(field)
mapdf = sqlContext.createDataFrame(sc.emptyRDD(), schema)
```

create random table

```python
df = sqlContext.range(0, 10)
df = df.select("id", rand(seed=10).alias("uniform"), randn(seed=27).alias("normal"))
```

create from empty pandas dataframe

```python
empty_dict = dict()
pd_empty = pd.DataFrame.from_dict(df_perf_dict, orient='index', columns=['score'])
empty_schema = StructType([StructField('score', DoubleType(), False)])
spark_empty = spark.createDataFrame(pd_empty, schema= empty_schema)
```

create dataframe from list

```python
schema = StructType([StructField('id', StringType()), StructField('cnt', IntegerType())])
val = [[1, 13234121], [2, 233], [3, 13132]]
df = spark.createDataFrame(val, schema=schema)
```



## create col

create new columns and assign values

```python
df = df.na.fill('0')
df.na.fill({'age': 50, 'name': 'unknown'})

df = df.withColumn('if_null', F.when(df.id=='00000',-1)
                               .F.when(df.id=='',0)
                               .otherwise(1))
df = df.withColumn('click_label', F.when(df.click_label==-1,0)
                                           .otherwise(df.click_label)
                                           .alias('click_label'))
df = df.select(*(F.when((col(c))=='', None).otherwise(col(c)).alias(c) for c in df.columns))

df = df.withColumn('app_id',regexp_replace('app_id','APPIDC:',''))
df = df.withColumn('category', explode(split('category','APPC:')))
df = df.select(F.substring(df.label,6,100).alias('label'))
```

create empty column in dataframe

```python
empty_schema = StructType([StructField('id', StringType(),True),
                           StructField('price', DoubleType(),True),
                           StructField('type', StringType(),True),
                           StructField('info_bag', ArrayType(StructType(
                               [StructField('seats', StringType(),True)]),True),True),
                           StructField('level',StringType(),True)])
df = df.withColumn('info_exp', lit(None).cast(empty_schema))
```

## rename col

```python
for c,n in zip(df.columns, newcolnames):
    df = df.withColumnRenamed(c,n)
```

## filters

```python
df = df.filter((df.type_id==2) | (df.type_id==3))
df = df.filter(F.col('id')==19)
df = df.filter(df.name.isNotNull()).filter(df.name !="")

df.filter("region='US' and name in ('John','Jane')").show()
```

## change data type

```python
df = df.withColumn('id', df.id.cast('integer'))

df_num = df_num.select(*(col(c).cast('integer') for c in df_num.columns))
df_num_rdd = df_num.rdd.map(lambda data: Vectors.dense([int(c) for c in data]))

OrderedDict(sorted(result_dic.items(), key=lambda t: t[1],reverse=True))
```

## unique value to list

```python
browser_list = df.select('name').rdd.flatMap(lambda x: x).collect()
```

## use SQL syntax

```python
df.registerTempTable("people")
a = sqlContext.sql("select * from people")
a.show()
```

## regular expression

```python
vista = vista.withColumn('v_version', F.when(vista.debug_metrics.like('%0.0.%'),1)\  
                                     .F.when(vista.debug_metrics.like('%2.0.%'),2)\  
                                     .otherwise(0))  
```

## distinct

```python
df.groupBy('click_label').count().show()

df.groupby('id_type').agg(countDistinct('id').alias('u_id')).orderBy('u_id').show()

df.select( [ countDistinct(cn).alias(cn) for cn in df.columns ] ).show()
df.select('user_id').distinct().show()
```

## substring

```python
dd2 = [('device1','APPC:1'),
       ('device2','APPC:2'),
       ('device3','APPC:3')]  
test2 = spark.createDataFrame(dd2, ['id','label'])  
replace1 = test2.select(F.substring(test2.label,6,100).alias('label'))  
replace2 = test2.select(F.regexp_replace('label','APPC:','').alias('label'))  
```

## explode

```python
dd3 = [('device1','APPC:lifeAPPC:sports'),
       ('device2','APPC:lifeAPPC:gameAPPC:sports'),
       ('device3','APPC:sports'),
       ('device4','nah')]  
test3 = spark.createDataFrame(dd3, ['id','group'])  
explode = test3.withColumn('group', F.explode(split('group','APPC:'))))  
explode = explode.filter(F.explode.group !="")  
```

## extract

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

## hashing

```python
dd5 = [('device1','level1'),
       ('device2','level2'),
       ('device3','level1'),
       ('device4','')]
test5 = spark.createDataFrame(dd5, ['id','group'])
test5.select(F.hash('group')).show()
```

## column intersection with list

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

# table transform

## join tables

side by side

```python
df_join = df1.join(df2, df1.source == df2.source, 'outer')
df_join = df1.join(df2, 'source', 'outer')

skewdf = bigdf.join(F.broadcast(smalldf), key)
```

stack

```python
tablenew = table1.union(table2)
```

## pivot table

```python
df.groupBy('reason').pivot('id_type').agg(F.sum('cnt')).na.fill(0)
  .orderBy('reason').show(10)
```

## cross tabulation / contigency table

```python
names = ["Alice", "Bob", "Mike"]
items = ["milk", "bread", "butter", "apples", "oranges"]
df = sqlContext.createDataFrame([(names[i % 3], items[i % 5]) for i in range(100)], ["name", "item"])
df.stat.crosstab("name", "item").show()
```

## (cumulative) percentages

```python
from pyspark.sql import Window

df = df.withColumn('total_req', F.sum('req_cnt').over(Window.partitionBy(key_col)))\
       .withColumn('req_p', F.round(100*F.col('req_cnt')/F.col('total_req'),1))

windowval = (Window.partitionBy('bucket').orderBy('fallout_bucket')
             .rangeBetween(Window.unboundedPreceding, 0))
df = df.withColumn('cum_load', F.sum('load').over(windowval).cast('integer'))
df = df.withColumn('adj_cum_load',F.round(df.cum_load/df.traffic_p,0).cast('integer'))

```

## row difference

```python
my_window = Window.partitionBy().orderBy('time')

# get value from the previous row
df = df.withColumn('prev_value', F.lead(df.value).over(my_window))
df = df.withColumn('diff', F.when(F.isnull(df.value - df.prev_value), 0).otherwise(df.value - df.prev_value))

# get value from the following row
df = df.withColumn('prev_value', F.lag(df.value).over(my_window))
df = df.withColumn('diff', F.when(F.isnull(df.value - df.prev_value), 0).otherwise(df.value - df.prev_value))
```

## bucketizer

```python
from pyspark.ml.feature import Bucketizer

splits = [-float("inf"), -0.5, 0.0, 0.5, float("inf")]

data = [(-999.9,), (-0.5,), (-0.3,), (0.0,), (0.2,), (999.9,)]
df = spark.createDataFrame(data, ["features"])

bucketizer = Bucketizer(splits=splits, inputCol="features", outputCol="bucketedFeatures")

print splits
print df.show()

# Transform original data into its bucket index.
bucketedData = bucketizer.transform(df)

print("Bucketizer output with %d buckets" % (len(bucketizer.getSplits())-1))
bucketedData.show()
```

# stats

## basic statistics

```python
df.groupBy().agg(F.min('value'),F.avg('value'),F.max('value')).show()
```

## pearson / spearman R correlation test

```python
df = df.select(*(col(c).cast('float') for c in df.columns))
df_rdd = df.rdd.map(lambda data: Vectors.dense([c for c in data]))

result_matrix = Statistics.corr(df_rdd, method="pearson")
result_matrix = Statistics.corr(df_rdd, method="spearman")

result_dic = dict(zip(df_num.columns, result_matrix[0].tolist()))
result_num = pd.DataFrame({'column': result_dic.keys(), 'corr': result_dic.values()})
```

## chi-square test

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

# UDFs and UDAFs

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

