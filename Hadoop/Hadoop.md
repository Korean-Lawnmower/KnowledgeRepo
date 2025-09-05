### 섹션 1

**Hadoop 기본 세팅**
- Virtualbox
- HDP (Hortonworks Sandbox 2.6.5)

**Hadoop 개요 및 역사**
- 분산 저장/처리를 위한 오픈소스 플랫폼
- 병렬 구조를 통해 한개의 문제를 여러 컴퓨터 (클러스터) 로 분산하여 처리 가능
	- 클러스터는 대여 가능한 일반적인 컴퓨터로 구성

- GFS (Google File System)
	- Hadoop file system 의 조상
	- 분산 저장소의 첫 도입

- 하둡 제작자 : Yahoo

- Default : Horizontal scaling
- Batch processing 을 위해 처음 도입


### Hadoop Architecture 개요

1. **Core Hadoop Ecosystem**
	- **HDFS (Hadoop Distributed File System)**
		- distribute storage of big data across cluster
		- maintains redundant copies of data
			- can recover from incidents (backup)
	- **YARN (Yet another resource negotiator)**
		- manages the resources on your computing cluster
			- descides what gets to run when
			- what nodes are available, which are not, ...
		- 
		- **Map Reduce**
			- programming metaphor/model that allows you to process data across an entire cluster
			- consist of mappers and reducers
			- Mappers
				- ability to transform data in parallel across your entire computing cluster
			- Reducers
				- aggregate that data together
			- 
			- **pig**
				- high level programming api that allows you to write simple scripts that look like sql
				- transform script into what will run on map reduce
					- will go through yarn and hdfs to process and get the data that is needed
			- **Hive**
				- looks like a sql database
				- can execute sql queries that is stored on your hadoop cluster
		- **Spark**
			- run queries on data
			- map reduce it
			- handle real time streaming data and etc
		- **Tez**
			- uses a directed acyclic graph and uses same techniques as spark
			- produce more optimal plans for actually executing queries
	- **Apache Ambari**
		- gives the view of your cluster and let you visualize the states of your cluster
	- **Mesos**
		- alternate for yarn
	- **HBase**
		- no-sql database
		- fast db for large transaction rates
		- fast way of exposing results to other systems
	- **Storm**
		- way of processing streaming data
		- storm/spark solves the same problem
		- does not have to be a batch
		- can update ml models or transform data into database
	- **oozie**
		- schedule all of these things together into jobs that can be run on some schedule
	- **zookeeper**
		- coordinating everything on your cluster
		- keep track of shared states across cluster
		- can be used for keeping track of who the master node is or who's up who's down
	
	**Data Ingestion**
	- **Scoop**
		- connector between rdbms to hadoop database
	- **Flume**
		- transport web logs to your cluster (real time)
			- processed by storm or spark streaming
	- **kafka**
		- collect data of any sort from a cluster of pc or web servers
		- broadcaset that into your hadoop cluster

2. **External Data Storage**
	- **MySQL**
		- rdbms, etc
	- **Cassandra**
	- **MongoDB**
		- columnar data stores
		- for exposing data in real time usage
		- used as layer between real time application and cluster
	- **Hbase**

3. **Query Engines**
	- **Drill**
		- write sql queries that will work across range of no-sql databases
		- so that they can talk to hbase, cassandra, mongodb
		- and tie those results together and allow you to write queries across them
	- **Hue**
		- interactively creating queries that works well with hive and hbase
	- **pheonix**
		- do sql style queries
		- guarantee acid and oltp

### Section 2

**HDFS**
- what allows data to be stored across cluster in a distributed and reliable manner
- for handling large files
- break them up into blocks
- store more than one copy of each block (duplicates) for backup upon incidents
- allow you to purchase "commodity" computers

**Name Node**
- keep track of where all the blocks live
- what file partition is in which cluster

**Data Node**
- what actually store each files


**Fetch data**
Client Node -A, B-> Name Node -cluster 1, cluster 2-> Client Node -> Data Node

**Write data**
Client Node -ima write a, b, c-> Name Node - okay -> Client node -a, b, c -> Data Node - make copy -> Data Node2 -make copy-> Data Node 3 -i wrote it-> Client Node -> Name Node


**why is there only name node?**
- there should be only one name node active at a time or else clients might not agree on where different blocks are stored


**Backup metadata**
- name node writes to local disk and nfs mount
- if name node dies, it can bootstrap it using logs


**Secondary Name Node**
- maintains merged copy of edit log from primary name node


**HDFS Federation**
- when there are lots of small files, name node will reach its breaking point
- namespace volume (sub directories)
- given volume will know which name to talk to for reading/writing data files
- we can have separate name structure for each volume
	- given volume will know which name node to talk to, reading and writing data


**HDFS high availability**
- use shared edit log (shared storage that is not hdfs)
- the backup can take over right away
- use zookeeper to track active name node
	- does have more complications


**Map Reduce**
- divide up all data into partitions that can be processed in parallel across cluster
- **Maps Data**
	- transform data
	- extract information that it cares about, and organize it so it makes sense
- **Reduce Data**
	- aggregates data together
	- associate data with certain key value (something you want to aggregate on)
- **handle failure**
- natively java

**Example**
Bob : Antz, Forest Gump, Dumbo
Jessica : Lion King, Nemo, Toy Story

INPUT DATA --> Mapper --> (K1:V, K2:V, K3:V, K1:V, K1:V) (key/value pair)
                         (Bob : Antz, Bob : Forest Gump, Bob : Dumbo, Jessica : Nemo, ...)


*Goal : How many movies did each person rate?*

1. Mapper transfer input data into individual movie rating and transfer it into key-value pair
	- Key : user_id (what to aggregate on)
	- Value : movies (what will be aggretating)
- **same key can appear multiple times and its desireable**


```csv
USER ID|MOVIE ID|RATING|TIMESTAMP
196 242 3 88123123
186 302 3 12314122
196 277 1 12312344
244 51  2 45632413
166 346 1 43562345
...
```

**--> mapper** 
- mapping stage may be distributed across different computers

--> 196:242, 186:302, 196:377, 244:51, ...
- it is important to drop everything that is not needed in the earlier stage of the process so excess data does not get moved around the clusters
	- meaning : optimization is important

**--> SHUFFLE AND SORT (Automatic)** 
- sorts and groups the mapped data

--> 166:346  186:302,274,265  196:242,377  244:51


**Reducer**
- given output from shuffle and sort, reducer is a bit of code that you provide that descides what to do with it

--> **len(movies)**

--> 166:1  186:3  196:2  244:1


---

**더 상세한 과정**

client - i want a mr job -> yarn
client - copy data "up" that is needed for mr job -> hdfs

mapreduce application master starts on Node Manager
Node manager : keeps track of what this node is doing, is it up, etc,,,

application master
- responsible for keeping an eye on all the mr task
- works with the resource manager to distribute that work across clusters

yarn (resource manager)
- will try to make sure that the mr job run as close to the data

**Streaming**
- map task can just kick off through std i/o talking to other process that is happening that actually implement mappers and reducers

**Failure**
1. Application master monitors worker tasks for errors or hanging
	1. Restarts as needed
	2. Preferably on a different node
2. What if the application master goes down
	1. Yarn can try to restart it
3. What if an entire node goes down
	1. yarn can try to restart it
