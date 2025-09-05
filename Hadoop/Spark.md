Spark

**df.show()**
- 전체 df

**df.printSchema()**
```
root |-- name: string (nullable = true) 
     |-- age: long (nullable = true)
```

**df.filter**
- df.filter(df.age > 10)
- df.filter(df.name.contains('i'))

**Spark Session**
```python
spark = SparkSession.builder \ # build sparksession object
    .appName("PySpark Fundamentals - Data Engineering") \ # set the name of the application
    .config("spark.sql.adaptive.enabled", "true") \ # enable adaptive query execution
    .config("spark.sql.adaptive.coalescePartitions.enabled", "true") \ # dynamically merge small shuffle partitions
    .config("spark.sql.adaptive.skewJoin.enabled", "true") \ # enable skewed join handling
    .config("spark.sql.shuffle.partitions", "200") \ # default number of partitions it create during shuffles
    .getOrCreate() # create or return existing one
```
- entry point to using pyspark (python api for apache spark)

- allows
	- read and write data
	- run sql queries
	- create and manipulate dataframes
	- configurate spark settings

- if the session dies out
	- variables/df memory are lost
	- all configuration setting reset
	- sparkcontext is terminated


**AQE (Adaptive Query Execution)**
- dynamically optimize queries at runtime
- change join strategies or number of partitions based on data statistics

**Skewed join handling**
- if one table in a join has a highly skewed key, it can split large partitions to avoid bottleneck during joins

**Shuffle**
- redistribute data across the cluster (between stages of a job)
- involves moving data between different executors

**Default - Data Types**
- StringType()
- IntegerType()
- LongType()
- DoubleType()
- BooleanType()
- DateType()
- TimestampType()

**Array Type**
- used for columns that contain a list of elements of the same type
- ex) ArrayType(StringType()) = ['a', 'b', 'c']

**Map Type**
- used for columns that store key-value pairs
- ex) MapType(StringType(), IntegerType()) = {'a', 1}

**Struct Type**
- structured data type used to define a complex object within a column
- ex) StructType([
	- StructField("id", IntegerType(), True)    
	- StructField("name", StringType(), True)
	- << True : field is nullable (it can contain null values)
	- << False : field is not nullable (it may cause an error)
- ])

**Defining Schema**
1. define schema -> read data

2. read data (inferred schema) -> make adjustment
	`df = spark.createDataFrame(employee_data, column_names)`
		> df 생성
	`df = df.withColumn("hire_date", F.col("hire_date").cast(DateType()))`
		> cast(DateType, IntegerType, BooleanType, ...) data 형식 변경
		
**Spark 1.x vs Spark 2.x**
- Spark1.x
	- RDD-Based, lower level API
- Spark 2.x
	- DataFrame, Dataset API, Catalyst optimizer, Tungsten engine, SQL Integration


**Spark Session**
- main entry point to use DataFrame, SQL and Dataset API in Spark2.0+

```python
from pyspark.sql import SparkSession

# Create SparkSession
# configurate app name, master node, etc...
spark = SparkSession.builder \
	# reuses if one already exits
    .appName("MyFirstApp") \
    # reuses if one already exits
    .getOrCreate()

# Check Spark version
print(spark.version)
```


**RDD vs DataFrame**
- **RDD**
	- low level
	- immutable, distributed collection of objects
	- functional programming (map, filter, reduce)

```python
rdd = spark.sparkContext.parallelize([1, 2, 3, 4])
rdd2 = rdd.map(lambda x: x * 2)
print(rdd2.collect())  # [2, 4, 6, 8]
```

- **DataFrame**
	- high-level tabular data
	- optimized by catalyst engine
	- support sql-style and functional apis
	  
```python
df = spark.createDataFrame([(1, "Alice"), (2, "Bob")], ["id", "name"])
df.show()
```

- **Dataset**
	- type-safe version of Dataframe(Scala/Java only)

### 🆚 Summary: When to Use

|Feature|RDD|DataFrame|
|---|---|---|
|API Level|Low-level (functional)|High-level (SQL-like + DSL)|
|Optimization|Manual|Catalyst optimizer (auto)|
|Performance|Slower|Faster|
|Usage|Custom logic, transformations|ETL, SQL-style ops, analytics|

**Read into DataFrame**
```python
# read csv
df_csv = spark.read.csv("people.csv", header=True, inferSchema=True)

# read json
df_json = spark.read.json("people.csv")

# read parquet
df_parquet = spark.read.parquet("people.parquet")
```

**DataFrame Transformations**
- Lazy operations
	- they build a plan, but do not run until you call an action like .show() or .collect()

- select()
	- choose columns
- filter() / where()
```python
df.filter(df['age'] > 25)
df.where('age > 25')
df.filter(df["age"] > 25).show()
```

- withColumn()
	- add new column or modify existing
```python
from pyspark.sql.functions import col

df.withColumn("age_plus_1", col("age") + 1)
```

- drop()
	- remove column
	- `df.drop("age")`

- distinct()
	- `df_with_dups = spark.createDataFrame([(1, "Alice"), (1, "Alice")], ["id", "name"])`
	- `df_with_dups.distinct().show()`

- groupBy()
	- `df.groupBy("column").agg(...)`

- orderBy() / sort()
	- `df.orderBy("age").show()`
	- `df.orderBy(df["age"].desc()).show()`

- join()
```python
df1 = spark.createDataFrame([(1, "Alice"), (2, "Bob")], ["id", "name"])
df2 = spark.createDataFrame([(1, "Seoul"), (2, "Busan")], ["id", "city"])

df1.join(df2, on="id", how="inner").show()
```

**How to write conditions**
1. String Expression (SQL style)
	- `df.where('age > 25')`
	- `df.filter('age > 25')`

2. Using Column objects
```python
from pyspark.sql.functions import col

df.where(col("age") > 25)

df.filter(df["age"] > 25)
```


**DataFrame Actions**

**show()**
- displays first 20 rows by default in a table format
- `df.show(5)`

**collect()**
- brings all data into a list of row objects on local driver
- `rows = df.collect()`

**take(n)**
- same as head()

**count()**
- count rows

**first() / head()**
- `df.first()  # same as take(1)[0]`
- `df.head()   # same`

**foreach()**
- apply function to each row
- `df.foreach(lambda row: print(row["name"]))  # used in cluster jobs`






























































