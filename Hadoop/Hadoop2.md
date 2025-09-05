
**Setting up spark development environment**
1. Cloud Platforms
	- Notebook
	- Databricks Cloud
2. On-Premise Platforms
	- Python IDE
	- Cloudera Platform (hadoop)

---

**logging**

- debug : detailed information
- info : confirmation that things are working as expected
- warning : an indication that something unexpected happened
- error : due to a more serious problem, software has not been able to perform some function
- critical : serious error, indicating that the program itself may be unable to continue running


```python
# configure basic logging settings

logging.basicConfig(
    filename = 'app.log', # 해당 파일에 로그 작성
    filemode='w', # write mode
    level=logging.DEBUG, # debug mode
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', # message format
    datefmt='%Y-%m-%d %H:%M:%S', # dateformat
    force=True, # incase there is a logging config set up prior to this, it overrides
    handlers=[
	    logging.FileHandler("app1.log"), #alternate to filename
    ]
)

logging.debug('this is a debug message')
logging.info('this is a info message')
logging.warning('this is a warning message')
logging.error('this is a error message')
logging.critical('this is a critical message')
```


**logging with multiple loggers**
```python
logger1 = logging.getLogger('module1') # naming logger
logger1.setLevel(logging.DEBUG)

logger2 = logging.getLogger('module2')
logger2.setLevel(logging.WARNING)
```
- each logger are named "module1", "module2" and not "root"
- they are only allowed to log specific level (logger2 cannot do debug)


---
**Hadoop**

**Scalability**
- can scale horizontally

**Fault tolerance**
- maintains and create duplicate/replicas to avoid failure if any single machine fails

**distributed processing**
- make sure it does not move around workload between machines
- can process data where it is stored

**cost-effectiveness**
- can use inexpensive hardware / commodity machines


**hdfs**
- full form of the hadoop distributed file system
- main purpose : assist storing of your data
	- distributed storage for large data files

**map reduce**
- helps in processing large scale data by splitting jobs into smaller tasks parallely

**yarn**
- resource negotiator
- decouples your resource management from your application execution
- handles all the requests/jobs user submit


**Tools**
- **Hive**
	- query engine and not a database
	- initially, they had to write map reduce code to do anything
		- they wanted to avoid java interaction and wanted query language
		- but wanted performance of map reduce
	- **abstracts map reduce in the backend, translate sql -> map reduce jobs**
	- work on the db that is stored on that cluster

- **pig**
	- abstraction of your map reduce
	- map reduce performance but no java or sql
		- high level scripting language to work on data for MR

- **sqoop**
	- it helps facilitate import and export between hadoop & RDBMS

- **oozie**
	- abstraction of MR, uses xml file for scheduling
	- essential for scheduling complex workflows in ETL process

- **Hbase**
	- no-sql database
	- columnar db
	- gives results quickly
	- and want to have access to a particular record
	- allows access to your random access/particular row
	- **allows real-time read and writes on hdfs**

- **Mahoot**
	- implements algorithms for machine learning (& libraries) for classification, clustering, etc
	- data science componet
- **flume**
	- ingesting real time streaming data
		- logs
	- messaging queue
	- can collect logs or event data from various sources and deliver them to hadoop or hbase
-  **zookeeper**
	- make sure that it can maintain consistency
	- coordinates distributed system to maintain consistency
		- make sure the data are consistent (inligned with each other)

![[Pasted image 20250729153124.png]]

![[Pasted image 20250729153418.png]]

**storage**
- HDFS
- hbase
	- no-sql

**processing**
- pig latin
- hive

**data ingestion**
- flume
- scoop

**zookeeper**
- coordination
- maintaining consistency

**workflow management**
- uzi

**ml**
- mahout

**loosely coupled framework**
- even we remove few elements, it still works

**tightly coupled framework**
- if you remove a single thing, everything does not work

- map reduce is being taken over by spark due to its optimization and ease of writing code
- hadoop can be easily integrated with other big data technology


---

**HDFS (Hadoop Distributed File System)**
- designed to store and manage large scale dataset
	- fault tolerant characteristic
- optimized for big data
	- where files are massive and need to be processed efficiently

1. **File System**
	- method and a data structure
	- determines how you data will be stored, retrieved and organized
	- layer between os and hardware
	- ex) 
		- linux -> ext
		- windows -> NTFS, Fat32
		- mac -> apfs
		- hadoop -> hdfs (& distributed)
2. **Block**
	- smallest unit of data storage in a file system

3. **Types of File system**
	1. Standalone
		- files are stored and managed in a single machine
	2. Distributed
		- files are stored across multiple machines in a cluster
		- it is very scalable

4. **Cluster & Node**
	- each computer is a node
	- group of nodes is a cluster

5. **Process and Daemon Process**
	- Process
		- program in execution
			- google chrome, firefox, ...
	- Daemon process
		- background process that runs without any user intervention
			- annlab, ...

6. **Metadata**
	- Data about data

7. **Replication**
	- Process of making copies of our data
	- it is needed to achieve fault tolerance


**hdfs**
- distributes data across multiple nodes/machines
- replicates data to ensure fault tolerance
- it provides efficient access for big data processing

- **Architecture**
	- *for each node, role must be specified*
	- **Master (Name Node)**
		- center control room
		- does not store data
		- maintains metadata
			- f1_b1 -> 1, 2
			- f2_b2 
	- **Worker (Data Node)**
		- actual data is stored
		- given a file 300mb (F1)  -> data node 1 (100mb) - f1_b1
		-                                        -> data node 2 (100mb) - f1_b2, f1_b1 (replica)
		-                                        -> data node 3 (100mb) - f1_b3
	- if you are writing on the master node, you may use linux, but for all the other nodes, you have to use hdfs/hadoop command





