4. what if yarn (resource manager) goes down
	1. can set up "high availability" using zookeeper to have a hot standby resource manager


**Making a problem into a MapReduce Problem**
Goal : count the  number of each rating

```python
from mrjob.job import MRJob
from mrjob.step import MRStep

class RatingsBreakdown(MRJob):
	def steps(self):
		return [
			MRStep(mapper=self.mapper_get_ratings,
				reducer=self.reducer_count_ratings)
		]

	# mapper
	def mapper_get_ratings(self, _, line):
		(user_id, movie_id, rating, timestamp) = line.split('\t')
		yield rating, i

	# shuffle & sort happens in MRStep automatically without specifying

	# reducer
	def reducer_count_rating(self, key, values):
		yield key, sum(values)

if __name__ == '__main__':
	RatingsBreakdown.run()

```

**SSH hdp**
`ssh maria_dev@127.0.0.1 -p 2222`

**Multi-stage jobs**
```python
return [
	MRStep(mapper=self.mapper_get_ratings,
		reducer=self.reducer_count_ratings),
	MRStep(reducer=self.reducer_sorted_output)
]
```

---
### Section 3

**Ambari**
- `127.0.0.1:8080`
- overview of what your cluester is doing
- can start off with ambari to set up the rest with specific services you want

*how to open ambari under "Admin" instead of "maria dev"*
- su admin
- ambari-admin-password-reset

**Pig (Pig Latin)**
- create map reduce jobs without having to write Mappers and Reducers
- scripting language lets you use sql like syntax to define your map and reduce steps
- highly extensible with user defined functions

hdfs -> yarn -> mapreduce / tez -> pig

- can translate its jobs into mr or tez

**Grunt**
- command line (execute one at a time)

**Script**
**Ambari / Hue**


**pig script example**
- find the oldest 5 star rating movies
```pig
-- find the oldest 5 star rating movies

-- creates a "relation" named ratings with a given schema
ratings = LOAD '/user/maria_dev/m1-100k/u.data' AS
	(userId:int, movieId:int, rating:int, ratingTime:int)

-- using pigstorage if you need a different delimeter
metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')
	AS (movieID:int, movieTitle:chararray, releaseDate:chararray,
		videoRelease:chararray, imdbLink:chararray);

-- Creating relation from another relation
metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')
	AS (movieID:int, movieTitle:chararray, releaseDate:chararray,
		videoRelease:chararray, imdbLink:chararray);

/*
                      (second time since 1970)
	01-Jan-1995      -->      788918400
*/

nameLookup = FOREACH metadata GENERATE movie ID, movieTitle,
		ToUnixTime(ToDate(releaseDate, 'dd-MMM-yyyy')) AS releaseTime;


-- Group By
ratingsByMovie = GROUP ratings BY movieID;


-- average Ratings
avgRatings = FOREACH ratingsByMovie GENERATE group AS movieID,
	AVG(ratings.rating) AS avgRating;

-- Filter

fiveStarMovies = FILTER avgRatings BY avgRating > 4.0;

-- JOIN

fiveStarsWithData = JOIN fiveStarMovies BY movieID, nameLookup BY movieID;

-- ORDER BY
oldestFiveStarMovies = ORDER fiveStarsWithData BY nameLookup::releaseTime;


```

***To run this even faster***
- does not have to use map reduce, but rather use TEZ
	- click button



**Pig Latin Commands**
- LOAD, STORE, DUMP
	- STORE ratings INTO 'outRatings' USING PigStorage(':');
		- write them in a file with delimeter :
- FILTER, DISTINCT, FOREACH/GENERATE, MAPREDUCE, STREAM, SAMPLE
- JOIN, COGROUP, GROUP, CROSS, CUBE
	- cogroup : creates seperate tuple for each key and create nested structure
	- group : reduce
	- cross : cartesian product
	- cube : all combination for more than two columns
- ORDER, RANK, LIMIT
	- rank : assign rank number
	- limit : head(5)
- UNION, SPLIT
	- union : put them into one relation

**Diagnostics**
- DESCRIBE
- EXPLAIN
- ILLUSTRARE

**UDF**
- REGISTER
- DEFINE
- IMPORT


**Other functions**
- AVG, CONCAT, COUNT, MAX, MIN, SIZE, SUM

- pigstorage
	- field based data (set delimiter)
- textloader
- jsonloader
- avrostorage
- parquetloader
- orcstorage
- hbasestorage

**pig challenge**

*Goal 1 : Find all movies with and average rating less than 2.0*
*Goal 2 : Sort them by the total number of ratings*


```pig
-- 데이터 불러오기 1
ratings = LOAD '/user/maria_dev/ml-100k/u.data' AS (userID:int, movieID:int, rating:int, ratingTime:int);

-- 데이터 불러오기 2
metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')
	AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRealese:chararray, imdblink:chararray);

-- for looking up movie title based on movie id (create subset)
nameLookup = FOREACH metadata GENERATE movieID, movieTitle;

-- mapping (group ratings by movieid)
groupedRatings = GROUP ratings BY movieID;

-- for each grouped movie, create columns 
-- 1. average of the ratings as avgRating
-- 2. count of the ratings as numRatings
averageRatings = FOREACH groupedRatings GENERATE group AS movieID,
	AVG(ratings.rating) AS avgRating, COUNT(ratings.rating) AS numRatings;

-- create filter
badMovies = Filter averageRatings BY avgRating < 2.0;
```


---

### Section 4


**Spark**
- fast and general engine for large-scale data processing
- its scalable
	- driver program (spark context) -> cluster manager (spark, yarn) -> executor (cache, tasks)

- its fast
	- 100x faster than mapreduce in memory, 10x faster on disk
- DAG Engine (directed acyclic graph) optimizes workflows
- written in scala


- code in Python, Java, Scala
- built around : Resilient Distributed Dataset (RDD)
	- object that represent dataset, and various functions you can call on that RDD to 
		- transform
		- reduce
		- analyze

**Components of Spark**

 Spark Streaming | Spark SQL | MLLib | GraphX |
------------------------------------------------
 |                              Spark Core                              |

**Spark Streaming**
- instead of batch processing, takes in live streaming data (log)
- analyzed across window of time
- can store it

**Spark SQL**
- SQL interface for spark
- can use sql like functions to transform dataset in spark

 **MLLib**
 - library of ml and tools
 - clustering or regression analysis into mapper and reducers

**GraphX**
- analyze property of graph


 *ultimately, running things on scala is most optimal vs python*


**RDD (Resilient Distributed Dataset)**
- make sure your job is evenly distributed across clusters
- can handle failures in resilient manner

**RDD Objects**
- storing key - value information in an object that can automatically do the right thing on a cluster
- resilient and distributed

**basically like a spreadsheet broken into chunks, spread across multiple computers where you can run operations like filter, map and reduce in parallel**


**Spark Context**
- environment that you are running RDDs in, which are created by driver program
- it creates RDD's
- spark shell creates a "sc" object for you


***Crate RDD's***
```
# direct init example (not practical)
nums = parallelize([1,2,3,4])

# load file from hdfs, s3, ...
sc.textFile("file:///c:/users/frank/gobs-o-text.txt")
	- or s3n:// , hdfs://

# using hive
hiveCtx = HiveContext(sc) rows = hiveCtx.sql("select name, age from users")

also available from
- JDBC
- Cassandra
- Hbase
- Elasticsearch
- JSON, CSV, sequence files, object files, various compressed formats
```


