
[EC2] Client-server Model
- A virtual server

# **Cloud Computing**

1. Cloud-Based Deployment
	- run all parts of the application in cloud
	- migrate existing applications to cloud
	- design and build new applications in the cloud
2. On-Premises Deployment (Private Cloud)
	- Deploy resources by using virtualization and resource management tools
	- Increase resource utilization by using application management and virtualization technologies
3. Hybrid Deployment
	- Connect cloud-based resource to on-premises infrastructure
	- Integrate cloud-based resources with legacy IT applications


**Benefit of Cloud Computing**
- Trading upfront expense (data center / physical server / other resources) with variable expense (only for what you use)
- Save money that goes into managing infrastructure and servers
- stop guessing capacity
- benefit from massive economies of scale
- flexibility and agility of accessing new resources
- Going global


# **[EC2]**

* Virtual Server

On-Premis
- Spend money upfront to purchase hardware
- Wait for servers to be delivered
- Install in physical data center
- Make necessary configurations

[EC2]
- launch instance within minutes
- stop using them when you have finished running workload
- pay only for "compute time"
- can save costs by paying only for server capacity

Hypervisor
- hypervisor running on host machine is responsible for sharing underlying physical resources between VM
- responsible for coordinating multi-tendancy
- responsible for isolating VM from each other as they share resources

[EC2] - Types
1. General purpose instances
	- application servers
	- gaming servers
	- backend servers for enterprise applications
	- small and medium databases
2. Compute optimized instances
	- ideal for compute-bound applications (high-performance processors)
	- ex) dedicated gaming server, high-performance web server
3. Memory optimized instances
	- deliver fast performance for workloads that process large dataset in memory
	- ex) require large amount of data to be preloaded before running application
4. Accelerated computing instances
	- use hardware accelerators, core-processors
	- ex) floating-point number calculations, graphics processing, data pattern matching, graphics applications, game streaming, application streaming
5. Storage optimized instances
	- workload that require high, sequential read and write access to large datasets on local storage
	- ex) distributed file systems, data warehousing applications, high-frequency online transaction processing systems

[EC2] - pricing
1. On-Demand (most expensive)
	- short-tem irregular workloads that cannot be interrupted
2. Reserved Instances
	1. Standard reserved instances
		- if you know the size you need for steady-state applications in which AWS Region you plan to run them
		- Must State (Instance type and size, Platform descriptions (OS), Tenancy)
	2. Convertible reserved instances
		- if you run EC2 instances in different availability zones or different instance types
		- can continue using EC2 instance without interruption
		- charged on-demand rates until
			- terminate instance
			- purchase a new reserved instance that match instance attributes
	3. EC2 instance saving plans
		- reduce EC2 instance cost when you make and hourly spend commitment to an instance family region for 1 or 3 year term (upto 72% discount)
	4. Spot Instances
		- Ideal for workload with flexible start and end times that can withstand interruptions (upto 90% discount)
	5. Dedicated Hosts
		- physical servers tih EC2 instance capacity that is fully dedicated to your use


[EC2] - Scalability
**Amazon EC2 Auto Scaling**
- Frequent time out : recieved more requests than it was able to handle
- Auto scaling enable you to automatically add or remove EC2 instances in response to changing application demand
1. Dynamic Scaling
	* Response to changing demand
2. Predictive Scaling
	- Schedules the right number of EC2 instances based on predicted demand

*! to scale faster, you can use dynamic scaling and predictive scaling together*

**Auto Scaling Group**
* There must be at least one EC2 instance running at all times
* Minimum capacity : number of EC2 instances that launch immediately after you have created the auto scaling group
* Desired Capacity : desired number of instances (if not specified, it is set to minimum capacity)
* Maximum capacity : limit of capacities that can be made

**Elastic Load Balancing**
- automatically distributes incoming application traffic across multiple resources such as EC2 instances
- Acts as *Single Point of Contact* of all incoming web trafic to your auto scaling group
	- as you add or remove EC2 instances, requests route to load balancer first
