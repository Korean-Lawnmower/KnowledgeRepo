# What is cloud?

**On-Premises**
- Before
	- hosted and maintained hardware such as compute, storage and networking equipment in their own data centers
**Cloud**
- Cloud computing is on-demand delivery of IT resources over the internet

**Hybrid**
- Connect on-premises with cloud

**Advantage of cloud computing**
1. Pay as you go
2. Benefit from massive economies of scale
3. Stop guessing capacity
4. Ignore speed and agility
5. Realize cost savings
6. Go global in minutes

# AWS Global Infrastructure

**Region - Availability Zone - Data Center**

**Region**
- **US East -1** 
- **US West -1**

**Availability Zone (AZ) - not explicitly mentioned (unselectable)**
- US East - 1**a**
- US East - 1**b**
- AZ consist of one or more data centers with **redundant power, networking, and connectivity**

**Deployment**
- Availability Zone level
- Region level
- Global level

**Edge Locations** [AWS CloudFront]
- Global locations where content is cached
- upon user request, the request is routed to the location that provide lowest latency

- if a service does not ask you to select a availability zone, it runs on a region-scoped service.
- Make sure services (workloads) are replicated across multiple availability zones
	- if an AZ fails, your application will have infrastructure still running at the second AZ (taking over traffic)

### Choosing the right AWS region
- **Latency**
	- based on your target users, you can select the region
- **Pricing**
	- The pricing of the same services may vary based on regions
- **Service Availability**
	- not all services are supported in all regions
- **Data Compliance**
	- if a company's compliance needs require you to keep all data/infrastructure within a specific region

# Interacting with AWS

**AWS Management Console**
**AWS CLI**
**AWS SDKs**
- API calls to AWS (boto3)
- C++, Go, Java, JavaScript, ...


# Shared Responsibility Model

![[Pasted image 20250103132714.png]]

**AWS responsibility Classification**

1. Infrastructure Services
	- Compute Services (EC2)
	- AWS manage underlying Infrastructure and foundation services
2. Abstracted Service
	- Service that require very little management from the customer (S3)
	- AWS operate infrastructure layer, os, platforms and serverside encryption and data protection

**Customer Responsibility Classification**

1. Infrastructure Services
	- Customer control os, application platform, encryption, protection, managing customer data
2. Abstracted Services
	- Responsible for customer data, encryption, protection using network firewalls and backups

# Root User

**Credentials**
1. Email & Password
	- Access AWS Management Console
2. Access Keys
	- **Access Key ID**
	- **Secret Access Key**
	- These access key allows you to run programmatic requests from CLI or API (SDK)
	- Do not create access key for the root user (unless you really have to)

**Root user best practice**
- strong password
- MFA


# IAM

**Authentication**
- Authentication ensure user is who they say they are
	- correct email/password

**Authorization**
- giving permission to access AWS resources and services

- IAM is global and not specific to any one region

**IAM user**
- represent person or service that interact with AWS
- you may give access permissions
	- AWS management console
	- programmatic access to CLI or API

**IAM group**
- collection of users
- all users in this "group" inherit the permissions assigned to the group
- using "group" is the best practice

**IAM Policies**
- manage access and provide permissions to AWS services
	- IAM policies attached to IAM identity
```
{
"Version": "2012-10-17",
"Statement": [{
"Effect": "Allow",
"Action": "*",
"Resource": "*"
}]
}
```
**Version**
- Define the version of the policy language
**Effect**
- specify "deny" or "access"
**Action**
- type of action that should be allowed or denied
**Resource**
- specify object or its policy that statement covers



# Servers

- Building block that you need to host an application
- handle HTTP requests and send response to clients via client-server model

**Client**
- Person or computer that send request

**Server**
- a computer or collection of computers connected to the internet serving websites to internet users
- OS
	- windows
		- internet information services
	- Linux
		- apache http server, nginx, apache tomcat


**Compute Options**
1. Virtual Machines (EC2)
	- emulate a physical server and allow installing http server to run applications
	- Require **Hypervisor** on the host machine
		- Allow sharing of hardware resources across one or more virtual machines