**Transforming RDD's**
- map
	- 1:1 relationship
	- one row outputs to another row
```python
rdd = sc.parallelize([1, 2, 3])
mapped = rdd.map(lambda x: x * 2)  # [2, 4, 6]
```
- flatmap
	- N:M relationship
	- input lines may result in one or more
```python
rdd = sc.parallelize(["hello world", "spark is fast"])
flat = rdd.flatMap(lambda line: line.split())  
# [["hello", "world"], ["spark", "is", "fast"]]
# ["hello", "world", "spark", "is", "fast"]
```
- filter
	- filter can take stuff out of RDD
```python
rdd = sc.parallelize([1, 2, 3, 4])
filtered = rdd.filter(lambda x: x % 2 == 0)  # [2, 4]
```
- distinct
	- give you distinct unique value within RDD
- sample
- union, intersection, subtract, cartesian

- groupByKey
```python
rdd = sc.parallelize([
    ("movie1", 3), ("movie1", 4), ("movie2", 2)
])
grouped = rdd.groupByKey()
# [("movie1", [3, 4]), ("movie2", [2])]
```
- mapValues
```python
rdd = sc.parallelize([
    ("movie1", (10, 2))
])
mapped = rdd.mapValues(lambda x: x[0] / x[1])  # [("movie1", 5.0)]
```
- join(otherRDD)
```python
rdd1 = sc.parallelize([("movie1", "Action")])
rdd2 = sc.parallelize([("movie1", 5.0)])
joined = rdd1.join(rdd2)
# [("movie1", ("Action", 5.0))]
```

**Actions (reduce/compute)**
- collect ()
	- take whatever in rdd to -> python list
- count
	- count number of elements
- countByValue
	- how many does each unique value occurs
- take
	- returns the first n elements
- top
- reduce
	- define function that will combine all values associated with each unique key
- reduceByKey
```python
rdd = sc.parallelize([
    ("movie1", 3), ("movie1", 4), ("movie2", 2)
])
reduced = rdd.reduceByKey(lambda a, b: a + b)
# [("movie1", 7), ("movie2", 2)]
```



**Lazy evaluation**
- nothing actually happens in your driver program until an **action** is called
- then, spark will say, i will work backwards from that result and figure out the fastest way to achieve that result
- until you hit an action, all you are doing is building chain of dependencies

**RDD to fine a lowest avg rating movies**

```python
from pyspark import SparkConf, SparkContext

# create dictionary so we can convert movieID to movie Name for the final result
def loadMovieNames(): # {1:'aaa', 2:'bbb', 3:'ccc'}
	movieNames = {}
	with open('ml-100k/u.item') as f:
		for line in f:
			fields = line.split('|')
			movieNames[int(fields[0])] = fields[1]
	return movieNames


# take each line of u.data and convert it to (movieID, (rating, 1.0))
# add up all the ratings for each movie, and the total number of ratings from each movie

def parseInput(line): # (movieID, (rating, 1.0))
	fields = line.split()
	return (int(fields[1]), (float(fields[2]), 1.0))


if __name__ == "__main__":
	conf = SparkConf().setAppName("WorstMovies")
	sc = SparkContext(conf=conf)

	# 추후 이름 변경을 위해 만든 dict
	movieNames = loadMovieNames()

	# read data from hdfs storage
	lines = sc.textFile("hdfs:///user/maria_dev/ml-100k/u.data") # lines : RDD

	# convert data into structure (movidID, (rating, 1.0))
	movieRatings = lines.map(parseInput) # movieRating : RDD

	# reduce (movieID, (sum, count))
	ratingTotalsAndCount = movieRatings.reduceByKey(lambda a, b: (a[0] + b[0], a[1] + b[1]))

	# map to averageRating
	averageRatings = ratingTotalsAndCount.mapValues(lambda totalandcount: totalandcount[0] / totalandcount[1])

	# sort by average rating
	sortedMovies = averageRatings.sortBy(lambda x: x[1])

	# take top 10 results
	results = sortedMovies.take(10)

	for result in results:
		print(movieNames[result[0]], result[1])

```
- strategy
	- for each movieID
		- sum up all the rating
		- sum up how many rating exist
		- calculate average using the sums

- ***when you do a reduce operation on RDD, you get passed in two values***
	- for each unique key, you are going to be passed pair of values that will be combined together in some way


**SC._____**
- `sc.parallelize`
	- splits it across spark workers so it can be processed in parallel
`rdd = sc.parallelize([1, 2, 3, 4, 5], numSlices=2)`

|Method|Purpose|
|---|---|
|`sc.textFile(path)`|Reads a text file (HDFS, S3, local, etc.) into an RDD of lines|
|`sc.wholeTextFiles(path)`|Reads files as (filename, content) pairs|
|`sc.sequenceFile(path)`|Reads Hadoop SequenceFiles|
|`sc.parallelize(data)`|Converts Python collection to RDD|
|`sc.broadcast(value)`|Broadcasts a variable to all worker nodes|
|`sc.accumulator(value)`|Shared counter (accumulator) used across workers|
|`sc.setCheckpointDir(path)`|Sets a directory for RDD checkpointing|
|`sc.addFile(path)`|Distributes a file to all executors (can be local or remote)|

***When running spark python scripts, you have to use spark-submit shell***
`spark-submit` shell
- sets up spark environment for you
- make sure its running on cluster
	- can pass in parameters to specify which cluster you want to run on
	- how much memory you want to allocate

`spark-submit LowestRatedMovieSpark.py`


**Spark SQL**
- Extends RDD to "DataFrame" object
	- contain row objects
	- can run sql queries
	- has a schema
		- can store data more efficiently, transport and optimize things based on schema information
	- read and write to json, hive parquet
	- communicates with JDBC / ODBC, Tableau

**SparkSQL in Python**
- `from pyspark.sql import SQLContext, Row`
- **Hive**
	- `hiveContext = HiveContext(sc)`
- **JSON -> DF**
	- `inputData = spark.read.json(dataFile)`
**create table view from dataframe**
	- `inputData.createOrReplaceTempView("myStructuredStuff")`
**running sql query on hive context**
- `myResultDataFrame = hiveContext.sql("""SELECT foo FROM bar ORDER BY foobar""")`

**df commands**
- df.show()
- df.select("field_name")
- df.filter(df("field_name" > 200))
- df.groupBy(df(field_name)).mean()
- df.rdd().map(mapperFunction)

**Datasets**
- Dataframe = DataSet of Row objects

#### [replace]
##### 1. 🔥 Use Modern Spark (ignore `SQLContext`, `HiveContext` unless absolutely needed)

`from pyspark.sql import SparkSession  spark = SparkSession.builder.appName("AppName").getOrCreate()`

✅ This single `spark` object replaces all of:

- `SQLContext`
    
- `HiveContext`
    
- Legacy confusion
    

---

##### 2. 📥 Read JSON → DataFrame

python

복사편집

`df = spark.read.json("data.json")`

- JSON becomes a **DataFrame** (think table with columns and types).
    
- You can `.show()`, `.filter()`, `.groupBy()`, etc.
    

---

##### 3. 🏷️ Create a Temp View from DataFrame (optional, for SQL)

python

복사편집

`df.createOrReplaceTempView("people") result = spark.sql("SELECT name FROM people WHERE age > 25")`

