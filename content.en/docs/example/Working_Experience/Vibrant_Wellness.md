---
title: Vibrant Wellness
# weight: 2
# bookToc: false
---

#### Working Expeirence in Vibrant Wellness

### Eletronic Medical Record Integration System

- Worked on a Electronic Medical Record (EMR) system of Vibrant America as a Backend. In this
project we rebuild two modules of order and send test results from a legacy program.
- For Order part we need to fetch HL7 files from external SFTP server and stored in our storage. Then
we need to parse these HL7 files into an order request that will send to our Order Service.
- The Send Result file part is similar to Order part in opposite ways. We subscribe to a Kafka topic
and consume message to send Test Results into SFTP server in HL7 format.
- For Database Connection we switched from JDBC to Mybatis ORM.
- For Restful Client call we used RestTemple in Spring and we also build a modules for gRPC call to
our internal service. We set up some interceptions for services call for logging and error handling.
- Used Kubernetes to deploy recourse including Volume, Volume Claim, Deployment, Service, Ingress.
- Set up Java project into a Spring Boot project and utilized Spring Boot to set up Kafka consumers,
REST controller and Scheduled Job (CronJob)
- To improve performance we used Redis for cache and run tasks in parallel with the help of Java
CompletableFuture and implement distributed Lock using Redis to resolve conflicts.
- To build a fault tolerant and stable program we designed an easy-to-use mechanism to achieve rate
limit and retry mechanism.
- Deployed EMR project as a Microservice on Kubernetes cluster served on Azure
- Collected OpentTelemetry data and send to Jaeger to view distributed logs in Jaeger UI

### Customer Relationship Management
- Worked on a Customer Relationship Management (CRM) system of Vibrant America as a Full Stack
Developer. In this project we focused on rebuilding charts and tables for statistical results.
- The backend of CRM was implemented in Python and used SQLAlchemy to connect with databases.
- In the previous implementation we need lots of aggregation operation in DB. So we did some hourly
precalculation result of aggregation results and stored in new tables.
- The above approach drastically improved the performance and the responsive time is less than 200 ms.
To further improved the performance and the query flexibility, we push the results to ElasticSearch
and write query to fetch custom results from ES.
- Implemented kafka consumers and producers to push updates of Database to ElasticSearch

### Electronic Health Record System
- Worked on an Electronic Health Record (EHR) system of Vibrant America as a Full Stack Developer.
- Utilized web development technologies Vue.js, Html, Javascript, CSS to build fascinating web pages
that provide excellent user experiences. We used ElementUI as UI framework and customized global
css required by our designer.
- Builded back-end service in Typescript connected to MySQL Database with the help Prisma ORM
- Designing and developing GraphQL API with the help of Apollo GraphQL Server and NexusJs.
- Using Jenkins for consistent integration and consistent deployment (CICD) on a docker swarm
cluster and Git for version control
- Focused on the calendar features that provide service for CRUD of scheduled events. We supported
some advanced features like recurrent events and available event time suggestions.
- Involved in the architectural of EHR. Used Debezium change data capture CDC to reduplicate data
from other service like Settings Service.
- Connected with Google Service using Oauth 2.0 Authentication Code Mode.Automatically push calendar
events to Google Calendar after the user has successfully logged in and granted permissions to
update Google Calendar.



