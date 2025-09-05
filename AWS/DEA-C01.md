#### Data Mesh
- individual teams owns "data products" witin a given domain
- these data products serve various use cases around the organization
- **domain-based data management**
- federated governance with central standards

#### ELT
**Extract**
- retrieve raw data from source system
	- db, crm, flat files, api, etc
- ensure data integrity during extraction phase
- real-time, batches


**transform**
- data cleansing
- data enrichment
- format changes
- aggreagations or computations
- encoding or decoding data
- handling missing values

**Load**
- transformed data to target data warehouse or other data repo
- done in batch/streaming manner

[AWS Glue]
- etl or elt data as recieved

**orchestration services**
- eventbridge
- managed workflows for apache airflow [Amazon MWAA]
- step functions
- lambda
- glue workflows


#### Data Sources

**JDBC**
- java database connectivity
- platform-independent
- language-dependent
	- if not using java, you might need intermediate layer

**ODBC**
- open database connectivity
- platform-dependent (need specific drivers to access)
- language-independent

**Raw logs**
**API's**
**Streams**


#### Data Formats
- CSV
	- tabular forma
- JSON
- Avro
	- binary format that stores both data and schema
	- appropriate for big data and real-time processing data
	- Systems
		- Kafka
		- Spark
		- Flink
		- Hadoop ecosystem

- Parquet
	- Columnar storage format optimized for analytics
	- allow efficient compression and encoding schemes
		- reading specific columns instead of entire records
		- storing data on **distributed systems**
	- Systems
		- Hadoop, Spark, Hive, Impala, Redshift Spectrum

#### Data Modeling
**Star Schema**
- Fact Tables
- Dimensions
- Primary / foreign keys

- *multi dimensional data model used to organize data in a database so that it is easy to understand and analyze*
- *design is optimized for querying large datasets*
![[Pasted image 20250730174146.png]]



**Data Lineage**
- visual representation that traces the flow and transformation of data through its lifecycle from its source to its final destination
- **importance**
	- help track errors back to its source
	- ensure compliance
	- provide clear understanding of how data is moved, transformed, consumed within systems

![[Pasted image 20250730174731.png]]

**Schema Evolution**
- ability to adapt and change the schema of a dataset without disrupting existing procrsses or systems
- Allows addition, removal, modification of columns/fields in a dataset

**Glue Schema Registry**
- Schema discovery, compatibility, validation, registration

### db performance optimization
- **indexing**
- partitioning
- Compression
	- Speed up data transfet, reduce storage, disk reads
	- Eg) columar compression

**Data sampling**
1. Random sampling
	- Everything has equal chance
2. Stratified sampling
	- Divide population into homogenous subgroups
	- Random sample within each stratum
	- Ensure representation of each subgroup
- Systemic sampling
	- pick every 3rd item
	- Choosing by rule
- cluster, convenience, judgemental


**Data Skew**
- Uneven distribution or imbalance of data across various modes or partitions in distributed systems
- **Causes**
	- Non uniform distribution if your data
	- Inadequate partitioning strategy
	- Temporal skew

- **How to fix it**
	- Adaptive partitioning
		- Dynamically adjust partitioning based on data characteristics to ensure more balanced distribution
	- Salting
		- Introduce random factor (salt) to the data to distribute it more uniformly
	- Repartitioning
		- Regularly redistribute the data based on its current distribution characteristics
	- Sampling
		- Sample of data to determine the distribution and adjust the processing strategy accordingly
	- Custom Partitioning
		- Define custom rules or functions based on domain knowledge

**Data validation and profiling**
- Completeness
	- ensure all required data is present and no essential part are missing
- Consistency
	- ensure data values are consistent and do not contradict
- Accuracy
	- correct, reliable and represent what is intended
- Integrity
	- ensure correctness and consistency over lifecycle across system

### [s3]
- created in a "region"
- bucket > object (files)

**objects**