2. Containers (ECS - elastic container service)
	- fully managed container orchestration service that allow you to run and scale containerized applications using Docker containers
3. Serverless (Lambda)


# EC2

web service that provide resizable compute capacity in the cloud

**Define EC2 instance**
- **Hardware specifications**
	- CPU, memory, network, storage
- **Logical config**
	- Networking location
	- firewall rules
	- authentication
	- OS
		- Can be selected via selecting AMI (Amazon machine image)

**AMI - EC2**
1. Launch new instance
2. AWS allocates VM that runs on hypervisor
3. AMI is copied to the root device volume that contains image used to boot volume

![[Pasted image 20250501213145.png]]

**c** - Instance family : compute optimized family
**5** - generation of the instance : fifth generation
**n** - additional attributes : local NVMe storage
.
**xlarge** - instance size

**Instance Families**
- General Purpose - g
- Compute Optimized - c
	- batch processing
- Memory Optimized - m
	- high performing db
- Accelerated Computing - a
	- Machine learning
- Storage Optimized - s
	- no-sql databases
- HPC Optimized - h?
	- Deep learning

**Default VPC**
- if not specified, EC2 are placed in default VPC
- everything in default VPC will be public and accessible by internet

**Architecting for high availability**
- consider using at least two EC2 in two separate Availability Zones
	- ex) frontend - one down, entire service fails

### EC2 States

**Rebooting**
- keeps public DNS name (IPv4) and private & public IPv4 addresses
- Ipv6 address remains same on the host computer

**Stopped -> Start**
- Can start a stopped instance if it has EBS volume as its root device
- when stopped instance is started, it is placed in a new physical server
- retain Ipv4 address & Ipv6

**Terminate**
- you lose both public and private IP of the machine

**Pricing**
- pricing for instance stop when 
	- SHUTTING DOWN
	- TERMINATED
	- Stopped?
		- Data transfer price not charged
		- EBS is still being charged
		- RAM memory is lost

**Stop-hibernate**
- saves RAM memory to EBS root volume, and its saved

# Price Optimization for EC2

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


# Container Services

**Containers**
- Standardized unit that package your code and its dependencies
- designed to run reliably on any platform because it creates its own environement

![[Pasted image 20250501221213.png]]
**VM vs Containers**
- Containers share same OS and kernel as the host they exist on
- Virtual machines contain their own OS

- Each VM must maintain copy of OS
	- **Wasted resources**

- **Container**
	- More lightweight
	- can provide speed

**Orchestrating Containers**
- What to consider when managing containers
	- how to place your containers on ec2
	- plan for failure - container
	- plan for failure - instance
	- monitor container deployment

**Orchestration Service**
[AWS ECS]
- end to end container orchestration service
- 구조
	- **Cluster**
		- Logical grouping of services, tasks, capacity providers in a region
	- **Service**
		- one or more identical tasks
		- check and replace unhealthy tasks
	- **Task**
		- one or more containers
		- specify compute, networking, IAM, config
	- **Computer**
		- Use EC2, EC2 Spot, Fargate, Fargate Spot

- can use them by installing **ECS container agent** on EC2 instances

- **Actions**
	- Launching and stopping containers
	- cluster state
	- scale
	- scheduling
	- assigning permission
	- meeting availability requirement

[AWS EKS]
- Kubernetes are more portable, extensible and open-source than ECS


**ECS vs EKS**
- ECS
	- container is called "task"
	- runs on AWS native technology
- EKS
	- container is called "pod"
	- runs on Kubernetes


# Serverless

**Serverless Pros**
- no servers to provision or manage
- scales with usage
- never pay for idle resources
- availability and fault tolerance are built in

[AWS Fargate]
- Fargate abstracts EC2 instance
- May use all the same ECS concepts, API and AWS integration
- natively integrates with IAM and VPC
	- can launch inside your network and control connectivity
- eliminates need to choose and manage EC2 instances, cluster capacity and scaling
- supports both ECS and EKS architecture

**if one wants to migrate their pipeline onto aws by minimizing refactoring, one should just ec2**

# Networking

