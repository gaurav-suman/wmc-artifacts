# FashionHub Enterprise Commerce Platform

## 1. Introduction

FashionHub is an enterprise-level e-commerce platform built for women's fashion retail. The application supports product discovery, cart management, checkout, payment, inventory reservation, shipment [...]
I worked on this project as part of the backend engineering team, mainly around Java, Spring Boot, microservices, REST API development, service integrations, Redis caching, security, observability, de[...] 

### 2. Business Context
  
FashionHub handles high-volume customer journeys, especially during sale events such as Diwali Sale, flash campaigns, festive offers, and seasonal launches.  
The main business needs are:
- Customers should be able to search and view products quickly.
- Cart updates should be fast and reliable.
- Checkout should avoid duplicate orders and duplicate payments.
- Inventory should be reserved correctly during order placement.
- Payment and shipment integrations should be traceable and fault tolerant.
- Customers should receive timely notifications.
- Support teams should be able to investigate issues using logs, dashboards, audit records, issue trackers, and RCA documents.

### 3. Technology Stack

| Area | Technology / Tool |
|---|---|
| Language | Java |
| Framework | Spring Boot |
| API | REST, OpenAPI |
| Security | Spring Security, JWT, OAuth2 concepts, IAM |
| Database | AWS RDS PostgreSQL |
| Cache | Redis / AWS ElastiCache |
| Messaging | Kafka, AWS SQS, AWS SNS |
| Cloud | AWS ECS Fargate, S3, CloudWatch |
| DevOps | Jenkins, Docker, CI/CD pipeline |
| Observability | CloudWatch Logs, metrics, dashboards, correlation ID, distributed tracing concepts |
| Testing | Unit testing, integration testing, API contract validation, smoke testing |
| Documentation | README, API notes, issue tracker, RCA, runbook, deployment checklist |


### 4. High-Level Architecture
  
FashionHub follows a microservices-based design where each service owns a clear business capability. The services communicate using REST for immediate operations and messaging for asynchronous backgro[...]
The platform has four major layers:
- **Client and Gateway Layer**
- Web and mobile clients call APIs through the API Gateway.
- Gateway handles routing, authentication validation, rate limiting, request validation, and correlation ID propagation.
- **Business Service Layer**
- Product, Customer, Cart, Order, Inventory, Payment, Shipment, Notification, Review, and Audit services handle business workflows.
- **Platform and Data Layer**
- RDS PostgreSQL stores transactional data.
- Redis stores fast-changing or frequently accessed data such as product cache and active cart state.
- S3 stores product images, invoices, exports, and archived files.
- Kafka/SQS/SNS supports asynchronous processing.
- **Operations Layer**
- Logs, metrics, alerts, dashboards, runbooks, issue tracker, and RCA documents help maintain the system in production.

### 5. Architecture
  
In this microservice architecture, each service should own its data model. In the diagram, AWS RDS PostgreSQL a managed database platform, not as one common shared schema that all services directly ac[...]
A Saga Orchestrator is present as part of the Order Service. If order, inventory, payment, and shipment are truly separate service boundaries, saga-style coordination is required to manage eventual co[...]

### 6. Integrated Service Flow Diagram

To ensure the ASCII diagram renders correctly in preview, it is wrapped in a fenced code block below.

