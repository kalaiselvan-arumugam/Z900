Distributed Spring Boot Dispatcher POC - Software Requirements Specification (SRS)
> This document is the **single source of truth** for the project. AI
> coding agents must follow this specification exactly. Do not replace
> technologies, alter the architecture, or simplify the design unless
> explicitly stated.
---
1. Project Goal
Build a production-quality Proof of Concept demonstrating a distributed
Spring Boot architecture that coordinates multiple application instances
while enforcing a global external API limit of 15 requests per clock
second.
The solution must demonstrate:
Distributed leader election
Shared database queue
Global rate limiting
Priority scheduling
Automatic failover
Chaos engineering
Real-time dashboards
Professional project structure
---
2. Applications
Project 1 - ClientSimulator
Purpose:
Simulate multiple clients.
Randomly route requests to Dispatcher instances (simulate load
balancer).
Generate configurable load.
Display statistics.
Port:
8080
---
Project 2 - DispatcherApp
A single codebase.
Run two instances.
Instance 1
application-dispatcher1.properties
Port 8081
Instance 2
application-dispatcher2.properties
Port 8082
Both instances:
Use identical code.
Share the same H2 FILE database.
Participate in leader election.
---
Project 3 - ExternalApiSimulator
Port:
9090
Simulates the external vendor API.
---
3. Mandatory Technology
Java 21
Spring Boot 3.x
Maven
Spring Web
Spring Data JPA
Spring Scheduling
Spring Boot Actuator
Thymeleaf
Bootstrap 5
Vanilla JavaScript
Apache Log4j2
H2 Database (FILE mode)
Do NOT use:
Gradle
Redis
Kafka
RabbitMQ
Hazelcast
Infinispan
Docker
Kubernetes
---
4. Project Structure
Three independent Maven projects.
ClientSimulator/
DispatcherApp/
ExternalApiSimulator/
Every project must contain:
pom.xml
README.md
src/main/java
src/main/resources
src/test/java
DispatcherApp must include:
application-dispatcher1.properties
application-dispatcher2.properties
log4j2.xml
---
5. Configuration
Use application.properties only.
Do not use YAML.
All configurable values must be externalized.
Examples:
server.port
datasource URL
dispatcher instance name
heartbeat interval
heartbeat timeout
scheduler delay
rate limit
external API URL
retry count
logging level
---
6. Architecture
ClientSimulator
↓
Random Dispatcher
↓
Shared Database Queue
↓
Leader Dispatcher
↓
External API
Only the elected leader may invoke the external API.
Followers only enqueue work.
---
7. Functional Requirements
API1
POST /api/instant
Queue request.
Wait up to 5 seconds.
Return response if completed.
Otherwise return HTTP 202 + trackingId.
API2
POST /api/bulk
Accept JSON array (100+).
Queue each record.
Return HTTP 202 immediately.
Webhook is out of scope.
---
8. Scheduling
Priority
API1 = High
API2 = Low
Weighted scheduling.
Default
API1 = 12
API2 = 3
API2 must never starve.
---
9. Global Rate Limiter
Maximum
15 requests every clock second.
The Dispatcher Leader must enforce this globally.
Followers never bypass the dispatcher.
---
10. Shared Database
Use H2 FILE mode.
Database responsibilities:
Leader election
Heartbeat
Queue
Rate limiter state
Request persistence
---
11. Dashboards
Provide dashboards for:
Client Simulator
Dispatcher
External API
Chaos Panel
Refresh every second.
Display:
Queue size
Leader
Heartbeat
TPS
Latency
Success
Failures
Retries
Recent events
---
12. Chaos Engineering
Provide controls for:
Stop/resume heartbeat
Pause/resume dispatcher
Force leader/follower
Database delay
External 429
External 500
External timeout
Variable TPS
Load generator
Reset all
---
13. Logging
Use Apache Log4j2.
Rolling logs.
Separate log files for:
Client
Dispatcher instance 1
Dispatcher instance 2
External API
Log business events and errors.
---
14. Coding Standards
Constructor injection
SOLID principles
Layered architecture
No field injection
No TODO placeholders
Production-quality code
Proper exception handling
Comprehensive logging
JavaDoc where appropriate
Packages:
controller
service
repository
entity
scheduler
dispatcher
dto
config
util
exception
---
15. Delivery Strategy
Generate projects in order:
ExternalApiSimulator
DispatcherApp
ClientSimulator
Compile and verify each project before continuing.
---
16. Acceptance Criteria
The completed solution must satisfy:
Global 15 TPS never exceeded.
Automatic leader failover.
Queue survives restart.
No request loss.
Priority scheduling works.
API2 never starves.
Dashboards function.
Chaos scenarios pass.
No compilation errors.
Single Dispatcher codebase with two property files.