- Elastic load balancing and EC2 auto scaling work together to ensure high performance and availability

# **Monolithic Applications and Microservices**

**Monolithic Application**
- architecture regarding applications with tightly coupled components
	- Databases, servers, user interface, business logic
	- Communication between components : transmit data, fulfill requests
*! If one component fails, other components fail as well and the entire application fail*

**Microservices**
- application components are "Loosely Coupled"
	- prevents entire application from failing
- Amazon service regarding Microservices
	- Simple Notification Service (Amazon SNS)
	- Amazon Simple Queue Service (Amazon SQS)

**Amazon Simple Notification Service (Amazon SNS)**
* Type : publish/subscribe service
* Summary : publisher publishes message to subscribers
* Subscribers : Web servers, email addresses, AWS Lambda function, ...
1. *Publishing updates from single topic*
	- Newsletter that includes information regarding "Coupons, coffee trivia, new products, ..."
2. *Publishing updates from multiple topics*
	- Each newsletter is devoted to a specific topic and subscribers only recieves immediate updates regarding topics to which they have subscribed

**Amazon Simple Queue Service (Amazon SQS)**
- Summary : can send and recieve message between software components without losing messages or requiring other services to be available
- Application send messages into a queue (lets messages between decouples applications complements)
- user or service retrieves a message from queue, process it and deletes it from queue
- Even if the barista is out on a break or busy with another order, cashier can continue placing new orders into the queue
- *Decoupled approach enables the separate components to work more efficiently and independently*


# **Serverless Computing [AWS Lambda]**

**Steps for Running application in EC2**
1. Provision Instances (Virtual Servers)
2. Upload Code
3. Continue to manage instances while application is running

*Serverless*
- Code runs on servers, but do not need to provision or manage these servers

*Benefit of Serverless*
* Flexibility to scale serverless applications automatically
* Can adjust the application's capability by modifying the units of consumptions (such as throughput and memory)

[AWS Lambda]
- let you run code without needing to provision or manage servers
- you pay only for the compute time that you consume
- ex) automatically resizing uploaded images to AWS cloud - triggered when new image is uploaded

**Steps of Lambda**
1. Upload code to Lambda
2. set your code to trigger from an event source (ex: AWS services, mobile applications, HTTP endpoints)
3. Lambda runs code only when triggered
4. pay only for compute time (compute time that you use when uploading new images)

**Containers**
- containers provide a standard way to package your application's code and dependencies into single object
- use containers for process and workflows in which there are essential requirements for security, reliability and scalability
1. One host with multiple containers
	- When the development environment is different from the user's environment
2. Tens of hosts with hundreds of containers
	- manage tens of hosts with hundreds of containers

**Amazon Elastic Container Service [Amazon ECS]**
- highly scalable, high-performance container management system that enables you to run and scale containerized applications
- Supports Docker containers
	- can use API calls to launch and stop Docker-enabled applications
- Docker
	- software platform that enables you to build, test, and deploy applications quickly

**Amazon Elastic Kubernetes Service [Amazon EKS]**
- [Kubernetes]
	- open-source software that enables you to deploy and manage containerized applications at **scale**

**[AWS Fargate]**

- Serverless compute engine for containers
- works with both [Amazon ECS] and [Amazon EKS]
- Do not have to provision or manage servers
- Pay only for the resources that are required to run your containers


# Amazon Global Infrastructure

* can run applications in desired places

**Determining the right region**
1. Compliance with data governance and legal requirements
- ex) if your company requires you to have data reside within UK, you could choose London
2. Proximity to customers
- Selecting region that is close to customers will help their runtime faster
3. Available services within a region
- Not all regions support all features that one might look for. If so, user must choose the region that offers the service
4. Pricing
- Pricing may be differently set-up based on countries
- ex) Brazil’s tax structure may cost 50% more to run the same workload in the US

**Region**
- Region consist of three or more availability zone