- Now you're using **SQL** on a DataFrame by referencing it as a table called `"people"`.
    

---

##### 4. 🧪 DataFrame API vs SQL

You can do the same logic two ways:

|SQL|DataFrame|
|---|---|
|`SELECT name FROM people WHERE age>25`|`df.filter(df.age > 25).select("name")`|

They both return a new **DataFrame**.

---

##### 5. 🔁 `.rdd()` (only if you _must_ drop back to RDDs)

python

복사편집

`rdd = df.rdd.map(lambda row: row.name)`

✅ Don't use `.rdd()` unless:

- You're doing something low-level that DataFrame API doesn't support
    
- You _already_ understand RDDs and have a reason to drop into them

[/replace]














**Lowest Rating movie**
```python
from pyspark import SparkConf, SparkContext

# This function just creates a Python "dictionary" we can later
# use to convert movie ID's to movie names while printing out
# the final results.
def loadMovieNames():
    movieNames = {}
    with open("ml-100k/u.item") as f:
        for line in f:
            fields = line.split('|')
            movieNames[int(fields[0])] = fields[1]
    return movieNames

# Take each line of u.data and convert it to (movieID, (rating, 1.0))
# This way we can then add up all the ratings for each movie, and
# the total number of ratings for each movie (which lets us compute the average)
def parseInput(line):
    fields = line.split()
    return (int(fields[1]), (float(fields[2]), 1.0))

if __name__ == "__main__":
    # The main script - create our SparkContext
    conf = SparkConf().setAppName("WorstMovies")
    sc = SparkContext(conf = conf)

    # Load up our movie ID -> movie name lookup table
    movieNames = loadMovieNames()

    # Load up the raw u.data file
    lines = sc.textFile("hdfs:///user/maria_dev/ml-100k/u.data")

    # Convert to (movieID, (rating, 1.0))
    movieRatings = lines.map(parseInput)

    # Reduce to (movieID, (sumOfRatings, totalRatings))
    ratingTotalsAndCount = movieRatings.reduceByKey(lambda movie1, movie2: ( movie1[0] + movie2[0], movie1[1] + movie2[1] ) )

    # Filter out movies rated 10 or fewer times
    popularTotalsAndCount = ratingTotalsAndCount.filter(lambda x: x[1][1] > 10)

    # Map to (rating, averageRating)
    averageRatings = popularTotalsAndCount.mapValues(lambda totalAndCount : totalAndCount[0] / totalAndCount[1])
    
    # Sort by average rating
    sortedMovies = averageRatings.sortBy(lambda x: x[1])

    # Take the top 10 results
    results = sortedMovies.take(10)

    # Print them out:
    for result in results:
        print(movieNames[result[0]], result[1])
```


**MLLIB**
- built in machine learning library for spark

**ALS**
- recommendation algorithm
- `als = ALS(maxIter=5, regParam=0.01, userCol="userID, itemCol="movieID", ratingCol="rating")`

**Cache DataFrame**
- `ratings = spark.createDataFrame(ratingsRDD).cache()`

**filter movies that have at least ten rating**
- **RDD version**
	- `popularTotalsAndCount = ratingTotalsAndCount.filter(lambda x: x[1][1] > 10`
- **DataFrame version**
```python
movieDataSet = spark.createDataFrame(movies)

# movieID, averageRating
averageRatings = movieDataSet.groupBy("movieID").avg("rating")

# movieID, count
counts = movieDataSet.groupBy("movieID").count()

joined = counts.join(averageRatings, "movieID").filter("count > 10")
```

---

### Section 5

**Hive**
- HDFS 클러스터 전체에 걸쳐 저장된 데이터에 표준 SQL 쿼리를 실행
- SQL -> MR or TEZ
- YARN 클러스터 관리자 위에서 실행
- give the experience of using a sql data warehouse
- **HiveQL**
	- variant of SQL
- lot easier to just use hive and sql syntax than write map reduce in java
- for OLAP, not fit for OLTP
	- for analytic queries across large dataset
- everything is denormalized

**extensible**
- user defined functions
- thrift server
- jdbc / odbc driver

**When to not use hive**
- OLTP
	- it converts sql commands into MR and MR takes time to spin up

**Views**
- can store the results of one query into a view and then use that view as a table in subsequent queries
	- way of breaking up more complicated queries into individual queries
	- it will persist, but not store a copy of that data anywhere

**Schema On Read**
- it takes an unstructured data, and applies a schema to it as it is being read instead
- **HCatalog**
	- expose that schema to other services as well

```sql
create table ratings (
	userID int,
	movieID int,
	rating int,
	time int
)
row format delimited
fields terminated by 't'
stored as textfile;

load data local inpath '${env:HOME}/ml-100k/u.data'
overwrite into table ratings;
```

**Load Data**
- move data from a distributed file system into hive

**Load Data Local**
- copies data from your local filesystem into hive

**Managed vs External Tables**
- Managed
	- load data
		- copy of data that is owned by hive
		- if you drop it, its gone
- External
	- when you create it, and give explicit location
	- if you drop it, its going to leave it there
```sql
create external table if not exists ratings(
	userID int,
	movieID int,
	rating int,
	time int
)
row format delimited fields terminated by '\t'
location '/data/ml-100k/u.data'
```

**Partitioning (optimization)**
- if you have massive dataset and if your queries are tend to be a specific partition of that data

```sql
create table customers (
	name string,
	address struct<street:string, city:string, state:string, zip:int>
)
partitioned by (country string);
```
- does treat country like a column
- it will become part of a sub directory under where hive stores its data
	- .../customers/country=CA/
	- .../customers/country=GB/

 **How to access hive**
 1. CLI / prompt
 2. saved query files
	 - `hive -f /somepath/queries.hql`
3. through ambari / hue
4. through jdbc / odbc server
	1. **jdbc**
		- Java Database Connectivity
		- a way for java applications to talk to a database
	2. **odbc**
		- general API for any application to connect to database using drivers
5. through thrift service
6. via oozie

**integrating MySQL to Hadoop**

**MySQL**
- generally monolithic in nature
- can be use for oltp

**Scoop**
- handle large dataset
- import export them to cluster
- RDBMS -> Mapper -> HDFS

`scoop import --conect jdbc:mysql://localhost/movielens --driver com.mysql.jdbc.Driver --table movies --hive-import`

`-m` : specify how many mapper you use

**incremental imports**
- keep RDBMS and Hadoop
- `--check-column
	- timestamp or sequence number
- --last-value`
	- where

**Sqoop : Export data from Hive to MySQL**
`sqoop export --connect jdbc:mysql://localhost/movielens -m 1 --driver com.mysql.jdbc.Driver --table exported_movies --export-dir /apps/hive/warehouse/movies --input-fields-terminated-by '\0001'`

**실습**
1. import movielens data into mysql database
```sql
set names 'utf8';
set character set utf8;
use movielens;
source movielens.sql
```
2. import movies to HDFS
```linux
sqoop import --connect jdbc:mysql://localhost/movielens --username root --password hadoop --table movies -m 1 2> error.log
```
3. import movies into Hive
```linux
sqoop import --connect jdbc:mysql://localhost/movielens --username root --password hadoop --table movies -m 1 --hive-import 2> error.log
```
4. export movies back into MySQL
apps > hive > warehouse > movies
```linux
sqoop export --connect jdbc:mysql://localhost/movielens -m 1 --username root --password hadoop --driver com.mysql.jdbc.Driver --table exported_movies --export-dir /apps/hive/warehouse/movies --input-fields-terminated-by '\0001' 2> error.log
```