- how you connect computers around the world and allow them to communicate with one another

**Example - sending a letter**
1. Letter (inside the envelope)
2. address of the sender - from
3. address of the recipient - to

**Routing**
- handling these "message" delivery

**IP address**
- 32bit
- 10101000

**IPv4**
- 32bits are grouped into 8 bits
	- each of the group separated by a period

![[Pasted image 20250503174130.png]]

- Used when trying to communicate to a single computer

**CIDR notation**
- compressed way of representing a range of IP addresses

192.168.1.0/24
- number at the end (24) specify how many of the bits of the IP address is being fixed

![[Pasted image 20250503174500.png]]
- first 3 (8 bits) bits 8 x 3 = 24 are fixed
- rest (last 8 bits) are flexible
- 256 ip address in that IP range

**you choose the network size by using CIDR notation**
- smallest range you can have is /28
	- 16 Ip addresses
- largest you can have is /16
	- 65,536 IP addresses

# VPC
- isolated network that you create in your AWS cloud

**configuration**
1. Name of the VPC
2. Region of the VPC
	- VPC spans all the availability zones within the selected region
3. IP range for the VPC in CDIR notation
	- determine size of your network
	- each VPC may have upto 5 CIDRs (1 primary, 4 secondaries)

![[Pasted image 20250503175037.png]]


# Subnet
- you **must** create subnets within vpc 
- they act as "smaller networks" inside your base network or virtual local area networks (VLANs)
- **used for isolating or optimizing network traffic**
- use public subnet for resources that need internet connection
- use private subnet for resources that won't be connected to the internet


**Configuration**
- **VPC** that you want your subnet to live in
- **AZ** that you want your subnet to live in
- **IPv4 CIDR** block for your subnet

**High availability**
- to maintain redundancy and fault tolerance, create at least two subnets configured in two availability zones

**Reserved IPs**
- aws reserves five ip addresses in each subnet
	- Network address
	- VPC local router
	- DNS server
	- Future use
	- Network broadcast address

- common practice
	- VPC : /16
	- subnet : /24

# Gateways
**Internet Gateway**
- internet gateway is a **VPC component** that permits communication between your VPC and internet
- highly available and scalable

**Virtual Private Gateway**
- virtual private gateway is VPN endpoint on the amazon side of your VPN connection that can be attached to single VPC
- connects VPC to another private network
- on the other side of the connection, you need to connect a customer gateway to the other private network

**AWS Direct Connect**
- physical connection between on-premis data center and VPC
- internal network is linked to an AWS Direct Connect location


# VPC Routing
- when VPC is created, "main route table" is created
	- contains set of rules called **routes** that are use to determine where network traffic is directed
- AWS assumes that when you create VPC with subnets, you want traffic flow in between.

- main route table is used implicitly by subnets that does not have any explicit route table association (default)

**main route table rules**
- cannot delete main route table
- cannot set a gateway route table as the main route table
- can replace the main route table with custom subnet route table
- can add remove and modify routes in the main route table
- you can explicitly associate a subnet with the main route table even if it's already implicitly associated

**Custom route tables**
- will have the **local route** already inside it upon creation
	- allow communication to flow between all resources and subnets inside VPC
- you can protect VPC by explicitly associating each new subnet with a custom route table and leaving the main route table in its original default state

**whether a subnet is public or private, depends on its associated "route table"**
- if a route from internet gateway to the subnet exist, it is public (internet access)

# nACL (network ACL)
- virtual firewall at the **subnet level**
- determines what kind of traffic is allowed to enter or leave your subnet
- **Stateless**
	- does not retain any information between requests
	- have to specify both inbound and outbound ports
		- just because it came in, does not mean it gets to leave
- all allowed by default, and can use both allow or deny rules

# Security Group
- virtual firewall for EC2 instances
- not optional
- by default, group only allows outbound traffic
	- need rule that allow ports 80, 443
- **stateful**
	- remember a connection is requested internally? or from internet
- by default, blocks all inbound traffic and allows all outbound traffic


# Storage

**File Storage Usage**
- Web serving
- Analytics
- Media and entertainment
- Home directories