**Availability Zone**
- single, or group of data center within a region
- maintain low latency between each other
- distant enough so that a disaster may not affect all of them in the same zone

*! Best practice is to run application across at least two availability zones in a region*

**Edge Location** [Amazon Edge Location]
- ex) Data is hosted from Tokyo Region but you have customers in Mumbai
- place a copy locally or cache a copy in Mumbai (CDN) (Content Delivery Networks)


Runnable programs of Edge Location
1. [CDN Amazon CloudFront]
- help deliver data, video, applications and APIs with low latency and high transfer speeds

2. [Amazon Route 53]
- run a domain name service (DNS)
- help direct customers to the correct web locations with low latency

[AWS Outposts]
- aws will install fully operational mini region inside your own data center
- owned and operated by AWS

# API calls
- every AWS service is run by api calls (EC2, Lambda, …)

[AWS Management Console]
- browser-based console
- can access service by name, keyword or acronym
- can use mobile application for monitoring resources, alarms, and access billing information
- multiple identities can stay logged in to AWS and app at the same time

[Command Line Interface (CLI)]
- allow you to make API calls using terminal on your machine
- make actions scriptable and repeatable
- available for Windows, macOS, Linux
- ex) use command to launch EC2 instance, connect to EC2 instance to specific Auto Scaling group

[AWS Software Development Kits (SDK)]
- allow you to interact with resources through various programming languages
- support C++, Java, .NET and more

[AWS Elastic Beanstalk]
- help provision EC2 based environments
- provide application code and desired configurations to build your environment
- easier to save environment configurations
Possible tasks
1. Adjust capacity
2. Load balancing
3. Automatic Scaling
4. Application health monitoring

[AWS Cloud Formation]
- infrastructure as code tool that allow you to define AWS resources in a declarative way using JSON or YAML text based documents (CloudFormation templates)
- support [EC2], storage, database, analytics, ml and more
Steps
1. Parse the template
2. Begin provisioning all resources you defined in parallel
3. Manage all calls to backend AWS APIs for you
- can run the same CloudFormation template in multiple accounts or regions
- rolls back changes automatically if it detects errors




# Networking

[Amazon Virtual Private Cloud (VPC)]
- let you provision a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define
- These can be public facing (with internet access) or private (with no internet access)

**Subnets**
- private and public grouping of **resources**
- they are ranges (chunks) of IP addresses in your VPC that allow you to group resources together

- ex) cashier : public subnet
- ex) barista : private subnet

[Amazon Virtual Private Cloud (VPC)]
- VPC allow you to define your private IP range for your AWS resources and you place things like EC2 instances and ELBs inside VPC

**Public Facing Resources**
- Attachment to **Internet Gateway**, IGW is essential
	- allow public traffic to enter VPC

**Virtual Private Gateway (VPG)**
- allows only particular target of people to your network
- Still have a regular internet connection that has a bandwidth that is being shared by many people using the internet
- enables you to establish Virtual Private Network between VPC and private network (such as on-premise data center)

[AWS Direct Connect]
- allow you to establish completely private dedicated fiber connection from data center to AWS
- you can work with Direct Connect partner in your area to establish this connection as it requires physical line that connect your network to VPC
- one VPC might have multiple types of gateways attached for multiple type of resources all residing in the same VPC, just in different subnets

**Security**
- Network hardening
- Application security
- User identity
- Authentication and Authorization
- Distributed denial-of-service (DDoS prevention)
- Data integrity
- Encryption


**Subnet**
- Can control traffic permissions

**Packet**
- unit of data sent over the internet or a network
- Every packet that cross subnet boundaries gets checked against network access control list 

**Network Access Control List (NACL)**
- Check if packet has permission to leave or enter subnet based on who it was sent from and how its trying to communicate
- both in and out is checked
- does not evaluate if a packet can reach specific EC2 instance
- default : stateless and allows all inbound and outbound traffic

**Security Groups**
- deal with instance level access questions
- default : does not allow any traffic into instance (all ports and IP addresses sending packet are blocked)
- can be modified