### Section 6

**Hbase**
- opensource implementation of google's big table
- only has CRUD (Create, Read, Update, Delete) operation API
	- no query language

- **HMaster**
	- what manage schema of the data
	- knows where everything is
- **Region Server**
	- data are stored on hdfs
- **Zookeeper**
	- who watches the watcher
	- track who the current master is

- 구성
	- **Row**
		- referenced by a unique KEY
		- each ROW has some small number of COLUMN FAMILIES
		- **COLUMN FAMILIES**
			- contain arbitrary COLUMNS
	- **CELL**
		- can have many VERSIONS with given timestamps

- 구성 Example
	- Key
		- com.cnn.www
	- **Column Family**
		- **Contents**
			- `<html><head>CNN...</html></html>`
	- **Anchor column family**
	- store all pages that link back go cnn.com
		- **Anchor:cnnsi.com**
			- "CNN"
		- **Anchor:my.look.ca**
			- "CNN.com"

----
### MongoDB 개요
- **MongoDB**는 기업 지원을 기반으로 한 대표적인 NoSQL DB로, 이름은 “HuMONGOus data”에서 유래.    
- 특징은 **문서 기반(Document) 데이터 모델**로, JSON 형식 데이터를 자유롭게 저장 가능. 스키마 강제가 없으며, 필요하면 스키마를 적용할 수도 있음.
    

### CAP 정리에서의 위치
- **일관성(Consistency) + 파티션 허용(Partition Tolerance)** 선택.
- 단일 **Primary 노드**에 쓰기가 집중 → Primary 장애 시, 새로운 Primary 선출까지 쓰기 불가(일시적 가용성 하락).
- 읽기는 Secondary에서 가능하지만 권장되지 않음.
    

### 기본 구조
- **데이터베이스 → 컬렉션(Collection) → 문서(Document)** 구조.
- 각 문서에 `_id` 기본 키 자동 생성.
- 인덱스를 다양한 필드·조합에 생성 가능.
- 조인(Join)이 비효율적이므로 **비정규화(De-normalization)** 중심으로 설계 필요.
    

### 복제(Replica Set)
- Primary–Secondary 구조로 데이터 복제.
- Primary 장애 시 Secondary가 선출되어 대체.
- **과반수 선출** 필요 → 최소 3대 서버 필요.
- 비용 절감을 위해 **Arbiter 노드** 사용 가능(투표만 담당).
- **지연 복제(Delayed Secondary)** 설정 가능 → 잘못된 작업(예: drop DB) 복구용.
    

### 샤딩(Sharding) – 빅데이터 확장
- 여러 Replica Set을 **샤드 키(Shard Key)** 기준으로 나누어 데이터 분산 저장.
- **mongos 프로세스**와 **Config 서버**가 라우팅 및 메타데이터 관리.
- **Balancer**가 데이터 분포 불균형 시 자동 재조정.
- 샤드 키는 **고유성(High Cardinality)** 높은 필드가 적합.
    

### 장점 및 추가 기능
- **유연성**: JSON 형태의 비정형 데이터 자유롭게 저장.
- **강력한 인덱스**: 텍스트, 지리 공간(Geospatial) 인덱스 지원.
- **Aggregation** 및 **MapReduce** 지원 → 일부 Hadoop 기능 대체 가능.
- **GridFS** 제공 → 대용량 파일 저장 가능.
- **SQL 커넥터** 제공 → SQL 쿼리 실행 가능하지만 RDB처럼 효율적 조인은 불가.
- **Spark/Hadoop 연계** 가능, 일부 연산을 MongoDB 내부로 푸시다운하여 성능 향상.

---

## 데이터베이스 선택 시 고려 사항

### 1. 시스템 통합성
- 어떤 **다른 기술과 연결**해야 하는지 먼저 확인.
- Spark, SQL 인터페이스 등과의 호환성이 선택지를 제한할 수 있음.
### 2. 확장성 (Scale)
- 데이터 크기: 단일 서버 한계 초과 예상 → **Cassandra, MongoDB, HBase** 같은 분산 DB 필요.
- 트랜잭션 처리량: 초당 수천 건 이상 요청 → 분산 구조 필요.
### 3. 운영 및 보안
- 내부에 전문가가 있는지 확인.
- NoSQL은 기본값으로는 보안이 취약 → 올바른 설정 필요.
- 전문가 부재 시 **유료 지원(예: MongoDB 기업 지원)** 고려.
### 4. 예산
- 대부분 오픈소스라 **소프트웨어 비용은 무료**.
- 주요 비용 = **서버/클라우드 비용 + 운영 인력 비용**.
- AWS, GCP 같은 클라우드 서비스 활용 가능.
### 5. CAP 정리
- **Partition Tolerance**는 대규모 시스템에선 필수.
- **Consistency vs Availability** 중 무엇을 우선할지 애플리케이션 성격에 따라 결정.
    - 금융/주식거래 → **Consistency** 중시.
    - 웹 서비스/추천 시스템 → **Availability** 중시.
### 6. 단순성
- 필요 이상으로 복잡한 NoSQL 아키텍처 도입 금지.
- 규모가 작으면 **MySQL 같은 단순 RDB**가 더 합리적.
    

---

## 사례별 접근

1. **내부 전화번호부 (소규모 애플리케이션)**
    - 데이터 적음, 트랜잭션 적음 → **MySQL** 적합.
    - 단순, 이미 설치된 경우가 많음.
        
2. **웹 로그 분석 (내부 분석용)**
    - 대규모 트랜잭션 없음 → DB 불필요.
    - **Hadoop, Spark, Hive, Pig** 활용.
        
3. **영화 추천 시스템 (대규모 사용자, 빠른 응답 필요)**
    - **Availability + Partition Tolerance** 우선.
    - **Cassandra** 적합. (Consistency는 조금 희생 가능)
        
4. **주식 거래 시스템 (대규모 트랜잭션, 강한 일관성 필요)**
    - **Consistency + Partition Tolerance** 필수.
    - 보안/지원 중요 → **MongoDB, HBase, 혹은 고급 RDB 분산 구조** 고려.
    - 유료 지원과 강력한 보안 체계 필요.
---

👉 핵심:  
데이터베이스 선택은 **통합성, 확장성, 운영/보안, CAP 우선순위, 단순성**을 기준으로 결정.  
작은 규모 = **RDB (MySQL)**,  
대규모 분석 = **Hadoop/Spark**,  
대규모 서비스 = **NoSQL (Cassandra/MongoDB/HBase)**,  
금융/거래 = **Consistency 중시 DB**.


---

**2025-09-02**

**Yarn (yet another resource negotiator)**
- 리소스 교섭자를 분리
- cluster compute layer (hdfs 저장소 레이어 위에서 작동)
- 위에 spark/tez 어플리케이션을 실행
	- 직접적으로 yarn 에 코딩 필요 없음
- yarn : **클러스터의 컴퓨팅 리소스 관리**
- hdfs : **클러스터의 저장 리소스 관리**