**Block Storage**
- splits files into fixed size chunks of data called blocks and have own addresses
- pro : if you want to change one character in a 1-gb file with block storage, you only have to make edit to one block
- **optimized for low latency operations**
	- high-performance workloads
	- transactional
	- mission-critical
	- I/O-intensive
- **Containers**
- **Virtual machines**
	- vm hypervisors

**Object Storage**
- single distinct unit of data
- **Data Archiving**
- **Backup and recovery**
- **Rich media**


**Amazon Elastic File System** [Amazon EFS]
- automatically grows and shrinks as you add and remove files
- can be used with computer services and on-premise resources

- **Classes**
	- efs standard
	- efs standard-infrequent access
	- efs one zone-infrequent access


**Amazon FSx**
- file managing service/system
- NetApp
- OpenZFS
- Windows File Server
- Lustre

**EC2 Instance Store**
- temporary block-level storage of an instance
- storage is located on disks that are physically attached to the host computer
- **Ephemeral Storage**
	- if you delete the instance, it is also deleted
- ideal if you host applications that replicate data to other EC2 instances such as Hadoop clusters

**EBS**
- block level storage attached to EC2 instance
- **Detachable**
	- can detach it and attach it to another ec2
- **Distinct**
	- if an accident happens to the computer, it does not go down with it
- **Size-limited**
	- it has a fixed limit of size
- **1-to-1 connection**
- Scaling
	- Increase volume size
	- Attach multiple volumes

- **Use Case**
	- when you need to retrieve data quickly and have data persist long time
	- **OS**
		- boot and root volumes (AMI)
	- **Databases**
		- Database running on EC2
	- **Enterprise Application**
		- high availability, high durability block storage
	- **Big data analytics engines**

**EBS Volume types**
- SSD
- HDD

**EBS snapshots**
- incremental back ups that only save blocks on the volume that has been changed after most recent snapshot
- stored redundantly in multiple AZ using S3
- can be used to create new volumes


# S3
- flat structure with no hierarchy of subbuckers or subfolders
**Object Key Names**
- unique identifiers inside the bucket
	- e.g. amazons3.html
- prefix acts as a folder structure
- htdtp://testbucket.s3.amazonaws.com/**2022-03-01**/amazons3.html

**Use Case**
- backup, storage
- media hosting
- software delivery
- data lakes
- static websites
- static content


**bucket openness**
- public bucket
- private bucket (default)
- controlled access (access policy)


**S3 bucket policies**
1. IAM policies
	- **Can be attached to resources & users**
	- if you want all policies to be centralized
	- if you have different permission requirements for each buckets

2. bucket policies
	- defined in JSON format
	- can only be attached to S3 buckets
	- when you need **cross-account access** to s3 without IAM roles
	- when IAM policies is over size limit

**S3 encryption**
- automatically encrypts all object on upload and apply server-side encryption with s3-managed keys


| **S3 Standard**                                    | general purpose                                                                                                                                                              |
| -------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **S3 Intelligent-Tiering**                         | This tier is useful if your data has unknown or changing access patters. automatically moves your data to the most cost-effective storage tier based on frequency of access. |
| **S3 Standard-Infrequent Access (S3 Standard-IA)** | This tier is for data that is accessed less frequently but requires rapid access when needed.                                                                                |
| **S3 One Zone-Infrequent Access (S3 One Zone-IA)** | S3 One Zone-IA stores data in a single Availability Zone                                                                                                                     |
| **S3 Glacier Instant Retrieval**                   | Use S3 Glacier Instant Retrieval for archiving data that is rarely accessed and requires millisecond retrieval.                                                              |
| **S3 Glacier Flexible Retrieval**                  | S3 Glacier Flexible Retrieval offers low-cost storage for archived data that is accessed 1–2 times per year                                                                  |
| **S3 Glacier Deep Archive**                        | S3 Glacier Deep Archive is the lowest-cost Amazon S3 storage class. default retrieval time of 12 hours.                                                                      |
| **S3 on Outposts**                                 | Amazon S3 on Outposts delivers object storage to your on-premises AWS Outposts environment using S3 API's and features.                                                      |