**NACL vs Security Groups**
- Security Groups: stateful (has memory regarding who to allow in or not)
- NACL : stateless (remembers nothing and checks every single packet that crosses its border regardless of circumstances)

**Journey of “A”**
Goal : Instance A -> packet -> Instance B
Path : Instance A -> packet -> boundary of security group instance A -> default : all outbound traffic is allowed from security group -> subnet boundary -> Network ACL -> if target address is allowed, can continue -> enter target subnet boundary -> Network ACL -> have to be on approved list for this subnet -> enter subnet -> approach target instance B -> if packet on the list of security group -> reach target instance B

**Return Journey**
- Security Group instance B does not stop any leaving packet
- NACL check the packet (if it's on the list) for leaving instance B
- NACL check the packet for returning to instance A
- Security Group recognize the packet from before and allows it back in


# Global Networking

**Domain Name System (DNS)**
- Customer's DNS resolver interacting with Company's DNS server
1. Enter domain name into browser
2. The request is sent to *Cutomer DNS Resolver*
3. Customer DNS resolver asks the *Company DNS Server* for IP address that correspond to website
4. company DNS server responds by providing the IP address for website "192.0.2.0"




[Route 53] - Domain Name System (DNS)
- Highly available and scalable
- Connects user requests to infrastructure running in AWS (such as EC2 and load balancer)
- Can buy and manage domain names on AWS
- Can transfer DNS records for existing domain names managed by other domain registrars.

**Route 53 Policies**
- Latency-based routing
- geolocation DNS
	- direct traffic based on where the customer is located
- geoproximity
- weighted round robin

[AWS CloudFront]
- can speed up delivery of website assets to customers
- **CDN** - network that helps deliver edge content to users based on geographic location

**Client Data retrieval journey**
1. Customer request data from application by going into website
2. [AWS Route 53] use DNS resolution to identify website's corresponding IP address and send it back to customer
3. customer's request is sent to nearest edge location through [Amazon Cloud Front]
4. [Amazon Cloud Front] connects to the [Application Load Balancer], which sends the incoming packet to EC2 instance

# Storage and Databases

**Application Needs**
- CPU
- Memory
- Network
- Storage

**Block-level Storage**
- place to store files
- file : serious of bytes that are stored in blocks on disc

**Hard Drives of EC2 Instance**
- Initial Volume : local storage called ***instance store volumes***
	- physically attached to the host
	- if EC2 instance stop, all data written to the instance storage volume will be lost
	- if you start instance from a stop state, most likely will start up on another host
	- They are useful in situations where you can lose the data (temporary files, scratch data, ...)

[Amazon Elastic Block Store] (EBS)
- can create virtual hard drives that can be attached to EC2 instances
	- EBS volumes
- They are not tied to the host EC2 is running on
- Can persist between stop and start of instances
- Initial volume allocation
	- Define
		- Size
		- Type
		- configurations
- Important to back up EBS
	- **Snapshots**
		- Incremental backups
		- Can restore data from a snapshot

**Object Storage**
- Each **object** Consist of : Data, Metadata, Key
- Data
	- Image
	- Video
	- Text Document
	- other type of ifle
- Metadata
	- information about what the data is
	- how the data is used
	- object size
- Key
	- Unique identifier

**Object Storage vs Block Storage**
Object Storage
- if a file is modified, the entire object is updated
Block Storage
- only the pieces that are changed are updated

[==Amazon S3] (Amazon Simple Storage Service)==
- Data are stored as **objects** and stored in "buckets"
- Maximum object size that you can upload is 5 terabytes
- Can **version** objects to protect them from accidental deletion
- Can create multiple buckets and store them across different classes or tiers of data
- Can create permissions to limit who can see or access objects
- Can stage data between different tiers
	- tiers offer mechanisms for different storage use cases
		- Data that needs to be frequently accessed
		- Data that needs to be retained for several years

**Factors that need to be considered**
- How of the will the data be retireved
- How available does the data need to be

**S3 Standard**
- **Designed for frequently accessed data**
- 11 nines of durability
	- object stored in S3 Standard has a 99.999999 percent
	- remain intact after a period of 1 year
- Can sustain concurrent loss of data in two separate storage facilities
	- Stored in at least three facilities - **High availability**
		- multiple copy reside across locations

**Static Website Hosting**
- Static Website : collection of HTML files
	- Does not change, whereas dynamic website's content change based on location or actions the user has made on the page before
- Can upload all HTML, static web assets, etc, into a bucket and checking a box to host it as a static website
	- Can access the webpage by entering *Bucket's URL*
	- Can still have animations and moving parts

**S3 Standard-Infrequent Access (S3 Standard-IA)**
- Used for data that is accessed less frequently but requires rapid access when needed
- Mainly used for : Backups, disaster recovery files, any object that require long-term storage
- Also stored in a minimum of three Availability Zones
- Lower storage price and higher retrieval price than S3 Standard

**S3 One Zone-Infrequest Access (S3 One Zone-IA)**
- Store data in a single Availability Zone
- Has a lower storage price then **S3 Standard-IA**
	- you can save cost on storage
	- you can easily reproduce your data in event of an availability zone failure

**S3 Intelligent-Tiering**
- ideal for data with unknown or changing access patterns
- requires a small monthly monitoring and automation fee per object
	- If you have not accessed the object in 30 consecutive days, automatically moves to "infrequent access tier (S3 Standard-IA)"
	- If you access object in infrequent access tier (S3 Standard-IA), moves to frequent access tier (S3 Standard)

**S3 Glacier Instant Retrieval**
- works well for archived data that requires immediate access
- can retrieve object within few milliseconds (same as S3 Standard)

**S3 Glacier Flexible Retrieval**
- Low-cost storage designed for data archiving
- Retaining data for several years but do not have to be retrieved very rapidly (take few minutes to hours)
- Can move data to it
- Can create vaults and populate them with archives
- **S3 Glacier vault lock policy**
	- if you have anything  compliance requirements
	- can specify controls such as write once, read many (WORM)
	- Once locked, policy can no longer be changed
- **Retrieval policy**
	- minutes to hours
	- can upload directly to S3 Glacier Flexible Retrieval
	- can use S3 Lifecycle policies

**S3 Glacier Deep Archive**
- Lowest-cost object storage ideal for archiving
- Able to retrieve object within 12 hours
	- ideal for data that might accessed once or twice in a year
- all objects are replicated and stored across at least three geographically dispersed Availability Zones

**S3 Lifecycle Policies**
- They are policies you can create that can move data automatically between tiers
- ex) S3 Standard (90 days) -> S3 Standard-IA (30 days) -> S3 Glacier Flexible Retrieval
- can create configuration without changing your application code and will perform moves automatically