**작동 방식**
- MR script (client node) -> YARN -> Node 관리자 -> MR 애플리케이션 마스터 -> YARN -> HDFS cluster - data -> YARN
- yarn 은 애플리케이션 프로세스 (mr) 을 어디에서 실행할지 알아내는 것
	- 노드 구분
	- 분산 방법
	- 네트워크를 통한 데이터 이동 최소화
	- 클러스터의 CPU 사용 최적화
	- 데이터를 지역적으로 유지

application <-> yarn
- distribute workload to cluster
- -> zookeeper -> track master node

**실패 복원력**
- Schedule Option
	- FIFO
		- 1st application -> 2nd application
	- Capacity
		- 여유 있는 잉여 용량을 병렬로 실행
	- Fair
		- 대규모 처리 용량 일부를 가져와서 작은 작업에 할당

**Tez**
- 하이브, 피그, MR 의 작업을 가속
	- 기존 작업 방식을 관찰 후 필요 없는 단계 제거와 같이 더 효율적인 방법 검색
- MR의 대안
- 미리 설치된 기본 값으로 설정/조작할 것이 딱히 없음


**Mesos**
- distributed data management system
- fetch all hardware pool and distribute work load
- kinda like yarn but not only for hadoop
- not technically part of hadoop but some hadoop component may communicate with mesos instead of yarn

- **Myriad**
	- package that merge yarn with mesos
	- via myriad, yarn can communicate with mesos and use its resource

**mesos vs yarn**
- yarn : single scheduler
	- for big data and hadoop cluster (optimized for longer runtime)
- mesos : two tiered system
	- mesos offers resources to framework
	- for huge computing resource and multiple variant of task with different lifecycle

- 하둡 뿐만이 아니라 모든 애플리케이션을 실행할 수 있는 아키텍처에 적합
	- 분산 애플리케이션을 대량 소유 및 실행
	- best if
		- you are using Spark & Storm & analyze big data


**Zookeeper**
- 전체 클러스터에 데이터를 일관성 있게 유지하는 시스템
- 클러스터의 부분 실패를 회복하는데 사용
	- 실패
		- 개별 노드 다운
		- 네트워크 오류
		- 네트워크가 이상하게 파티션
		- 하드 드라이브 실패
- 분산 시스템의 상태를 일정하게 유지
- Specific
	- 마스터 선출 (마스터를 추적하고 그 마스터가 다운되면 상태 파악)
		- 다른 백업 마스터 하나를 선별해 새 마스터로 지정 후 추적
	- 크래시 탐지(작업자 다운)
		- 애플리케이션에 이 사실을 전달하고 작업을 다시 분산
	- 그룹 관리
		- 어떤 작업자가 가용 가능한지 추적
	- 클러스터와 관련된 메타데이터 유지
		- 미결 작업 (outstanding tasks)
		- 작업 부여 (task assignment)
	- znode (master)
		- 현재 마스터에 대한 데이터 보유
	- ```
	  /
	  |
	  - - /master   "master1.foobar.com:2223"
	  |
	  - - /workers
		    |
		     - - worker-1 "worker-2.foobar.com:2225"
		    |
		     - - worker-2 "worker-5.foobar.com:2225"
	  ```
		- master1.foobar.com 이 다운되면 해당 노드를 놓아주고 다른 노드가 그 자리를 차지
			- zookeeper 은 한번에 하나의 클라이언트만 마스터 znode의 주권을 갖도록 조정
		- 잔류성
			- 작업자에게 부여한 작업은 마스터 노드가 실패해도 잔류해야 함
	- zookeeper ensemble
		- 두개 이상의 주키퍼 서버가 필요
			- 아니면 단일 실해 지점이 됨
		- zookeeper client must have all server's list
	- **안정적인 성능을 위해 5개의 서버와 정족수 3이 필요**


**OOZIE**
- 하둡 클러스터의 주기적인 작업을 관리 (스케줄 및 실행)
	- 여러 조각에서 실행되는 액션을 정리, 한 데 묶어서 워크플로를 제작
	- 워크플로가 주기적으로 실행되도록 스케줄
- 워크플로
	- 하둡의 서로 **의존**할 수 있는 다양한 작업을 한데 묶음
		- 단일 액션
		- 연결된 액션 (MR > HIVE > Pig > Sqoop > distcp)
		- 작업간 종속성 구조 설정 가능
		- 묶인 액션에 DAG 설정 가능
- Fork Node
	- 두개 이상의 action 을 병렬로 처리할 때
- join Node
	- fork 노드의 병렬 action 이 만나는 지점
	- 두 종속성이 완료되기를 대기
- 구성 방법
	- 개별 액션이 스스로 잘 작동해야 함
		- 사전에 개발 후 테스트
			- oozie 에서 디버깅은 쉽지 않음
	- 구성 파일을 hdfs 에 저장 (xml)
	- job.properties 파일 생성
		- workflow.xml 파일에서 참조할 변수 정의
- 코디네이터
	- 시작 시간과 실행 주기를 알려줄 수 있음 (cron)
	- 필요한 입력 데이터가 가능할 때 까지 기다릴 수 있음
- 번들
	- 코디네이터를 관리 (그룹화)
		- 일괄 종료

**Apache Zeppelin**
- Spark 스크립트를 신속히 실험하고 빅데이터를 시각화해 분석하는데 사용
- 데이터에 스크립트와 코드를 상호적으로 실행하는 것
	- ipynb notebook

**Hue**
- 데이터를 쿼리하고 노트북 상호작용 및 hdfs 파일 시스템을 탐색하는 사용자 인터페이스
	- Ambari + Zeppelin

**Ganglia**
- 하둡 모니터링 시스템
- Ambari, Cloudera Manager, Grafana 가 이를 대체

**Chukwa**
- hadoop cluster의 로그를 수집하고 분석
- Flume/Kafka 가 이를 대체

**Kafka**
- Pub/Sub Message system
- 데이터가 생성되는 즉시 클러스터로 가져올 수 있음
	- 웹서버의 고객 행동 데이터 로그 -> RDBMS
	- internet sensor data
	- stock trading

- **Data source -> Cluster**
	- Kafka handles this
- **What to do with this data?**


**general purpose publish/subscribe messaging system**
 - store all incomming messages from publishers from some period of time and publishes them to a stream of data called **topic**
	- web/sensor -> kafka - publish topic -> subscribers
- consumers basically subscribe to one or more topics and they will receive data as its published
- kafka store the cutoff point so consumers can catch up from where they last left off
	- can pick up whenever they want to


**Flume**

#### Flume 개요

- **Hadoop 전용 데이터 수집 도구**로, Kafka보다 Hadoop에 특화되어 있음.
- 주로 **로그 집계(Log Aggregation)** 문제를 해결하기 위해 개발됨.
- 여러 웹 서버에서 발생하는 로그 데이터를 안정적으로 **HDFS, HBase, Hive 등**에 전달하는 역할.
- HDFS가 동시에 많은 연결을 싫어하고, 로그 데이터가 **스파이크(급격한 증가)** 할 수 있기 때문에, Flume은 **버퍼 역할**을 하며 안정적으로 전송을 보장.
#### Flume Agent 구조

Flume Agent는 세 가지 컴포넌트로 구성됨:

1. **Source (데이터 수집지)**
    - 로그 파일, Kafka, Netcat(TCP 포트), HTTP, Exec(명령어 출력), Avro/Thrift 등 다양한 입력 소스 지원.
    - **Channel Selector**를 통해 데이터의 조건에 따라 다른 채널로 분기 가능.
    - **Interceptor**를 이용해 수집 시 데이터 변형(타임스탬프 추가 등) 가능.
        