**AWS S3 versioning**
- versioning keeps multiple version of a single object in the same bucket
- preserves old versions of the object without using different names, helping object recovery
- unique version ID is automatically generated for the object

**Version Types**
1. **Unversioned (default)**
	- no version control
2. Versioning-enabled
	- enabled for all object in the bucket
3. Versioning-suspended
	- suspended for new objects while keeping 

**Transition actions between storage tiers**
- automate process by configuration
	- **Transition Actions**
		- define when objects should transition to another storage
	- **Expiration Actions**
		- define when objects expire and should be permanently deleted

![[Pasted image 20250506171226.png]]


**lifecycle configuration tools**
- Periodic logs
	- upload periodic logs to a bucket, you might need them for a week/month and delete them
- Data that changes in access frequency
	- documents are frequently accessed for a limited period of time
	- after that, they are infrequently accessed


# Etc Storage

**EC2 instance store**
- ephemeral block storage attached to EC2
- best for buffers, caches, scratch data
- not meant for persistent or long lasting

**EBS**
- data that changes frequently and must persist through instance stops, terminations, or hardware failures
- SSD
	- depend on IOP and is ideal for transactional workloads such as databases and boot volumes
- HDD
	- depend on megabytes per second and is ideal for throughput-intensive workloads

- block storage
- you pay for what you provision
- replicated across multiple servers in single availability zone

**Amazon S3**
-  if your data does not change often, most cost-effective and scalable storage solution


**Amazon EFS**
- provide highly optimized file storage for a broad range of workloads and applications
- cloud native shared file system with fully automated lifecycle management
- ideal for workload that require high level of durability and availability

**AWS FSx**
- native compatibility with third-party file systems
- automate time consuming administration task such as
	- hardware provisioning
	- software configuration
	- patching
	- backups



**RDBMS**
- benefits
	- complex SQL
	- reduced redundancy
	- familiarity
	- accuracy

- use cases
	- application with a fixed schema
		- can lift from on-premise, onto cloud with no modification
	- application that need persistent storage (ACID principle)
		- Enterprise resource planning (ERP) applications
		- CRM applications
		- commerce and financial applications

**Unmanaged Databases**
- on-premise database
	- data center security
	- electricity
	- host machines management
	- database management
	- query optimization
	- customer data management
- EC2 hosted database
	- still responsible for managing EC2 instance
	- and all aspect of the database

![[Pasted image 20250511170436.png]]

**Managed Databases**
- provide setup of both EC2 instance and database
- provide systems for
	- high availability
	- scalability
	- patching
	- backups

- "you are" responsible for
	- database tuning
	- query optimization
	- customer data security

****

**RDS**
- managed relational database service
- **DB types**
	- Commercial
		- Oracle, MS SQL
	- Open Source
		- MySQL, Postgresql, MariaDB
	- Cloud Native
		- Aurora

**Database Instances**
- RDS is built from computer and storage
- **Compute**
	- DB instance
		- underneath db instance, there is an EC2 instance which is managed via RDS console instead of EC2 console
- **Storage**
	- EBS volumes for database and log storage
	- MySQL, MariaDB, PostgreSQL, Oracle, MS SQL
	- General Purpose SSD, Provisioned IOPS SSD, Magnetic

**RDS in VPC**
- DB Subnet group
	- the VPC your database will live in 
	- subnet should be private so they don't have a route to the internet gateway
		- ensure db instance to only be reached by the application backend
- Access to DB instance can be restricted further via nACL and security groups

**Backup data**
- Automated backups
	- retained between 0 and 35 days
- Point-in-time recovery
	- creates a new DB instance using data restored from a specific point in time

- Manual Snapshots
	- if you want to keep automated backup for more than 35 days, use manual snapshots

**RDS Multi-AZ deployment**
- creates redundant copy of your database in another AZ
	- each in different subnet
- primary copy
	- provide access to your data so application can query and display the information
	- synchronously replicated to standby copy
- secondary copy
	- improve availability
	- automatic failover