```text
+----------------------+                       
|  Web / Mobile Apps   |                       
+----------+-----------+                       
           |                                   
           v                                   
+----------------------+                       
|     API Gateway      |                       
| - JWT Validation     |                       
| - Rate Limiting      |                       
| - Correlation ID     |                       
+----------+-----------+                       
           |                                   
           v                                   
+------------------------------------------------------------+
|                    Core Business Services                  |
+------------------------------------------------------------+
| Product | Customer | Cart | Pricing | Order | Review      |
+------------------------------------------------------------+
           |
           v
+------------------------------------------------------------+
|                 Checkout Orchestration Flow                |
+------------------------------------------------------------+
| Order Service                                               |
|      |                                                      |
|      +--> Inventory Service (Reserve Stock)                |
|      +--> Payment Service (Authorize Payment)              |
|      +--> Shipment Service (Create Shipment)               |
+------------------------------------------------------------+
           |
           v
+------------------------------------------------------------+
|            Event & Messaging Layer                         |
+------------------------------------------------------------+
| Kafka Topics | SNS | SQS | DLQ                             |
+------------------------------------------------------------+
           |
           v
+------------------------------------------------------------+
|             Downstream Processing Services                 |
+------------------------------------------------------------+
| Notification | Audit | Reporting | Analytics              |
+------------------------------------------------------------+
           |
           v
+------------------------------------------------------------+
|                   Data & Infrastructure                    |
+------------------------------------------------------------+
| Redis Cache                                                 |
| RDS PostgreSQL                                              |
| S3 (Images, Invoices, Reports)                              |
+------------------------------------------------------------+
           |
           v
+------------------------------------------------------------+
|              Observability & Operations                    |
+------------------------------------------------------------+
| CloudWatch Logs | Metrics | Dashboards | Alerts | RCA      |
+------------------------------------------------------------+
```


### 7. Key Challenges and there solution
  
Some practical challenges in maintaining this application were:

- Choosing b/w Kafka and SQS:
   SQS was cheaper than Kafka interms of cost but in this project as events are produced and consumed by multiple services kafka was the better choice, for different service commiunication. Kafka also[...]

- Handling high traffic during sale events without overloading database and downstream service:
   1. Redis cache is introduced into search APIs which gives faster response.
   2. On normal days, ECS service was running with 1 task handling around 300 to 400 users/day. For Diwali sale, we configured target-tracking autoscaling to scale from 1 to 4 tasks based on CPU above[...]

- Keeping checkout reliable across order, inventory, payment, and shipment flows:
   Order Service as the checkout coordinator. It create the order in PENDING state, call Inventory for stock reservation, Payment, and Shipment after payment success. Failures were handled using statu[...]

- Inventory Overselling During Concurrent Checkout
    Multiple customers attempted to purchase the same product simultaneously during sale events, causing inventory inconsistencies. So implemented optimistic locking using a version column in Inventor[...]

- Preventing duplicate orders and duplicate payments during retry scenarios:
   Introduced an Idempotency-Key header in checkout APIs.The key was stored with customerId + cartId + requestHash in RDS with a unique constraint. If the same request came again, we returned the exis[...]

- Debugging duplicate event processing across services:
   Duplicate Kafka events were handled by storing processed_eventId key in a deduplication_table. Before processing any event, the consumer checked whether the event was already processed. This avoide[...]

- Debugging issues across multiple services using correlation ID and business identifiers:
   Propagated correlationId from API Gateway to all Spring Boot services using request filters and MDC logging. Logs included correlationId, orderId, cartId, and paymentReference, which helped trace t[...]

- Issue: Payment Callback Arriving Before Kafka Event Processing
    Implemented strict order status transitions (PENDING → PAYMENT_COMPLETED → INVENTORY_RESERVED → SHIPPED → CONFIRMED) to prevent invalid updates. Payment callbacks were made idempotent by c[...]
    Scheduled Every 6 hours reconciliation jobs fixed orders stuck due to callback or consumer failures.

- Kafka Partition tuning and Lost/Failed event handling
    Currently Order service has 3 partitions and all other services has 2-2 partitions each. DLT has been setup for any lost/failed event to recover.
    

### 8. Cost Calculation

| Component       | Configuration           | Monthly Cost (₹)    |
| --------------- | ----------------------- | ------------------- |
| ECS Fargate     | 1 task (0.5 vCPU, 1 GB) | ₹1,500              |
| RDS PostgreSQL  | db.t3.medium            | ₹3,500              |
| Redis           | cache.t3.micro          | ₹1,000              |
| Kafka (MSK)     | Small 2-broker cluster  | ₹5,000              |
| SNS             | Low traffic             | ₹500                |
| S3              | Images + invoices       | ₹300                |
| CloudWatch      | Logs + dashboards       | ₹1,000              |
| **Total**       |                         | **~₹12,800/month** |


