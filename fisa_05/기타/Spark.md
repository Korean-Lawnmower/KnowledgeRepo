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
		



