**S3 Outposts**
- Delivers object storage to your on-premise AWS Outposts environment
- Create S3 buckets on Amazon S3 Outposts
- Makes it easier to retrieve, store, and access data on AWS Outposts

#### EBS vs S3

**Use case #1** - **S3**
- *People upload photos on the web and the website searches for animal picture that looks like them*
- Web Enabled
	- Every object already has a URL that you can control access rights
- Regionally distributed
	- durable so do not have to worry about backup strategies
- Cost savings
	- Advantage of being serverless (no EC2 instance needed)

**Use case #2 - EBS**
* *Frequently updating a 80GB video file*
	* As EBS breaks down big files into smaller component (blocks), the engine only updates the blocks where those bits live
* If you use S3, you would have to upload 80GB every single time (object storage)

#### Amazon Elastic File System [Amazon EFS]
- Can keep existing file systems in place but let AWS do scaling and replications
- EFS allows you to have multiple instances accessing data in EFS at the same time

**Amazon EBS vs EFS**
- Amazon EBS volumes attach to EC2 instances and are an **single Availability Zone-level resource**
	- EC2 and EBS has to be in the same AZ
- Amazon EFS can have **multiple** instances reading and writing from it at all the same time
- EC2 instance in the region can write to EFS file system
- on-premises servers can access Amazon EFS using AWS Direct Connect