2. **Channel (데이터 이동 경로)**
    - **Memory Channel**: 속도는 빠르지만 장애 발생 시 데이터 유실 가능.        
    - **File Channel**: 디스크에 기록해 장애 시에도 데이터 보존 가능.
        
3. **Sink (데이터 저장지)**
    - HDFS, Hive, HBase, Elasticsearch, Kafka 등 다양한 목적지에 저장 가능.
    - Sink는 하나의 Channel에만 연결 가능.
    - Kafka와 달리 Flume은 데이터를 **소비 후 삭제**(임시 버퍼 성격).
        
#### 아키텍처 패턴

- **멀티 티어(fan-in) 구조** 가능:
    - 1차 Flume Agent는 애플리케이션 서버 근처에서 로그 수집.
    - 2차 Agent는 이를 모아 최종적으로 HDFS에 저장.
    - 이런 구조를 통해 **네트워크 부하 분산**, **데이터센터 간 효율적 전송**이 가능.
    - Agent 간 연결에는 주로 **Avro 프로토콜** 사용.
#### 실습 예제

1. **Netcat Source + Logger Sink**  
    - TCP 포트(Netcat)로 데이터 입력 → Memory Channel → Logger Sink에서 로그 출력.
    - 간단히 데이터 흐름을 확인하는 데 사용.
        
2. **Spooldir Source + HDFS Sink**
    - 디렉토리에 떨어지는 로그 파일 감시.
    - Interceptor(타임스탬프 추가) 적용.
    - Memory Channel → HDFS Sink로 전달.
    - 저장 시 **연-월-일-10분 단위 디렉토리 구조**로 기록, 확장성 있는 데이터 관리 가능.


**Spark Streaming**

#### 🔹 배경

- 기존 배치 처리(batch processing)는 데이터가 쌓일 때마다 한 번씩 분석 → **실시간성 부족**.
- 웹 로그, IoT 센서 데이터 등은 24/7 계속 들어오기 때문에, **실시간 분석 필요성**이 증가.
- 해결책: **실시간 스트리밍 처리 (Spark Streaming, Apache Storm 등)**.
#### 🔹 Spark Streaming 기본 개념

- **Spark Streaming**은 데이터 스트림을 **마이크로 배치(micro-batch)** 단위로 나눠 처리.
    - 예: 1초마다 들어오는 데이터를 한 덩어리(RDD)로 묶어 처리.
    - 완전한 실시간은 아니지만, **사실상 실시간 처리에 가까움**.

- Spark 클러스터의 **여러 Receiver**가 데이터를 받고, 이를 **RDD로 분할(discretize)**.
- 이 RDD들의 집합을 **DStream(Discretized Stream)** 이라 부름.
- DStream에 대해 **map, flatMap, filter, reduceByKey** 등 RDD 연산 가능.
- 상태(State) 관리 가능: 세션 데이터, 누적 합계 등 장기적인 상태 유지.
#### 🔹 윈도우(Window) 연산

- 실시간 처리에서 중요한 개념은 **Windowing**.  
- 3가지 주요 파라미터:
    1. **Batch Interval**: 데이터를 잘라내는 단위 (예: 1초).
    2. **Window Interval**: 분석 대상 기간 (예: 최근 1시간).
    3. **Slide Interval**: 얼마마다 결과를 계산할지 (예: 30분마다).
- 예: 최근 1시간 동안의 인기 상품을 30분마다 계산.
#### 🔹 코드 예시 (Python)
```python
ssc = StreamingContext(sc, 1)   
# batch interval = 1초 dstream = ... 
# DStream 정의 
windowed_counts = dstream.reduceByKeyAndWindow(     lambda x, y: x + y,       # 추가 로직     
lambda x, y: x - y,       
# 제거 로직     
300, 1                    
# window=300초(5분), slide=1초 
)

```

- 매 1초마다, 최근 5분간 데이터를 합산해 결과 출력.
#### 🔹 Structured Streaming (신버전)

- **DStream → Dataset(DataFrame 기반) API**로 발전.
- 개념: **끝나지 않는 DataFrame** → 새로운 데이터가 행(Row) 형태로 계속 추가됨.
- 장점:
    - **Batch 코드와 Streaming 코드의 차이 최소화** → 코드 재사용 용이.
    - Spark MLlib 등 다른 Dataset 기반 API와 자연스럽게 연동.
    - **머신러닝 실시간 적용 가능**.
        
- 예: S3 버킷의 로그를 실시간으로 모니터링 → 집계 → DB 저장 (몇 줄 코드로 구현 가능).
#### 🔹 Flume + Spark Streaming 예제

1. **Flume**이 디렉토리를 모니터링 (spooldir).
2. 로그를 **Avro 포맷**으로 포트에 송출.
3. **Spark Streaming**이 해당 포트를 리스닝하여 데이터 수신.
4. Spark Streaming이 로그 내 URL 등장 횟수를 5분 윈도우 단위로 집계.
5. 결과를 콘솔(또는 HDFS, DB)에 출력.

👉 요약하자면, **Spark Streaming은 데이터 스트림을 마이크로 배치로 처리하는 방식**이며,  
**윈도우 연산**과 **상태 관리**를 통해 실시간 분석을 가능하게 합니다.  
또한 **Structured Streaming**으로 발전하면서, **Dataset 기반 통합 API**로 실시간 머신러닝까지 가능해지고 있습니다.

#### 🔹 Apache Storm 개요

- **실시간(event-by-event) 스트리밍 처리 프레임워크**.
- Spark Streaming과 비슷하게 연속 데이터 스트림을 처리하지만, Spark가 **마이크로 배치(micro-batch)** 기반인 반면 Storm은 **개별 이벤트 단위**로 처리.
- 따라서 **서브 초 단위 지연(sub-second latency)** 가 필요한 경우 Storm이 유리.
- YARN 위에서 실행할 수도 있지만 기본적으로는 **독립적인 시스템**.
#### 🔹 핵심 개념

- **Stream**: 연속적으로 흐르는 데이터(튜플 단위).
- **Tuple**: 데이터 묶음(예: [5, 7, 33] 같은 리스트).
- **Spout**: 데이터 소스(예: Kafka, Twitter API, DB, TCP 포트 등).
- **Bolt**: 데이터 처리(변환, 집계, 저장 등).
- **Topology**: Spout과 Bolt들을 연결한 구조(Storm 애플리케이션). Spark의 DAG과 유사.
#### 🔹 아키텍처

- **Nimbus**: 중앙 관리 노드(Job Tracker 역할). 단일 장애점(SPOF)이지만 HA 구성이 가능.
- **Supervisor**: 실제 작업을 실행하는 워커 노드 관리.
- **Zookeeper**: 클러스터 코디네이션 담당(고가용성).
#### 🔹 개발 방식

- 주로 **Java**로 개발 (이론적으로는 모든 언어 가능하나 실제 예제는 거의 Java 중심).
- **Storm Core**: 저수준 API, _at least once_ 보장 (중복 가능).
- **Trident**: 고수준 API, _exactly once_ 보장.
#### 🔹 Spark Streaming vs. Storm

