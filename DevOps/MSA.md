**Monolith**
- original architecture
- all software compinent is executed in a single process
- strong coupling between classes
- does not expose any data or functionality to any other apps (**Silo**)

**SOA (Service Oriented Architecture)**
- emphasis on sharing and giving
- app is build in a structure that is shares its functionalities via api
- communicate by consuming vi
	- SOAP
	- WSDL


---

2014 - martain fowler & james lewis
"Microservices" article
- Best description of microservices


**Characteristic of microservices**
- Componentization via services
- Organized around business capabilities
- Products not projects
- Smart endpoints and dumb pipes
- Decentralized governance
- Decentralized data management
- Infrastructure automation
- Design for failure
- Evolutionary design

---

**Componentization via Services**
- **modular design**
	- updating requires a small code change
	- can be achieved by using
		- **libraries (not prefered)**
			- called directly within the process
			- share same resources
			- does not require any mediators
			- help with the inner workings of the service (not for modular design)
		- **Services (prefered)**
			- services that are out of process mechenism (Web API, RPC)
			- allows components independently deployable
				- if we need to modify a single component, we depoloy this single component
			- forces us to define components interface very well

- **Components**
	- parts that together compose the software
	- every component is a service
		- runs in its own process and accessible


---

**Business Capabilities**

**Traditional setting (Monolith)**
	- Team Interface
	- Team API
	- Team Logic
	- Team Database
- inter-group communication is slow and cumbersome and may have different goals

**microservice architecture**
- Interface, API, logic, database all handled by same team
- allows for holistic view of the service

**Defining Boundary of a service (??)**
- look at overall functionality of the system and map the various business capabilities it has


---

**Products, not projects**

**Traditional**
- goal : deliver working code
- customer involvement in development process is minimal

**Microservice**
- goal : deliver a working product, not a perfect code
	- product require ongoing support and close relationship with customer
	- responsible even after the delivery of product
	- "you build it, you run it" - Werner Vogels, AWS CTO
	- Agile Manifesto
		- increase customer's involvement in development process

---

**smart endpoints, dumb pipes**

**Traditional**
- **ESB (Enterprise Service Bus)**
	- mediate between the services in source system
	- role
		- mediate between services in source system
		- help with routing authorization
		- validation
		- aggregation
- **WS Star Protocol**
	- extension of the original SOA protocol
	- first web API
	- WS discovery
		- locating services in network
	- WS security
		- security & reliable messaging
- complicatae interservice communication and difficult to maintain

**Microservice**
- Dumb Pipes (Simple protocols)
	- HTTP Protocol
		- what is already there
	- Rest API
		- simplest API in existance

**direct connection between services**
- create coupling between services
- if one service change its location, all the other must be updated
- Solution
	- **Discovery Service**
	- **Gateway**

**API for data (microservices)**
- GraphQL
- gRPC






















