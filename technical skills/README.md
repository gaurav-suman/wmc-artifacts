# FashionHub Enterprise Commerce Platform

## 1. Introduction

FashionHub is an enterprise-level e-commerce platform built for women's fashion retail. The application supports product discovery, cart management, checkout, payment, inventory reservation, shipment tracking, notifications, audit, and operational support.

I worked on this project as part of the backend engineering team, mainly around Java, Spring Boot, microservices, REST API development, service integrations, Redis caching, security, observability, deployment support, and production issue analysis.


## 2. Business Context

FashionHub handles high-volume customer journeys, especially during sale events such as Diwali Sale, flash campaigns, festive offers, and seasonal launches.

The main business needs are:

- Customers should be able to search and view products quickly.
- Cart updates should be fast and reliable.
- Checkout should avoid duplicate orders and duplicate payments.
- Inventory should be reserved correctly during order placement.
- Payment and shipment integrations should be traceable and fault tolerant.
- Customers should receive timely notifications.
- Support teams should be able to investigate issues using logs, dashboards, audit records, issue trackers, and RCA documents.

## 3. Technology Stack

| Area | Technology / Tool |
|---|---|
| Language | Java |
| Framework | Spring Boot |
| API | REST, OpenAPI |
| Security | Spring Security, JWT, OAuth2 concepts, IAM |
| Database | AWS RDS PostgreSQL |
| Cache | Redis / AWS ElastiCache |
| Messaging | Kafka, AWS SQS, AWS SNS |
| Cloud | AWS ECS Fargate, S3, CloudWatch, Secrets Manager |
| DevOps | Jenkins, Docker, CI/CD pipeline |
| Observability | CloudWatch Logs, metrics, dashboards, correlation ID, distributed tracing concepts |
| Testing | Unit testing, integration testing, API contract validation, smoke testing |
| Documentation | README, API notes, issue tracker, RCA, runbook, deployment checklist |

## 4. High-Level Architecture

FashionHub follows a microservices-based design where each service owns a clear business capability. The services communicate using REST for immediate operations and messaging for asynchronous background processing.

The platform has four major layers:

1. **Client and Gateway Layer**
   - Web and mobile clients call APIs through the API Gateway.
   - Gateway handles routing, authentication validation, rate limiting, request validation, and correlation ID propagation.

2. **Business Service Layer**
   - Product, Customer, Cart, Order, Inventory, Payment, Shipment, Notification, Review, and Audit services handle business workflows.

3. **Platform and Data Layer**
   - RDS PostgreSQL stores transactional data.
   - Redis stores fast-changing or frequently accessed data such as product cache and active cart state.
   - S3 stores product images, invoices, exports, and archived files.
   - Kafka/SQS/SNS supports asynchronous processing.

4. **Operations Layer**
   - Logs, metrics, alerts, dashboards, runbooks, issue tracker, and RCA documents help maintain the system in production.

## 5. Architecture 

In this microservice architecture, each service should own its data model. In the diagram, AWS RDS PostgreSQL a managed database platform, not as one common shared schema that all services directly access.

For example:

- Product Service owns product and catalog data.
- Cart Service owns active cart state and cart-related persistence.
- Order Service owns order lifecycle data.
- Payment Service owns payment transaction data.
- Inventory Service owns stock and reservation data.
- Audit Service owns audit trail and support reference data.

A Saga Orchestrator is present as part of the Order Service. If order, inventory, payment, and shipment are truly separate service boundaries, saga-style coordination is required to manage eventual consistency and compensation.

## 6. Integrated Service Flow Diagram

The diagram below shows one complete customer journey from product browsing to order confirmation. 