#### Amazon Relational Database Service ([Amazon RDS])

**RDBMS**
- Store data in a way such that it relates to other pieces of data
- Store each information in a corresponding table and relate tables on a common attribute

**DBMS systems**
- MySQL
- PostgreSQL
- Oracle
- Microsoft SQL Server
- Maria DB
- [Amazon Aurora]

**Migrating database to run on EC2**
- have control over the same variables as on-premises environment

**[Amazon RDS]**
- supports all major database engines
- include automated patching, backups, redundancy, failover, disaster recovery
- can integrate Amazon RDS with other services to fulfill your business and operational needs ex) [AWS Lambda] to query database from a serverless application

**[Amazon Aurora]**
- most managed relational databse
- MySQL
- PostgreSQL
- 1/10th cose of commercial grade databases
- 6 copies across facilities
- deploy up to 15 read replicas
- Continuous backups to [S3]
- include point in time recovery : recover data from a specific period

**[Amazon DynamoDB]**
- Non-relational database
	- Does not use SQL
	- Does not use schema (relations)
		- RDBMS may have performance and scaling issues when under stress
		- allows to be less rigid and allow access at a very high rate
	- you would write queries based on small subset of attributes that are designed as keys
	- it has specific use cases and in not the best fit for every workload
- Serverless database that you don't need to manage underlying instances or infrastructure powering it
- stores data redundantly across availability zones and mirrors data across multiple drivers for you
- Auto-Scaling
	- as size of database shrink or grows, dynamoDB automatically scales to adjust while maintaining consistent performance

**DynamoDB Tables**
- place where store and query data
- Data is organized into items
- Items have attributes

**No-SQL**
- Key-Value pairs
	- items (keys)
		- attributes (values) - different features of data
	- you can add or remove attributes from items in the table at any time
	- not every item in the table has to have same attributes

![[Pasted image 20240609171722.png|600]]
**[Amazon RDS] vs [DynamoDB]**
- **AmazonRDS**
	- Automatic high availability
	- Customer ownership of data
	- Customer ownership of schema
	- Customer control of network
- **DynamoDB**
	- Key-Value
	- Massive throughput capabilities
	- PB size potential
	- Granular API access

**Case #1** - Sale supply chain management system that you have to analyze for weak spots
- RDBMS is built for business analytics
- **Amazon RDS**

**Case #2** - Anything Else
- most of what people use RDBMS for has nothing to do with complex databases
- they just end up being lookup tables
- contact info - single table territory
- **Dynamo DB**


#### Amazon Redshift

**Data warehouses**
- engineered specifically for big data
- Designed for "historical analytics" opposed to "operational analytics (real-time)"

**Historical Analytics**
- how many coffee did we sell on the last hour?
	- we are not selling coffee from the last hour, it is now in the past

**[Amazon Redshift]**
- Data Warehousing as a service
- massively scalable
- you can run a single SQL query against exabytes of unstructured data running in data lakes

#### AWS Database Migration Service

**Amazon Database Migration Service [Amazon DMS]**
- allows migration on existing database onto AWS whether its on-premise or from another cloud service
- source and target databases don't have to be of the same type

**Homogeneous Migrations**
- MySQL to Amazon RDS for MySQL
- Microsoft SQL Server to Amazon RDS for SQL Server
- Oracle to Amazon RDS for Oracle
- Schema structures and database code is compatible between source and target

**Heterogeneous Migrations**
- When the source and target databases are of different types
	1. Convert Schema, data types, database code using [AWS Schema Conversion Tool]
	2. Use DMS to migrate data from source to target database

**Other DMS Use Case**
- development and test database migrations
	- develop this to test against production data but without affecting production users
	- Make copy of your production database to your dev or test environments
- database consolidation
	- When you have several databases and want to consolidate them into one central database