| 비교 항목 | Spark Streaming                   | Apache Storm                 |
| ----- | --------------------------------- | ---------------------------- |
| 처리 단위 | 마이크로 배치 (1초 단위 등)                 | 이벤트 단위 (진짜 실시간)              |
| 지연 시간 | 보통 ≥ 1초                           | 서브 초                         |
| 언어    | Scala, Python, Java               | 주로 Java                      |
| API   | DStream, Structured Streaming     | Core, Trident                |
| 장점    | Spark 생태계(MLlib, GraphX 등)와 통합 용이 | 진짜 실시간, 튜플 단위 처리             |
| 윈도우   | Sliding Window 중심                 | Sliding + Tumbling Window 지원 |
| 활용 예  | 로그 분석, ML 실시간 처리                  | 초저지연 처리, Kafka와 자주 함께 사용     |
#### 🔹 윈도우 처리

- **Sliding Window**: 특정 시간 구간이 겹치며 이동 (Spark와 유사).
- **Tumbling Window**: 겹치지 않는 고정 구간 단위 처리 (예: 정확히 5초마다).
#### 🔹 Storm의 활용

- **Kafka + Storm 조합**이 많이 사용됨 (실시간 로그/이벤트 처리 파이프라인).
- Spark Streaming은 점점 더 주목받고 있으며 ML과의 통합에서 강점.
- Storm은 비교적 성숙(stable)한 기술이며, 실시간성이 특히 중요한 경우 선택.
#### 🔹 예제 Topology

- **Spout**: 무작위 문장 생성.
- **Bolt 1**: 문장을 단어로 분리(split).
- **Bolt 2**: 단어 빈도수 카운트(hash map 기반).
- **출력**: 로그에 단어별 등장 횟수 출력.
- 이 토폴로지는 **계속 실행되며**, 명시적으로 중단해야 멈춤.

#### 🔹 Apache Flink 개요

- 이름 유래: **Flink = “빠르고 민첩하다(독일어)”**.
- **실시간(event-by-event) 스트리밍 처리** 프레임워크.
- 현재는 충분히 성숙해 대규모 기업(예: Capital One, Alibaba)에서도 사용.
- Spark Streaming, Storm과 경쟁하며 발전 중.
#### 🔹 주요 특징

1. **이벤트 단위 처리 (진짜 실시간)**
    - Spark Streaming(마이크로 배치)와 달리 **개별 이벤트**를 즉시 처리.
    - Storm과 유사하지만 성능(throughput) 면에서는 Storm보다 한 단계 빠름.
        
2. **확장성 & 성능**
    - 수천 개 노드까지 확장 가능.
    - Storm 대비 **10배 수준의 처리량** → 더 적은 하드웨어로 동일 작업 수행 가능.
        
3. **내결함성 (Fault Tolerance)**
    - **State Snapshot** 기능 → 장애 발생 시에도 정확히 한 번(exactly-once) 처리 보장.
    - 금융 거래 같은 민감한 데이터 처리에 적합.
        
4. **Event Time 기반 처리**
    - 데이터가 늦게 도착하거나 순서가 뒤바뀌어도 **이벤트가 발생한 시점 기준**으로 처리.
    - 예: 주식 거래 데이터가 몇 시간 늦게 도착해도 올바른 시간 순서로 반영.
        
5. **강력한 윈도우 연산**
    - 다양한 형태의 Window 지원 → 시간/이벤트 단위로 유연한 집계 가능.
        
#### 🔹 Spark & Storm과 비교

| 비교 항목  | Spark Streaming                    | Apache Storm        | Apache Flink            |
| ------ | ---------------------------------- | ------------------- | ----------------------- |
| 처리 방식  | Micro-batch (1초 단위)                | Event-by-Event      | Event-by-Event          |
| 지연 시간  | 초 단위                               | 밀리초 단위 (sub-second) | 밀리초 단위 (더 높은 처리량)       |
| 장점     | Spark 생태계(MLlib, SQL, GraphX 등) 활용 | 진짜 실시간              | 진짜 실시간 + 높은 성능 + 배치도 지원 |
| ML/SQL | MLlib, Spark SQL                   | 제한적                 | FlinkML, Table API      |
| 성숙도    | 성숙 & 광범위 사용                        | 안정적이지만 점차 감소세       | 빠르게 성장 중 (가장 젊음)        |

---

#### 🔹 Flink의 이중 성격

- **Streaming API**: 무한 데이터 스트림(event 단위 처리).
- **Batch API**: 유한 데이터셋(batch) 처리.
- 따라서 Flink는 **스트리밍과 배치를 모두 지원**하는 범용 엔진.
#### 🔹 Flink 생태계

- Spark처럼 자체 생태계를 구축 중:
    - **FlinkML** → Spark MLlib 대응.
    - **Gelly** → Spark GraphX 대응.
    - **Table API** → Spark SQL 대응.
        
- 연결 가능 시스템: **Kafka, HDFS, Cassandra, Elasticsearch, NiFi, Redis, RabbitMQ** 등.

**Impala**
- cloudera 버전의 hive
- 쿼리를 실행 시키기 위해 항상 대기중

**Accumulo**
- nosql database that can specify who can see which data

**redis**
- next-generation of memcached
- 애플리케이션이 빈번히 액세스 하는 정보를 메모리 안에 저장할 수 있게 함
	- memcache : key-value pair
	- redis : entire data structure

**ignite**
- redis의 대안
- 메모리 내 데이터 저장소와 액세스를 제공하지만 DB에 가까움
- supports
	- 메모리 내 데이터 액세스
	- ACID transaction
	- support SQL
- 분산 문서 검색 및 분석 엔진

**kinesis**
- aws 버전의 kafka

**AWS**
- kinesis = kafka
- s3 = hdfs
- cloudsearch = elasticsearch
- dynamodb = cassandra
- rds = mysql
- elasticache = redis
- elastic mapreduce = hadoop cluster

**Apache NiFi**
- 데이터 루팅의 방향성 그래프를 구성하는 방법
- kafka, hdfs, hive 등과 연결 가능
- 클러스터로 들어오는 데이터의 흐름을 체계화하는 수단으로 사용

**Falcon**
- Oozie 위에서 작동하는 데이터 거버넌스 엔진
- 클러스터에 들어온 다음에 데이터의 흐름을 관리함
- oozie에 너무 많은 워크플로가 있어서 일종의 관리자가 필요할 때 사용

**Apache Slider**
- yarn 클러스터에 분산하려는 범용 애플리케이션을 위한 도구
- 애플리케이션을 클러스터에 배치하고 모니터하며 시간에 따라 그 용량을 늘이거나 줄임

**Hadoop = hdfs + yarn + Map Reduce**

**항상 최종 사용자의 관점에서 문제에 접근**
- 거슬러 올라가 고객의 필요를 충족

1. 최종 사용자에게 어떤 액세스 패턴을 예상하는가
	1. 큰 날짜 범위에 걸친 분석 쿼리인지
	2. 대형 웹사이트에서 대량의 작은 트랜잭션이 있고 신속하게 계산해야 하는 구체적인 쿼리인지
2. 가용성
	1. availability
3. 일관성 
	1. consistency
4. Data size
	1. how big a data?
	2. how fast does it grow
		1. cluster and distributed computing
5. Data retention
	1. do you need them for auditing?
	2. how long do you need to keep them alive
	3. privacy policy
6. latency
	1. how fast should 