```text
Customer
   |
   v
Web / Mobile App
   |
   v
API Gateway
   |
   |-- Validate request
   |-- Validate token for protected APIs
   |-- Add correlation ID
   |-- Route request to target service
   |
   v
+-------------------+        +-------------------+        +-------------------+
| Product Service   |<------>| Redis Cache       |<------>| RDS PostgreSQL    |
| Product details   |        | Product/category  |        | Product catalog   |
| Search/listing    |        | Frequently read   |        | Persistent data   |
+---------+---------+        +-------------------+        +-------------------+
          |
          v
+-------------------+        +-------------------+        +-------------------+
| Cart Service      |<------>| Redis Cache       |<------>| Pricing Service   |
| Add/update item   |        | Active cart       |        | Price/offer check |
| Cart summary      |        | Cart summary      |        | Discount rules    |
+---------+---------+        +-------------------+        +-------------------+
          |
          v
+-------------------+
| Order Service     |
| Checkout request  |
| Idempotency check |
| Order lifecycle   |
+---------+---------+
          |
          | REST / Async message depending on operation
          v
+-------------------+        +-------------------+        +-------------------+
| Inventory Service |------->| Payment Service   |------->| Shipment Service  |
| Reserve stock     |        | Authorize payment |        | Create shipment   |
| Release stock     |        | Capture/refund    |        | Tracking details  |
+---------+---------+        +---------+---------+        +---------+---------+
          |                            |                            |
          |                            |                            |
          v                            v                            v
+--------------------------------------------------------------------------+
| Messaging Layer: Kafka / SQS / SNS                                       |
| Used for async processing, retryable flows, notifications, audit events,  |
| delay handling, and decoupling heavy background work                     |
+-------------------------------+------------------------------------------+
                                |
                                v
+-------------------+        +-------------------+        +-------------------+
| Notification      |        | Audit Service     |        | CloudWatch /      |
| Service           |        | Audit trail       |        | Dashboards        |
| Email/SMS/Push    |        | Support lookup    |        | Logs/metrics      |
+-------------------+        +-------------------+        +-------------------+
```

## 7. Main Data Flow

### Product and Catalog Data

Product details and category information are read frequently, especially during sale events. To reduce repeated database calls, Redis is used for frequently accessed product and category data. If data is found in Redis, the response is returned quickly. If not, Product Service reads from RDS and refreshes the cache with a proper TTL.

### Cart Data

Cart is a high-frequency flow because customers add, remove, and update items multiple times before checkout. Redis is used for active cart state and fast cart summary calculation. Important cart state can be persisted where required so the checkout flow remains reliable.

### Order Data

Order Service maintains the order lifecycle. It validates the cart, checks idempotency, creates the order, coordinates with inventory and payment, and updates order status. The order flow must be carefully logged because it is the main source of customer-impacting issues.

### Payment Data

Payment Service integrates with payment providers. It handles authorization, failure response, retry-safe operations, and payment status tracking. Payment reference, order ID, and correlation ID are important support fields during issue investigation.

### Inventory Data

Inventory Service manages available stock, reserved stock, and stock release. During checkout, inventory reservation must be reliable. If payment fails, reserved stock should be released so that inventory does not remain blocked incorrectly.

### Shipment Data

Shipment Service integrates with courier partners. Shipment creation, tracking number generation, delivery updates, and partner response handling are maintained separately from core order logic.

### Notification Data

Notification Service sends email, SMS, and push notifications. This flow is asynchronous so customer checkout is not blocked due to notification provider issues. Retry and DLQ handling are important for failed notifications.

### Audit and Support Data

Audit Service stores key business events and state changes. This helps support teams check what happened to an order, payment, shipment, or notification without depending only on scattered logs.

## 8. Cache Usage

Redis is used mainly to reduce latency and database load for read-heavy and frequently changing flows.

Key cache usages:

- Product details cache
- Category and filter cache
- Active cart state
- Cart summary
- Frequently accessed order tracking view where applicable
- Temporary lookup data used during checkout

Important cache practices:

- Use clear cache keys based on business identifiers.
- Apply TTL to avoid stale data.
- Log cache hit, cache miss, and DB fallback cases.
- Do not cache sensitive data unnecessarily.
- Use cache only where it improves performance without breaking business correctness.

## 9. Security Design

Security is applied from the entry point of the request and continues at service level. Authentication, authorization, secrets management, IAM access, and safe logging are part of the design.