- continuous database replication
	- for disaster recovery or for geographic separation

#### Additional Database Services

**Choosing the right database**
- there is no one-size-fits-all database for all purposes

**[DocumentDB]**
- great for content management, catalogs, user profiles

**[Amazon Neptune]**
- graph-database engineered for social networking and recommendation engines
- Also great for fraud detection needs

**Amazon Quantum Ledger Database [Amazon QLDB]**
- immutable system of record where any entry can never be removed from the audits

**[Amazon Managed Blockchain]**
- use to create and manage blockchain networks with open-source frameworks

**Speeding up Databases**
- [Amazon ElastiCache]
	- adding caching layers on top of your database to help improve read times of common requests
	- Memchached
	- Redis
- [DynamoDB Accelerator]
	- native caching layer designed to dramatically improve read times for your nonrelational data


# Security

**Shared responsibility model**

*Responsible for EC2, S3, RDS*
- both customer and AWS

- Customer
     - Responsible for security "in" the cloud
     - OS
     - Applications
     - Data
- AWS
     - Responsible for security "of" the cloud
     - Hypervisor
     - Network
     - Physical

![[Pasted image 20240707163030.png]]


**[AWS IAM] (AWS Identity and Access Management)**
- IAM enables you to manage AWS services and resources
- Features
	1. IAM users, groups, roles
		1. IAM users
			1. identity that you create in AWS initially has no permissions
		2. IAM groups
			1. collection of IAM users
			2. all users in group are granted permissions specified by the policy
		3. IAM roles
			1. One abandon all previous permissions and take permission of the new role.
			2. Identity that you can assume to gain ***temporary*** access to permissions
	2. policies
		1. enables you to customize users' level of access to resources
		2. *! BEST PRACTICE : least privilege when granting permissions*
	3. MFA
	4. 
- Root User
	- *! Do not use root user for everyday task*
		- Create first IAM user and assign permission to create other users

**AWS Organizations**
- Can consolidate and manage multiple AWS accounts within a central location
- Creates a root account upon creation of "Organization"
	- Consolidated billing
- **Organizational Units**
	- group accounts into OUs to make it easier to manage accounts with similar business or security requirements
		- Can group accounts to meet certain regulatory requirements

**SCP (Service Control Policies)**
- Can centrally control permissions for the accounts
- "Controllable" Units
	- Organization root
	- Individual member account
	- OU (Organizational unit)

**[AWS Artifact]**
- service that provide on-demand access to AWS security and compliance reports and select online agreements

[AWS Artifact Agreements]
- sign agreements with AWS regarding user of certain types of information throughout AWS services
- Unit : Individual account / all account in AWS Organizations

[AWS Artifact Reports]
- provide compliance reports from third-party auditors
- tested and verified that AWS is compliant with a variety of global, regional and industry-specific security standards and regulations

[Customer Compliance Center]
- resources that help with AWS compliance

**Denial of service (DoS) Attacks**
- deliberate attempt to make a website or application unavailable to users
- Attacks
	- excessive network traffic to cause overloaded application

**Distributed denial-of-service (DDoS) attacks**
- Distributed attacks with the usage of multiple infected computers ("Bots")

[AWS Shield]
- protect applications against DDoS attacks
- **Standard**
	- Automatically protects all AWS customer at no cost
- **Advanced**
	- paid service that provide detailed diagnostics and provide detection and mitigate sophisticated DDoS attacks
	- integrated with other services such as CloudFront, Route 53, ELB.

**[Amazon Key Management Service (AWS KMS)]**
- encrypt operation through use of **cryptographic keys**

[Amazon WAF (Web application firewall]
- application firewall that let you monitor network requests that come into your web applications
- Web access control list (ACL)
	- rule and configurations ex) ip ban

[Amazon Inspector]
- help improve security and compliance of applications by running automated security assessments
	- provide list os security findings that is prioritized by severity level
	- does not guarantee that finding will resolve every potential security issue