**RDS security**
- you have control over managing access to your RDS resources
- nACL & Security groups help users dictate flow of traffic
- IAM
	- policies to assign permissions
- Security Groups
	- control IP addresses or EC2 instances
- RDS Encryption
	- secure your DB instances and snapshots
- SSL/TLS
	- Secure sockets layer or transport layer security connections

**Dynamo DB**
- NoSQL database

**ElastiCache**
- Fully managed, in-memory caching solution
	- Redis
	- Memecached

**Memory DB for Redis**
- redis compatible, durable, in-memory database
- achieve microsecond read latency
- fully managed, primary database to build high-performance applications
	- do not need separate cache, or db

**Document DB**
- fully managed document database
- type of no-sql database you can use to store and query rich documents

**Amazon Keyspaces**
- scalable, highly available and managed Apache Cassandra

**Amazon Neptune**
- graph database

**Amazon Timestream**
- serverless time series database for IoT and operational applications

**Amazon Quantum ledger database**
- purpose-built ledger database that provide complete and cryptographically verifiable history of all changes made to your application data


# DynamoDB

- fully managed NoSQL database that provide fast and predictable performance with seamless scalability
- can create database tables that can store and retrieve any amount of data and serve any level of request traffic
- scale up or down your tables throughput capacity without downtime or performance degradation
- monitor resource usage and performance metrics using AWS management console

**Core Components**
1. Tables
	1. Collection of items
2. Items
	1. Collection of attributes
3. Attributes
	- key-value pair (cannot be broken down further)

**use cases**
- you are experiencing scalability problems with traditional db
- actively engaged in developing application or service
- working with OLTP workload
- deploying a mission-critical application that must be highly available at all times without manual intervention
- require high level of data durability, regardless of your backup


**Security**
- data is redundantly stored on multiple devices across multiple facilities in DynamoDB region
- all user data is stored is fully encrypted at rest
- IAM admin control who can be authenticated and authorized to use DynamoDB resources

**AWS CloudTrail**
- can tell who made the request, services used, actions performed, ...

**IAM roles for authentication**
- users or applications must include valid AWS credentials to fetch AWS API requests

**IAM policy conditions for fine-grained access control**
- implement least privilege is key in reducing security risk


**DB services**
1. RDS, Aurora, Redshift
	1. Relational
	2. Traditional application, ERP, CRM, ecommerce
2. DynamoDB
	1. Key-value
	2. high traffic web applications, ecommerce, gaming
3. ElastiCache
	1. In-Memory
	2. Caching, session management, gaming leaderboards, geospacial applications
4. DocumentDB
	1. Document
	2. Content manangement
5. Keysapces
	1. Wide column
	2. High-scale industrial applications for equipment maintenance
6. Neptune
	1. Graph
	2. Fraud detection, social networking, recommendation engines
7. Timestream
	1. Time series
	2. IoT applications, DevOps
8. QLDB
	1. Ledger
	2. systems of record, supply chain, registrations, banking transactions

# Monitoring

- monitoring provide near real-time pulse on your system and help answer the previous questions
- act of collecting, analyzing and using data to make decision or answer questions about IT resources

**Metrics**
- **S3**
	- Size of objects stored in a bucket
	- Number of objects stored in a bucket
	- Number of HTTP request made to a bucket
- **Amazon RDS**
	- Database connections
	- CPU utilization of an instance
	- Disk space consumption
- **EC2**
	- CPU utilization
	- Network utilization
	- Disk performance
	- Status checks

**Benefit of Monitoring**
- Respond proactively
	- respond to operational issues proactively before your end users are aware of them
- Improve performance and reliability
	- Monitoring can improve the performance and reliability of your resources
- Recognize security threats and events
	- Monitoring can recognize security threats and events
- Make data-driven decisions
- Create cost-effective solutions

**Cloud Watch**
- monitoring and observability service that collects your resource data and provides actionable insights into your applications

**Example usage**
- detect anomalous behavior in your environments
- set alarms to alert you when something is not right
- visualize log and metrics with the AWS management console
- take automated actions like scaling
- troubleshoot issues
- discover insights to keep your applications healthy