```text
Customer / Admin User
   |
   v
Login Request
   |
   v
Identity Service
   |
   |-- Validate credentials
   |-- Generate JWT token
   |-- Return token with role/scope information
   |
   v
Client calls protected API with JWT
   |
   v
API Gateway / Security Filter
   |
   |-- Validate token
   |-- Check expiry
   |-- Check role/scope
   |-- Apply rate limit
   |-- Add correlation ID
   |
   v
Target Microservice
   |
   |-- Apply method/API level authorization
   |-- Validate request payload
   |-- Avoid sensitive data in logs
   |-- Use service credentials from Secrets Manager
   |
   v
AWS Platform Access
   |
   |-- IAM role based access to RDS, S3, SQS, SNS, CloudWatch
   |
   v
Response returned to client
```

Security practices:

- JWT-based authentication for protected APIs.
- Role-based access for customer and admin operations.
- Request validation at gateway and service level.
- Secrets stored in AWS Secrets Manager instead of application code.
- IAM roles used for AWS resource access.
- Sensitive fields are not printed in logs.
- Admin APIs are protected with stricter authorization rules.
- API versioning is considered while exposing secured APIs.

## 10. Reliability and Maintainability

The system is designed to handle failures gracefully because many flows depend on external services and asynchronous processing.

Important reliability practices:

- Idempotency key in checkout to prevent duplicate orders.
- Retry handling for temporary failures.
- Circuit breaker for slow or failing external integrations.
- DLQ for messages that cannot be processed after retry.
- Graceful fallback for non-critical flows like recommendation or notification.
- Correlation ID across services for end-to-end tracking.
- Audit records for important state changes.
- Clear error response format for support and client teams.

## 11. Observability

Observability is one of the most important parts of maintaining this application. Without proper logs and metrics, it becomes difficult to identify whether an issue is in cart, order, inventory, payment, shipment, notification, or external providers.

Key observability items:

- Structured logs with correlation ID.
- Logs containing order ID, cart ID, customer ID where safe, payment reference, and service name.
- API latency metrics.
- Error rate metrics.
- Redis hit/miss ratio.
- Kafka consumer lag.
- SQS queue depth and DLQ count.
- Payment failure percentage.
- Inventory reservation failure count.
- ECS task restart count.
- RDS CPU and slow query indicators.
- Dashboards for production monitoring.

## 12. Integration Testing and API Contract Management

Integration testing is important because most production issues in microservices are not isolated to one class or one API. They often happen because of contract mismatch, timeout differences, missing configuration, invalid payloads, or downstream behavior changes.

Testing focus areas:

- Cart to Product integration.
- Cart to Pricing integration.
- Order to Inventory integration.
- Order to Payment integration.
- Payment callback handling.
- Shipment partner response handling.
- Notification retry and DLQ handling.
- API backward compatibility.
- Environment-specific configuration validation.

API contract practices:

- Maintain OpenAPI documentation.
- Avoid breaking response fields used by existing clients.
- Add new fields in a backward-compatible way where possible.
- Introduce API versioning for breaking changes.
- Validate request and response examples during integration testing.
- Review error codes and error response structure before release.

## 13. Deployment and Release Strategy

The release process is designed to reduce production risk. A feature is not considered ready only because coding is complete. It also needs testing, deployment validation, monitoring, and rollback readiness.

Release practices:

- Code review before merge.
- Unit and integration tests in pipeline.
- Docker image build and scan.
- Deployment through Jenkins pipeline.
- Environment-specific configuration check.
- Smoke testing after deployment.
- Feature flag usage for controlled rollout where required.
- Monitoring of logs, latency, error rate, and business metrics after release.
- Rollback plan for critical services.

## 14. Production Support Documents

Support documents are important because they reduce dependency on individual team members and help the team handle production issues in a structured way.

### Issue Tracker

The issue tracker captures production and UAT issues in a consistent format.

