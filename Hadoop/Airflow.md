**Airflow**
- set order of your tasks
- make sure each task starts only when the previous ones are done
- control the timing of your entire data process

- **provide dashboard where you can see status of each pipeline at a glance**
	- monitoring
- if one fails, you can spot and take action rather than discovering the problem hours later when you report doesn't arrive

- **flexibility**
	- connect to many data sources and tools
	- start small and grow as your projects get bigger
	- customize work to fit your exact needs

- open source platform to programmatically author, schedule and monitor workflows

- **Benefit**
	- Dynamic
		- easy to use and powerful
		- generate tasks based on dynamic inputs
		- generate workflow based on static inputs
		- execute different set of tasks based on condition or result
		- **if yesterday's data was higher than normal, airflow can automatically add extra tasks to process every data without manually changing anything**
	- Scalability
		- can handle both small and large amount of work
			- depend on infrastructure and budget
	- Fully functional user interface
		- monitor and troubleshoot your workflows
		- Highlight relationships between workflows and tasks
		- identify bottlenecks with performance metrics
		- manage users and roles of your airflow instance
	- Extensibility
		- can add new features or connect airflow to other tools easily
			- package with functions to interact with tool or service (AWS, Snowflake, etc)
		- Customizable user interface
		- Possibility to custom existing functions

**Airflow Components**
- **Metadata Database**
	- database that stores information about your tasks and their status
	- keep track of all important details about your workflows
- **DAG file processor**
	- parses DAG files and serializes them into metadata database
	- transforms python workflow into organized catalog that can be easily followed
- **Executor**
	- determine how your tasks will be run
	- manage the execution of your tasks, deciding whether to run them in sequence or in parallel and on which system
- **API Server**
	- provides endpoints for task operations and serving the UI
	- handle task operations sent by the executor and allows you to view, manage, monitor your workflows through web browser
- **Worker**
	- processes that actually perform the tasks
- **Queue**
	- list of tasks waiting to be executed
	- helps manage order of task execution, especially when there are many tasks to run
- **Triggerer**
	- responsible for managing deferrable tasks (tasks that wait for external events)
	- allow airflow to handle tasks that depend on external factors without blocking other processes

**Core Concepts**
- **DAG (Directed Acyclic Graph)**
	- collection of all the tasks you want to run, organized in a way that reflects their dependencies
	- **Acyclic**
		- there cannot be a loop
- **Operator**
	- single, ideally idempotent, task in DAG
	- allow you to break down your workflow into discrete, manageable pieces of work
	- basically python package with functions so it can interact with a tool or service from your workflow
	- **Types**
		- PythonOperator
			- execute python script or function
		- BashOperator
			- bash script or command
		- SQLExecuteQueryOperator
			- execute SQL query to database
		- FileSensor
			- wait for file
		- **How to find them?**
			- registry.astronomer.io/providers
- **Task / Task Instance**
	- specific instance of an operator
	- when operator is assigned to DAG it becomes a task
	- actual unit of work that get executed when DAG runs
- **Workflow**
	- entire process defined by DAG, including all tasks and their dependencies

**When to not use airflow**
- high frequency, sub-minute scheduling
- processing large datasets directly
- real-time data streaming
- simple, linear workflows with few dependencies


**Airflow Architectures**
- **Single Node Architecture**
	- a "node" is a single computer or server
	- all components of Airflow are running on one machine
	- **Example architecture**
		- **API Server**
			- allow you to access airflow user interface
			- provide endoints for task operations that will be used by other components in airflow
		- **Scheduler**
			- continuously checks for tasks that are ready to be triggered
			- **Executor**
				- Define how and on which system to run your tasks
		- **Metadata Database**
			- store all metadata related to your environment
				- task instances, DAG runs, user of airflow instance, ...
				- usually a postgresql database
		- **Worker(s)**
			- subprocess of scheduler that actually run the tasks
		- **Trigger**
			- its for the deployable operators
		- **DAG file processor**
			- parse and serialize workflows your dag file
			- **Dag file**
				- usually stored in dags folder or git repo
	- **Communication**
		- Trigger & Workers <--> API Server
		- API Server <--> Metadata Database
		- Scheduler <--> Metadata Database
		- Dag file processor <--> Metadata Database
	- **When to use single node architecture?**
		- for smaller workflows
		- easier
	- **When to use multi-node setup**
		- for better performance
		- scalability
- **Multi-node Architecture**
	- running airflow across multiple computers or servers
	- setup that ensure performance, scalability and reliablity
	- **Example Architecture**
		- **Node A**
			- **API Server 1**
			- **API Server 2**
				- can have multiple api server in case many people want to access airflow user interface, and can use "Load balancer" in front of API servers
		- **Node B**
			- **Metadata Database**
				- postgres database
					- should run on dedicated machine for scalability and security
			- **DAG File Processor 1**
			- **DAG File Processor 2**
				- in charge of parsing and serializing your DAGs, python files into metadata database
				- can have more than one (scale up)
		- **Node C**
			- **Scheduler 1**
				- **Executor 1**
			- **Scheduler 2**
				- **Executor 2**
					- celery executor
						- python framework to distribute your tasks among multiple machines
				- ensure availability by having more than one scheduler
			- **(External) Queue**
				- recieves tasks from celery executor
				- redis or rabbitmq
		- **Node D**
			- **Worker**
				- pull the tasks to run from queue
				- communicate with API servers to update status of the tasks in the airflow metadata database
		- **Node E**
			- **Worker**
		- **Node F**
			- **Worker**
	- **Benefit for this setup**
		- scalable
			- can add more workers to handle more tasks
		- reliable
			- if one machine fails, others can take over its work
		- performance
			- can process more tasks simultaneously

**Workflow**
1. User -> DAGs folder
	1. add new dag.py
2. DAGs folder -> DAG File Processor
	1. detects and parses dag.py in DAGs folder every five minutes
3. DAG file processor -> Metadata Database
	1. serialize dag file into metadata database
4. Metadata Database
	1. -> Airflow UI
		1. for human use
	2. -> Scheduler
		1. check tasks to run

Task ready to run (determined by scanning metadata database)
1. Scheduler -> Dag run
	1. create an instance of dag and the state of dag
2. Dag run -> Queue
	1. Dag Run object is queued
3. Queue -> Dag run
	1. Dag run object is in running stat
4. Scheduler -> Executor
	1. create task instance, and submits the task instance to executor
5. Executor -> task Queue
	1. task instance is queued so it run in the "correct" order
	2. this queue may be internal or external
6. task Queue -> worker
	1. task instance is in running state
7. successful task run
	1. Worker -> API server
		1. indicate success
	2. API server -> meta database
		1. update corresponding task of the state
	3. meta database -> scheduler
		1. notify task instance has been completed in success
	4. scheduler -> DAG run
		1. completed in success

---

**Running vs Active**
- running
- active
	- running but are unpaused (enabled and scheduler can trigger runs according to schedule)
	- 활성화는 됬는데 아직 실행 시간이 아님
- paused
	- 비활성화
	- 시간이 도래해도 dag run 생성 없음

**Asset**
- logical grouping of data (csv file or a table in a database)
- 파일을 등록할 수 있음 (dataset 과 같은 개념)
**Asset Event**
- 데이터에 생긴 사건
- asset can be updated or newly generated over time

**Materialize an asset**
- 실제 데이터 생성/갱신
- 










