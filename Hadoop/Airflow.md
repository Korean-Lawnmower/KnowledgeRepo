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
- 