[Amazon GuardDuty]
- Service that provide intelligent threat detection for AWS infrastructure
1. Enable Amazon GuardDuty
2. Continuously analyze network and account activity
3. detect threats
4. review detailed findings and take action
	- can review findings in AWS management console

**[AWS CloudWatch]**
- web service that enables you to monitor and manage various metrics and configure alarm actions based on data from those metrics
- **Metrics**
	- data about the performance of your systems
- **Alarms**
	- automatically perform actions if value of metric has gone above or below a predefined threshold
- **Dashboard**
	- enables you to access all the metrics for your resources from a single location

**[AWS CloudTrail]**
- Records API calls for your account
	- Identity of API Caller
	- Time of the API Call
	- Source IP Address of the API Caller
	- etc
- updated within 15minutes after an API call
- **CloudTrail Event History**
	- can track down history of your inquiry
- **CloudTrail Insights**
	- detect unusual API activities in your AWS account

**[AWS Trusted Advisor]**
- Web service that inspect your AWS environment and provide real-time recommendations
- **Cost optimization**
- **Performance**
- **Security**
- **Fault Tolerance**
- **Service Limites**

**[AWS Free Tier]**
- Always Free
- 12 Months Free
- Trials

**[AWS Pricing]**
- Pay for what you use
- Pay less when you reserve ahead of time
- Pay less with volume-based discounts

- [AWS Lambda] (How to save)
	- cost by number of requests and time it takes for them to run
	- Compute Savings Plan
		- committing to consistent amount of usage over 1-year, 3-year term
			- **Paying less when you reserve**
- [Amazon EC2] (How to save)
	- cost by compute time
	- Spot Instances (batch processing job that withstand interruptions)
		- 90% cost savings
- [Amazon S3]
	- Storage
	- Requests and Data Retrievals
	- Data Transfer
		- No fee between data transfer within AWS service in same region
		- No cost for data transfer into S3 from internet or out to CloudFront
		- No cost for data transfer out to EC2 in the same region as the bucket
	- Management & replication
		- Pay for storage management features you have enabled on your account

[Billing Dashboard]
- Compare your month-to-date balance with previous month, and get forecast of the next month
- view month-to-date spend by service
- view free tier usage by service
- access cost explorer and create budgets
- purchase and manage saving plans
- publish aws cost and usage reports

**AWS Budgets**
- visualize, understand and manage AWS costs and usage over time (for your top five cost-accruing AWS services)

**Consolidated Billing (Feature of AWS Organizations)**
- enable you to receive single bill for all AWS account in your organization
- max number of account is 4
- enables to share volume pricing discounts across accounts

[AWS Budgets]
- set maximum usage limit

[AWS Support Plans]
- Basic
	- limited selection of AWS Trusted Advisor checks
	- **AWS Personal Health Dashboard**
- General
	- Unlimited number of technical support cases
		- pay by the month pricing
	- Developer
		-  best practice guidance
		- client-side diagnostic tools
		- building block architecture support
	- Business
		- all Trusted Advisor checks
		- use-case guidance
	- Enterprise On-Ramp
		- pool of technical account managers to provide proactive guidance and coordinate access to programs and AWS experts
		- Cost Optimization workshop
		- concierge support team for billing and account assistance
	- Enterprise
		- All previous features
		- Technical Account Manager (TAM) is only available for Enterprise or Enterprise On-Ramp

[AWS Marketplace]
- digital catalog that include softwares from independent software vendors

**[Cloud Adoption Framework]**
- organizes guidance into six **Perspectives**
- **Business**
	- ensure IT align with business needs (IT investments link to key business results)
- **People**
	- development of organization-wide change management strategy for successful cloud adoption
- **Governance**
	- focus on skills and processes to align IT strategy with business strategy
- **Platform**
	- include principles and patterns for implementing new solutions on the cloud and migrating on-premises workload
- **Security**
	- ensure organization meets security objectives for visibility auditability, control and agility
- **Operations**
	- help you enable, run, use, operate and recover IT workloads to agreed level