```text
Issue ID: FH-INC-001
Title: Payment success received but order not confirmed
Environment: Production
Severity: High
Impacted Services: Order Service, Payment Service, Notification Service
Customer Impact: Payment completed but order confirmation delayed
Correlation ID: <correlation-id>
Order ID: <order-id>
Payment Reference: <payment-reference>
Current Status: Open / In Progress / Resolved / Monitoring
Owner: Backend Team
Initial Observation:
  - Payment callback received
  - Order remained in PENDING state
  - Notification was not triggered
Action Taken:
  - Checked logs using correlation ID
  - Verified payment provider response
  - Checked order update flow
  - Reprocessed failed event if required
  - Updated issue notes for support team
```

### RCA Document

RCA is created for repeated or high-impact incidents.

```text
RCA Title: Order confirmation delay after payment success
Incident Date: <date>
Severity: High
Services Involved: Order Service, Payment Service, Notification Service

What Happened:
Payment was completed successfully, but order confirmation was delayed for some orders.

Impact:
Customers saw payment completed, but order status was not updated immediately.

Root Cause:
Order update processing was delayed due to downstream failure, timeout, or message processing delay.

Immediate Fix:
Affected orders were validated and reprocessed safely.

Long-Term Fix:
- Added better alerting for payment-order mismatch.
- Improved logs with payment reference and order ID.
- Updated retry and idempotency handling.
- Added runbook steps for support team.
```

### Runbook

Runbooks are used for repeated operational issues.

Examples:

- High payment failure rate.
- Order stuck in PENDING state.
- Inventory reserved but payment failed.
- Kafka consumer lag.
- DLQ message replay.
- Redis cache issue.
- Shipment creation failure.
- High API latency after deployment.

### Deployment Checklist

```text
Before Deployment:
- Code review completed
- Unit tests passed
- Integration tests passed
- API contract reviewed
- Configuration verified
- Rollback plan ready
- Monitoring dashboard available

After Deployment:
- Health check completed
- Smoke test completed
- Error rate checked
- API latency checked
- Queue/DLQ checked
- Logs checked for new exceptions
- Business metrics monitored
```

## 15. Key Challenges

Some practical challenges in maintaining this application were:

- Handling high traffic during sale events without overloading database and downstream services.
- Keeping checkout reliable across order, inventory, payment, and shipment flows.
- Preventing duplicate orders and duplicate payments during retry scenarios.
- Managing API compatibility while services continued to evolve.
- Debugging issues across multiple services using correlation ID and business identifiers.
- Handling external payment and courier partner failures safely.
- Keeping deployment risk low using smoke tests, dashboards, and rollback awareness.
- Maintaining useful documentation so support and development teams could work with the system confidently.

## 16. My Role and Responsibilities

I worked on this project as a backend engineer with strong involvement in Java, Spring Boot, microservices, REST APIs, AWS-based deployment, Redis caching, security, observability, and production support.

My main responsibilities were:

- Designed and developed Spring Boot REST APIs for business workflows.
- Participated in service integration across Product, Cart, Order, Inventory, Payment, Shipment, Notification, and Audit services.
- Improved API throughput by reducing repeated database calls, using Redis cache effectively, reviewing query patterns, and validating pagination.
- Worked on checkout reliability using idempotency, proper error handling, audit points, and meaningful logs.
- Contributed to security implementation around JWT validation, role-based access, request validation, Secrets Manager usage, and safe logging.
- Improved observability by adding correlation IDs, structured logs, useful error categories, and dashboard-friendly metrics.
- Supported integration testing and API contract validation to reduce issues between services.
- Supported Jenkins-based deployments by validating configurations, health checks, smoke tests, and post-deployment monitoring.
- Helped investigate production issues using logs, dashboards, correlation IDs, order IDs, and payment references.
- Created and maintained support documents such as issue tracker notes, RCA format, runbooks, deployment checklist, and troubleshooting steps.

## 17. Summary

FashionHub is an enterprise microservices platform where the main challenge is not only building services, but keeping them reliable, secure, observable, and maintainable in production.

The project helped me strengthen my grip on Java, Spring Boot, REST APIs, microservices integration, Redis caching, AWS services, security practices, CI/CD, observability, production troubleshooting, and support documentation.

The biggest learning from this project is that a system becomes production-ready only when business flows are clear, APIs are stable, logs are useful, deployments are controlled, and the support team can quickly understand what happened during an issue.
