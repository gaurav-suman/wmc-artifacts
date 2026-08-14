# FashionHub Enterprise Commerce Platform

> Enterprise-grade, cloud-native, event-driven women's fashion e-commerce platform designed using Java, Spring Boot, Microservices, AWS ECS, Kafka, SQS, SNS, RDS, ElastiCache, S3, MCP, and AI-enabled engineering skills.

---

## 1. Executive Summary

FashionHub is a enterprise e-commerce platform for selling women's clothing, footwear, accessories, and seasonal fashion collections.

The platform is designed for high traffic events such as Diwali Sale, Black Friday, End of Season Sale, flash campaigns, influencer-led launches, and personalized recommendation journeys.

The system focuses on:

- High availability
- Scalability
- Resilience
- Performance
- Reliability
- Security
- Maintainability
- Observability
- Cost awareness
- AI-assisted engineering productivity

---

## 2. Business Problem

FashionHub sells women's clothing across multiple categories such as ethnic wear, western wear, party wear, office wear, footwear, handbags, accessories, and seasonal collections.

The business needs a platform that can:

- Handle heavy traffic during sale events
- Support real-time inventory visibility
- Process large order volumes
- Avoid duplicate orders and payments
- Provide personalized recommendations
- Support returns and refunds
- Integrate with payment and logistics partners
- Notify customers through email, SMS, and push notifications
- Maintain product images, invoices, and audit records
- Provide strong production monitoring and incident response

---

## 3. Key Capabilities

### Customer Capabilities

- Customer registration and login
- Product search and filtering
- Wishlist management
- Shopping cart
- Checkout
- Order tracking
- Returns and refunds
- Notifications
- Reviews and ratings
- Personalized recommendations

### Business Capabilities

- Product catalog management
- Inventory management
- Pricing and promotions
- Coupon management
- Payment processing
- Shipment orchestration
- Campaign management
- Product image management
- Audit reporting
- Operational dashboards

### Engineering Capabilities

- Microservices architecture
- Event-driven communication
- Distributed transaction management
- Distributed caching
- CI/CD automation
- Infrastructure as Code
- Observability
- Security controls
- AI-assisted development through MCP and Skills

---

## 4. Technology Stack

| Area | Technology |
|---|---|
| Language | Java 21 |
| Framework | Spring Boot 3.x |
| API | REST, OpenAPI |
| Security | Spring Security, OAuth2, JWT |
| Messaging | Apache Kafka, AWS SQS, AWS SNS |
| Database | AWS RDS PostgreSQL |
| Cache | AWS ElastiCache Redis |
| Storage | AWS S3 |
| Compute | AWS ECS Fargate |
| Containerization | Docker |
| CI/CD | Jenkins |
| Infrastructure | AWS CloudFormation |
| Monitoring | CloudWatch, Prometheus, Grafana |
| Tracing | OpenTelemetry, Jaeger compatible tracing |
| AI Enablement | MCP Server, Custom Skills, Copilot-assisted workflows |

---

## 5. High-Level Architecture

```text
                                      +-------------------------+
                                      |      Web / Mobile App    |
                                      +-----------+-------------+
                                                  |
                                                  v
                                      +-------------------------+
                                      |    AWS ALB + AWS WAF     |
                                      +-----------+-------------+
                                                  |
                                                  v
                                      +-------------------------+
                                      |       API Gateway        |
                                      +-----------+-------------+
                                                  |
        ---------------------------------------------------------------------------------
        |             |             |             |             |             |           |
        v             v             v             v             v             v           v
+---------------+ +-----------+ +-----------+ +-----------+ +-----------+ +-----------+ +-----------+
| Identity      | | Customer  | | Product   | | Cart      | | Pricing   | | Order     | | Review    |
| Service       | | Service   | | Service   | | Service   | | Service   | | Service   | | Service   |
+---------------+ +-----------+ +-----------+ +-----------+ +-----------+ +-----------+ +-----------+
                                      |             |
                                      |             v
                                      |     +-------------------+
                                      |     | Saga Orchestrator  |
                                      |     +---------+---------+
                                      |               |
                                      v               v
                               +-----------------------------+
                               |       Kafka Event Bus        |
                               +-------------+---------------+
                                             |
        ---------------------------------------------------------------------------------
        |                 |                  |                  |                |        |
        v                 v                  v                  v                v        v
+---------------+ +---------------+ +----------------+ +----------------+ +----------+ +----------------+
| Inventory     | | Payment       | | Shipment       | | Notification   | | Audit    | | Recommendation |
| Service       | | Service       | | Service        | | Service        | | Service  | | Service        |
+---------------+ +---------------+ +----------------+ +----------------+ +----------+ +----------------+
        |                 |                  |                  |                |        |
        v                 v                  v                  v                v        v
+----------------------------------------------------------------------------------------------+
|                                  AWS Platform Layer                                            |
| ECS Fargate | RDS PostgreSQL | ElastiCache Redis | SQS | SNS | S3 | CloudWatch | Secrets Manager |
+----------------------------------------------------------------------------------------------+
```

---

## 6. Microservices Landscape

### 6.1 Identity Service

Responsible for authentication and authorization.

Responsibilities:

- User login
- JWT generation
- Refresh token validation
- Role-based access control
- Admin and customer roles

Patterns used:

- Client-server pattern
- Facade pattern
- Token-based security

### 6.2 Customer Service

Responsible for customer profile lifecycle.

Responsibilities:

- Customer profile management
- Address management
- Preferences
- Loyalty profile

Events published:

- customer-created
- customer-profile-updated

### 6.3 Product Service

Responsible for product catalog.

Responsibilities:

- Product details
- Category management
- Brand information
- Product attributes
- Product image metadata
- Product status

Cache usage:

- Product details cache
- Category cache
- Search filter cache

Patterns used:

- Repository pattern
- Builder pattern
- Cache-aside pattern
- Facade pattern

### 6.4 Inventory Service

Responsible for stock management.

Responsibilities:

- Available stock
- Reserved stock
- Stock release
- Stock adjustment
- Inventory audit

Events consumed:

- order-created
- payment-failed
- order-cancelled

Events published:

- inventory-reserved
- inventory-reservation-failed
- inventory-released

Patterns used:

- Saga participant
- Idempotency pattern
- Optimistic locking

### 6.5 Cart Service

Responsible for shopping cart lifecycle.

Responsibilities:

- Add item to cart
- Remove item
- Update quantity
- Apply coupon preview
- Cart summary

Cache usage:

- Redis cart cache

Patterns used:

- Proxy pattern for cache access
- Strategy pattern for pricing preview

### 6.6 Pricing Service

Responsible for price calculation.

Responsibilities:

- Base price
- Sale price
- Dynamic discounts
- Customer segment pricing
- Seasonal campaign pricing

Patterns used:

- Strategy pattern
- Factory pattern
- Chain of Responsibility pattern

Example strategies:

- FlatDiscountStrategy
- PercentageDiscountStrategy
- FestivalSaleStrategy
- LoyaltyCustomerStrategy
- FirstOrderDiscountStrategy

### 6.7 Promotion Service

Responsible for coupons and offers.

Responsibilities:

- Coupon validation
- Usage limit
- Expiry validation
- Customer eligibility
- Campaign rules

Patterns used:

- Specification pattern
- Strategy pattern
- Rule engine style evaluation

### 6.8 Order Service

Responsible for order lifecycle and checkout orchestration.

Responsibilities:

- Create order
- Update order status
- Order history
- Order cancellation
- Idempotent order creation
- Saga orchestration

Events published:

- order-created
- order-confirmed
- order-cancelled

Patterns used:

- Saga orchestration
- CQRS
- Builder pattern
- Template method pattern
- Idempotency pattern

### 6.9 Payment Service

Responsible for payment processing.

Responsibilities:

- Payment authorization
- Payment capture
- Refund initiation
- Payment status tracking
- Third-party payment gateway integration

Patterns used:

- Adapter pattern
- Circuit breaker
- Retry pattern
- Factory pattern

Events published:

- payment-authorized
- payment-failed
- refund-processed

### 6.10 Shipment Service

Responsible for delivery orchestration.

Responsibilities:

- Shipment creation
- Courier partner integration
- AWB generation
- Shipment tracking
- Delivery status update

Patterns used:

- Adapter pattern
- Circuit breaker
- Retry pattern

Events published:

- shipment-created
- shipment-delivered
- shipment-failed

### 6.11 Notification Service

Responsible for customer communication.

Responsibilities:

- Email notification
- SMS notification
- Push notification
- Order confirmation
- Payment failure notification
- Delivery updates

Messaging:

- SNS for fanout
- SQS for reliable delivery

Patterns used:

- Observer pattern
- Template method pattern

### 6.12 Recommendation Service

Responsible for personalized product recommendations.

Responsibilities:

- Personalized recommendations
- Recently viewed products
- Similar products
- Trending products
- Campaign recommendations

Patterns used:

- Strategy pattern
- Cache-aside pattern
- Asynchronous processing

### 6.13 Review Service

Responsible for product reviews and ratings.

Responsibilities:

- Add review
- Moderate review
- Product rating aggregation
- Customer feedback

Patterns used:

- CQRS read model for product rating summary
- Observer pattern for rating aggregation updates

### 6.14 Audit Service

Responsible for compliance and traceability.

Responsibilities:

- Audit trail
- Order events
- Payment events
- Admin activity
- Security events

Storage:

- RDS for searchable audit records
- S3 for archived audit logs

---

## 7. Domain Driven Design

The application is divided into clear bounded contexts.

```text
Customer Domain
- Customer
- Address
- Preference
- LoyaltyProfile

Catalog Domain
- Product
- Category
- Brand
- ProductImage

Commercial Domain
- Price
- Coupon
- Promotion
- Campaign

Order Domain
- Cart
- Order
- OrderItem
- Checkout

Fulfillment Domain
- Inventory
- Shipment
- Return
- Refund

Engagement Domain
- Review
- Rating
- Recommendation
- Notification
```

---

## 8. Architecture Patterns

### 8.1 Saga Pattern

Used for distributed transaction management during checkout.

```text
Order Service
→ Inventory Service
→ Payment Service
→ Shipment Service
→ Notification Service
```

#### Successful Saga Flow

```text
1. Order Service creates order with PENDING status
2. Order Service publishes order-created event
3. Inventory Service reserves stock
4. Inventory Service publishes inventory-reserved event
5. Payment Service authorizes payment
6. Payment Service publishes payment-authorized event
7. Shipment Service creates shipment
8. Shipment Service publishes shipment-created event
9. Order Service marks order as CONFIRMED
10. Notification Service sends order confirmation
```

#### Compensation Flow

```text
1. Order created
2. Inventory reserved
3. Payment failed
4. Saga compensation triggered
5. Inventory released
6. Order marked as CANCELLED
7. Customer notified
```

### 8.2 CQRS Pattern

Command Query Responsibility Segregation is used for order and product read models.

Write side:

- Handles business validation
- Maintains consistency
- Publishes events

Read side:

- Optimized for search and query
- Uses projection tables
- Uses Redis for frequently accessed views

Example:

```text
Order Write Model:
orders, order_items, order_status_history

Order Read Model:
customer_order_summary_view, order_tracking_view
```

### 8.3 Event-Driven Architecture

Services communicate through events to reduce tight coupling.

Benefits:

- Independent scaling
- Resilience
- Better fault isolation
- Easier extension of new capabilities

### 8.4 Circuit Breaker Pattern

Implemented using Resilience4j.

Used for:

- Payment gateway calls
- Shipment provider calls
- Recommendation engine calls

States:

- CLOSED
- OPEN
- HALF_OPEN

### 8.5 Retry Pattern

Used for transient failures.

Retry is configured with:

- Exponential backoff
- Maximum retry limit
- Dead letter fallback

### 8.6 Bulkhead Pattern

Used to isolate failures.

Examples:

- Separate thread pools for payment calls
- Separate connection pools for product and order DB
- Separate queues for notification channels

### 8.7 Cache-Aside Pattern

Used with Redis.

```text
1. Check Redis cache
2. If cache hit, return data
3. If cache miss, fetch from RDS
4. Store result in Redis
5. Return response
```

### 8.8 API Gateway Pattern

The API Gateway acts as a single entry point for external clients.

Responsibilities:

- Request routing
- Authentication validation
- Rate limiting
- Request correlation ID
- API version routing

---

## 9. Java Design Patterns

### Facade Pattern

CheckoutFacade simplifies the checkout flow.

```text
Client → CheckoutFacade → Cart + Pricing + Order + Inventory + Payment
```

### Strategy Pattern

Used for pricing, discounts, shipping charges, and recommendation algorithms.

### Observer Pattern

Used through Kafka event consumers.

```text
order-created event observed by Inventory Service, Audit Service, Notification Service
```

### Factory Pattern

Used for payment gateway client creation.

Examples:

- RazorpayPaymentClient
- StripePaymentClient
- InternalWalletPaymentClient

### Adapter Pattern

Used to integrate third-party systems.

Examples:

- Payment gateway adapter
- Courier partner adapter
- SMS provider adapter

### Builder Pattern

Used for complex objects.

Examples:

- Order
- Product
- ShipmentRequest
- PaymentRequest

### Template Method Pattern

Used for standard processing flows.

```text
AbstractOrderProcessor
- validate()
- calculatePrice()
- reserveInventory()
- processPayment()
- confirmOrder()
```

### Proxy Pattern

Used for:

- Caching proxy
- Security proxy
- Remote service proxy

### Chain of Responsibility Pattern

Used for coupon and promotion validation.

```text
CouponExistsValidator
→ CouponExpiryValidator
→ CouponUsageLimitValidator
→ CustomerEligibilityValidator
→ MinimumCartValueValidator
```

---

## 10. Communication Design

### Synchronous Communication

Used where immediate response is required.

Examples:

- Get product details
- Validate customer
- Fetch cart summary

Technologies:

- REST
- OpenFeign
- API Gateway

### Asynchronous Communication

Used for durable, scalable, loosely coupled processing.

Examples:

- Order created
- Inventory reserved
- Payment completed
- Shipment created
- Notification triggered

Technologies:

- Kafka
- SQS
- SNS

---

## 11. Kafka Topic Design

| Topic | Producer | Consumers | Purpose |
|---|---|---|---|
| customer-created | Customer Service | Notification, Audit | Customer onboarding |
| product-created | Product Service | Recommendation, Audit | Product catalog update |
| cart-checked-out | Cart Service | Order Service | Checkout initiation |
| order-created | Order Service | Inventory, Audit | Start order saga |
| inventory-reserved | Inventory Service | Payment, Order | Continue saga |
| inventory-released | Inventory Service | Order, Audit | Compensation tracking |
| payment-authorized | Payment Service | Order, Shipment, Audit | Payment success |
| payment-failed | Payment Service | Order, Inventory, Notification | Compensation |
| shipment-created | Shipment Service | Order, Notification | Shipment creation |
| shipment-delivered | Shipment Service | Order, Notification | Delivery confirmation |
| refund-processed | Payment Service | Order, Notification, Audit | Refund completion |

### Kafka Reliability Practices

- Idempotent producers
- Consumer groups
- Partitioning by aggregate ID
- Dead letter topics
- Replayable event streams
- Schema validation
- Correlation ID propagation

---

## 12. Database Design

Each microservice owns its database schema. Direct database access across services is not allowed.

### Product Service Schema

```sql
CREATE TABLE products (
  product_id UUID PRIMARY KEY,
  product_name VARCHAR(255) NOT NULL,
  brand VARCHAR(100),
  category_id UUID NOT NULL,
  description TEXT,
  base_price NUMERIC(12,2) NOT NULL,
  status VARCHAR(30) NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL
);

CREATE TABLE product_images (
  image_id UUID PRIMARY KEY,
  product_id UUID NOT NULL,
  s3_url TEXT NOT NULL,
  display_order INT,
  created_at TIMESTAMP NOT NULL
);

CREATE TABLE categories (
  category_id UUID PRIMARY KEY,
  category_name VARCHAR(100) NOT NULL,
  parent_category_id UUID,
  status VARCHAR(30) NOT NULL
);
```

### Inventory Service Schema

```sql
CREATE TABLE inventory (
  inventory_id UUID PRIMARY KEY,
  product_id UUID NOT NULL,
  available_qty INT NOT NULL,
  reserved_qty INT NOT NULL,
  version INT NOT NULL,
  updated_at TIMESTAMP NOT NULL
);

CREATE TABLE inventory_reservations (
  reservation_id UUID PRIMARY KEY,
  order_id UUID NOT NULL,
  product_id UUID NOT NULL,
  quantity INT NOT NULL,
  status VARCHAR(30) NOT NULL,
  created_at TIMESTAMP NOT NULL
);
```

### Order Service Schema

```sql
CREATE TABLE orders (
  order_id UUID PRIMARY KEY,
  customer_id UUID NOT NULL,
  order_status VARCHAR(30) NOT NULL,
  payment_status VARCHAR(30),
  shipment_status VARCHAR(30),
  total_amount NUMERIC(12,2) NOT NULL,
  idempotency_key VARCHAR(100) UNIQUE NOT NULL,
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL
);

CREATE TABLE order_items (
  order_item_id UUID PRIMARY KEY,
  order_id UUID NOT NULL,
  product_id UUID NOT NULL,
  quantity INT NOT NULL,
  unit_price NUMERIC(12,2) NOT NULL,
  total_price NUMERIC(12,2) NOT NULL
);

CREATE TABLE order_status_history (
  history_id UUID PRIMARY KEY,
  order_id UUID NOT NULL,
  old_status VARCHAR(30),
  new_status VARCHAR(30) NOT NULL,
  reason TEXT,
  created_at TIMESTAMP NOT NULL
);
```

### Payment Service Schema

```sql
CREATE TABLE payments (
  payment_id UUID PRIMARY KEY,
  order_id UUID NOT NULL,
  customer_id UUID NOT NULL,
  amount NUMERIC(12,2) NOT NULL,
  payment_method VARCHAR(50) NOT NULL,
  payment_status VARCHAR(30) NOT NULL,
  gateway_reference_id VARCHAR(100),
  created_at TIMESTAMP NOT NULL,
  updated_at TIMESTAMP NOT NULL
);
```

---

## 13. API Contract Examples

### Create Order

```http
POST /api/v1/orders
Idempotency-Key: 922a9347-order-unique-key
Authorization: Bearer <jwt-token>
Content-Type: application/json
```

```json
{
  "customerId": "c101",
  "cartId": "cart-991",
  "addressId": "addr-781",
  "paymentMethod": "CARD"
}
```

### Response

```json
{
  "orderId": "ord-10091",
  "status": "PENDING",
  "message": "Order creation accepted and processing started"
}
```

### Get Order Tracking

```http
GET /api/v1/orders/ord-10091/tracking
Authorization: Bearer <jwt-token>
```

```json
{
  "orderId": "ord-10091",
  "orderStatus": "CONFIRMED",
  "paymentStatus": "AUTHORIZED",
  "shipmentStatus": "CREATED"
}
```

---

## 14. End-to-End Sequence Diagram

### Successful Checkout

```text
Customer
  |
  v
API Gateway
  |
  v
Order Service
  |
  |-- publish order-created --> Kafka
                                  |
                                  v
                           Inventory Service
                                  |
                                  |-- publish inventory-reserved --> Kafka
                                                                    |
                                                                    v
                                                             Payment Service
                                                                    |
                                                                    |-- publish payment-authorized --> Kafka
                                                                                                      |
                                                                                                      v
                                                                                               Shipment Service
                                                                                                      |
                                                                                                      |-- publish shipment-created --> Kafka
                                                                                                                                        |
                                                                                                                                        v
                                                                                                                                 Order Service
                                                                                                                                        |
                                                                                                                                        v
                                                                                                                             Order CONFIRMED
```

### Failed Payment Compensation

```text
Order Created
   |
Inventory Reserved
   |
Payment Failed
   |
Saga Compensation
   |
Inventory Released
   |
Order Cancelled
   |
Customer Notified
```

---

## 15. AWS Deployment Architecture

### AWS Components

| AWS Service | Usage |
|---|---|
| VPC | Network isolation |
| Public Subnets | ALB and NAT Gateway |
| Private Subnets | ECS tasks, RDS, Redis |
| ECS Fargate | Run microservice containers |
| ALB | Load balancing |
| RDS PostgreSQL | Relational persistence |
| ElastiCache Redis | Distributed caching |
| S3 | Product images, invoices, reports |
| SQS | Durable async processing |
| SNS | Fanout notifications |
| CloudWatch | Logs and metrics |
| Secrets Manager | Secrets and credentials |
| IAM | Access control |

### VPC Deployment Model

```text
fashionhub-vpc
|
|-- public-subnet-a
|   `-- Application Load Balancer
|
|-- public-subnet-b
|   `-- NAT Gateway
|
|-- private-subnet-a
|   |-- ECS Services
|   |-- RDS Primary
|   `-- Redis Node
|
`-- private-subnet-b
    |-- ECS Services
    |-- RDS Standby
    `-- Redis Replica
```

---

## 16. CloudFormation Template

> This is a compact enterprise starter template. In production, each service can be split into nested stacks.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: FashionHub Enterprise E-Commerce Platform Infrastructure

Parameters:
  EnvironmentName:
    Type: String
    Default: dev

  VpcCidr:
    Type: String
    Default: 10.20.0.0/16

Resources:
  FashionHubVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCidr
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub fashionhub-${EnvironmentName}-vpc

  PublicSubnetA:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref FashionHubVPC
      CidrBlock: 10.20.1.0/24
      AvailabilityZone: !Select [0, !GetAZs '']
      MapPublicIpOnLaunch: true

  PrivateSubnetA:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref FashionHubVPC
      CidrBlock: 10.20.11.0/24
      AvailabilityZone: !Select [0, !GetAZs '']

  ECSCluster:
    Type: AWS::ECS::Cluster
    Properties:
      ClusterName: !Sub fashionhub-${EnvironmentName}-cluster

  ProductImageBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub fashionhub-${EnvironmentName}-product-images
      VersioningConfiguration:
        Status: Enabled

  NotificationTopic:
    Type: AWS::SNS::Topic
    Properties:
      TopicName: !Sub fashionhub-${EnvironmentName}-notification-topic

  OrderProcessingQueue:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub fashionhub-${EnvironmentName}-order-processing-queue
      VisibilityTimeout: 60
      RedrivePolicy:
        deadLetterTargetArn: !GetAtt OrderProcessingDLQ.Arn
        maxReceiveCount: 3

  OrderProcessingDLQ:
    Type: AWS::SQS::Queue
    Properties:
      QueueName: !Sub fashionhub-${EnvironmentName}-order-processing-dlq

  RedisSubnetGroup:
    Type: AWS::ElastiCache::SubnetGroup
    Properties:
      Description: Redis subnet group
      SubnetIds:
        - !Ref PrivateSubnetA

  RedisReplicationGroup:
    Type: AWS::ElastiCache::ReplicationGroup
    Properties:
      ReplicationGroupDescription: FashionHub Redis Cache
      Engine: redis
      CacheNodeType: cache.t3.micro
      NumCacheClusters: 1
      AutomaticFailoverEnabled: false
      CacheSubnetGroupName: !Ref RedisSubnetGroup

  RDSSubnetGroup:
    Type: AWS::RDS::DBSubnetGroup
    Properties:
      DBSubnetGroupDescription: RDS subnet group
      SubnetIds:
        - !Ref PrivateSubnetA

  FashionHubDatabase:
    Type: AWS::RDS::DBInstance
    Properties:
      DBInstanceIdentifier: !Sub fashionhub-${EnvironmentName}-db
      Engine: postgres
      DBInstanceClass: db.t3.micro
      AllocatedStorage: 50
      MasterUsername: fashionhub_admin
      MasterUserPassword: changeMeInSecretsManager
      DBSubnetGroupName: !Ref RDSSubnetGroup
      PubliclyAccessible: false

Outputs:
  VpcId:
    Value: !Ref FashionHubVPC

  ECSClusterName:
    Value: !Ref ECSCluster

  ProductImageBucketName:
    Value: !Ref ProductImageBucket

  NotificationTopicArn:
    Value: !Ref NotificationTopic

  OrderQueueUrl:
    Value: !Ref OrderProcessingQueue
```

---

## 17. Service Configuration Example

### application.yml

```yaml
server:
  port: 8080

spring:
  application:
    name: order-service

  datasource:
    url: jdbc:postgresql://${DB_HOST:localhost}:5432/order_db
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}

  jpa:
    hibernate:
      ddl-auto: validate
    properties:
      hibernate:
        dialect: org.hibernate.dialect.PostgreSQLDialect

  kafka:
    bootstrap-servers: ${KAFKA_BOOTSTRAP_SERVERS}
    consumer:
      group-id: order-service-group
      auto-offset-reset: earliest
    producer:
      acks: all
      retries: 3

redis:
  host: ${REDIS_HOST}
  port: 6379

aws:
  region: ap-south-1
  s3:
    product-image-bucket: ${PRODUCT_IMAGE_BUCKET}
  sqs:
    order-processing-queue: ${ORDER_PROCESSING_QUEUE}
  sns:
    notification-topic: ${NOTIFICATION_TOPIC_ARN}

resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        failureRateThreshold: 50
        waitDurationInOpenState: 30s
        slidingWindowSize: 10
  retry:
    instances:
      paymentService:
        maxAttempts: 3
        waitDuration: 2s
```

---

## 18. Dockerfile

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

---

## 19. Jenkins CI/CD Pipeline

```groovy
pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPOSITORY = 'fashionhub/order-service'
        ECS_CLUSTER = 'fashionhub-prod-cluster'
        ECS_SERVICE = 'order-service'
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests=false'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Code Quality') {
            steps {
                sh 'mvn sonar:sonar'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'trivy fs . || true'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${ECR_REPOSITORY}:${IMAGE_TAG} .'
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                docker tag ${ECR_REPOSITORY}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
                docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPOSITORY}:${IMAGE_TAG}
                '''
            }
        }

        stage('Deploy To ECS') {
            steps {
                sh '''
                aws ecs update-service \
                  --cluster ${ECS_CLUSTER} \
                  --service ${ECS_SERVICE} \
                  --force-new-deployment \
                  --region ${AWS_REGION}
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                sh 'curl -f https://api.fashionhub.com/order-service/actuator/health'
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully'
        }
        failure {
            echo 'Deployment failed. Rollback may be required.'
        }
    }
}
```

---

## 20. Capacity Planning

### Normal Traffic Assumptions

| Metric | Estimate |
|---|---:|
| Registered users | 2 million |
| Daily active users | 250,000 |
| Concurrent users | 25,000 |
| Products | 500,000 |
| Orders per hour | 10,000 |
| Product images | 5 TB |
| Average API latency target | < 300 ms |

### Peak Sale Assumptions

| Metric | Estimate |
|---|---:|
| Concurrent users | 100,000 |
| Transactions per minute | 5,000 |
| Product detail reads per minute | 200,000 |
| Cart updates per minute | 50,000 |
| Order creation requests per minute | 5,000 |

### Scaling Strategy

- ECS Service Auto Scaling based on CPU and memory
- Dedicated scaling rules for Order, Product, Cart, and Inventory services
- Redis for read-heavy workloads
- Kafka/SQS for asynchronous load leveling
- RDS read replicas for query-heavy workloads
- CDN-ready S3 product image delivery

---

## 21. Performance Engineering

### Optimization Techniques

- Redis caching for product details and offers
- CQRS read models for order tracking
- Database indexing on high-cardinality columns
- Connection pooling using HikariCP
- Async processing for notifications and audits
- Pagination for product listing APIs
- Bulk APIs for admin catalog operations
- Lazy loading avoided in critical paths
- JVM tuning for containerized workloads

### Sample Indexing Strategy

```sql
CREATE INDEX idx_products_category_status ON products(category_id, status);
CREATE INDEX idx_orders_customer_created ON orders(customer_id, created_at DESC);
CREATE INDEX idx_inventory_product ON inventory(product_id);
CREATE INDEX idx_payments_order ON payments(order_id);
```

---

## 22. Reliability Engineering

### Idempotency

Every order creation request must contain an Idempotency-Key.

Purpose:

- Avoid duplicate orders
- Avoid duplicate payments
- Support safe retries

### Dead Letter Queues

DLQs are used for failed message processing.

Examples:

- order-processing-dlq
- payment-processing-dlq
- notification-dlq
- shipment-dlq

### Graceful Degradation

If Recommendation Service fails:

- Show trending products
- Show category bestsellers
- Avoid blocking checkout

If Notification Service fails:

- Persist notification request
- Retry asynchronously
- Do not fail the order

### Event Replay

Kafka events can be replayed to rebuild projections.

Use cases:

- Rebuild order read model
- Recalculate product rating summary
- Rebuild audit view

---

## 23. Security Architecture

### Authentication and Authorization

- OAuth2 based login
- JWT access token
- Refresh token rotation
- Role-based access control
- Admin/customer separation

### API Security

- AWS WAF
- Rate limiting
- IP allowlisting for admin APIs
- Request validation
- Input sanitization

### Data Security

- Encryption at rest
- Encryption in transit
- Database credentials stored in AWS Secrets Manager
- S3 bucket encryption
- Least privilege IAM roles

### Application Security

- OWASP validation
- Dependency scanning
- Container image scanning
- Static code analysis
- Secure logging without sensitive data leakage

---

## 24. Observability

### Logs

- Structured JSON logs
- Correlation ID in every request
- Centralized CloudWatch logging

### Metrics

Business metrics:

- Orders per minute
- Revenue per hour
- Payment success rate
- Cart abandonment rate
- Inventory reservation failure rate

Technical metrics:

- API latency
- Error rate
- Kafka consumer lag
- Redis hit ratio
- RDS CPU
- ECS CPU and memory
- SQS queue depth

### Tracing

Distributed tracing is enabled using OpenTelemetry.

Trace example:

```text
API Gateway
→ Order Service
→ Inventory Service
→ Payment Service
→ Shipment Service
→ Notification Service
```

---

## 25. SRE and Production Operations

### Production Readiness Checklist

- Health endpoints enabled
- Readiness and liveness checks configured
- Dashboards created
- Alerts configured
- Runbooks available
- Rollback process tested
- DR backup verified
- Load test completed
- Security scan completed
- Performance baseline captured

### Incident Management

Incident lifecycle:

```text
Alert Triggered
→ Triage
→ Impact Analysis
→ Mitigation
→ Root Cause Analysis
→ Corrective Actions
→ Retrospective
```

### Runbooks

Example runbooks:

- High Kafka lag
- Payment failure spike
- Redis memory pressure
- RDS CPU high
- ECS task restart loop
- SQS DLQ message replay
- Product image upload failure

---

## 26. Disaster Recovery

### Backup Strategy

- RDS automated backups
- RDS snapshots before major releases
- S3 versioning enabled
- Infrastructure reproducible through CloudFormation

### Recovery Targets

| Area | Target |
|---|---|
| RTO | 1 hour |
| RPO | 15 minutes |
| Critical service recovery | Priority based |
| Data backup frequency | Automated |

### DR Approach

- Recreate infrastructure using CloudFormation
- Restore RDS snapshot
- Redeploy ECS services
- Replay events if required
- Validate health and smoke tests

---

## 27. Cost Optimization

Cost optimization practices:

- ECS Fargate right sizing
- Auto Scaling based on demand
- S3 lifecycle policies
- Redis sizing based on hit ratio and memory
- RDS performance insights for query tuning
- Log retention policies
- Non-production environment shutdown strategy
- Reserved capacity planning for stable workloads

---

## 28. AI Engineering Enablement

FashionHub includes an internal AI engineering enablement layer to improve developer productivity, operations, and knowledge discovery.

### MCP Server

A custom Model Context Protocol server is built for the project.

The MCP server connects engineering tools and operational systems so developers can retrieve useful project context through AI-enabled workflows.

Connected systems:

- GitHub repositories
- Jenkins pipelines
- CloudWatch logs
- Kafka topic metadata
- Architecture documents
- API documentation
- Runbooks
- Incident records
- Deployment history

### MCP Capabilities

- Search architecture decisions
- Analyze production logs
- Summarize incidents
- Review deployment history
- Find API ownership
- Explain service dependencies
- Generate RCA drafts
- Identify Kafka lag patterns
- Suggest runbook steps
- Summarize release impact

---

## 29. Custom Skills

Custom skills are created for routine and repeatable engineering work.

### RCA Skill

Input:

```text
incidentId
serviceName
startTime
endTime
```

Output:

```text
Incident summary
Timeline
Impacted services
Probable root cause
Immediate fix
Long-term corrective action
```

### Kafka Health Skill

Responsibilities:

- Check topic lag
- Identify slow consumer groups
- Identify partition imbalance
- Summarize failed events
- Recommend scaling action

### Deployment Review Skill

Responsibilities:

- Review changed services
- Validate Jenkins build status
- Check ECS deployment status
- Scan recent errors
- Generate release summary

### Pull Request Review Skill

Responsibilities:

- Identify missing tests
- Check coding standards
- Highlight risky changes
- Check API contract changes
- Suggest refactoring opportunities

### API Documentation Skill

Responsibilities:

- Generate API documentation
- Update endpoint summaries
- Create sample request and response payloads
- Highlight breaking changes

### Runbook Skill

Responsibilities:

- Generate runbooks from recurring incidents
- Convert troubleshooting notes into structured steps
- Recommend monitoring alerts
- Summarize operational ownership

---

## 30. Architecture Decision Records

### ADR-001: Use Microservices Architecture

Decision:

```text
Use microservices instead of a monolith.
```

Reason:

- Independent deployments
- Service-level scalability
- Clear domain ownership
- Better team autonomy

Trade-off:

- Increased distributed system complexity
- Requires strong observability and DevOps maturity

### ADR-002: Use Saga for Distributed Transactions

Decision:

```text
Use Saga orchestration for checkout transaction management.
```

Reason:

- Avoid distributed database transactions
- Support compensation
- Improve service autonomy

Trade-off:

- Eventual consistency
- More complex failure handling

### ADR-003: Use Kafka for Core Domain Events

Decision:

```text
Use Kafka for high-volume domain events.
```

Reason:

- High throughput
- Replay capability
- Consumer group support
- Event-driven extensibility

Trade-off:

- Operational complexity
- Requires schema discipline

### ADR-004: Use Redis for Distributed Cache

Decision:

```text
Use ElastiCache Redis for frequently accessed read data.
```

Reason:

- Reduce database load
- Improve response time
- Support session and cart caching

Trade-off:

- Cache invalidation complexity
- Requires TTL strategy

### ADR-005: Use ECS Fargate for Deployment

Decision:

```text
Deploy services on AWS ECS Fargate.
```

Reason:

- Container-native deployment
- Reduced infrastructure management
- Easy autoscaling
- Good AWS integration

Trade-off:

- Less control than self-managed Kubernetes
- Requires ECS task sizing discipline

---

## 31. Testing Strategy

### Unit Testing

- Service layer tests
- Utility tests
- Strategy tests
- Validator tests

### Integration Testing

- Database integration
- Kafka consumer/producer tests
- Redis integration
- SQS/SNS integration

### Contract Testing

- API provider contracts
- Consumer contract verification
- Backward compatibility checks

### Performance Testing

- Product listing load test
- Checkout load test
- Payment callback test
- Kafka throughput test

### Resilience Testing

- Payment service timeout
- Inventory service failure
- Kafka consumer delay
- Redis unavailable
- RDS slow query simulation

---

## 32. Release Strategy

### Deployment Model

- Dev
- UAT
- Staging
- Production

### Release Practices

- Feature flags
- Blue-green deployment
- Canary releases for critical services
- Automated rollback support
- Smoke tests after deployment
- Release approval gate

---

## 33. Team and Leadership Practices

As a Technical Lead, the responsibilities include:

- Microservice design
- API design reviews
- Code reviews
- Cloud architecture decisions
- Production incident handling
- Root cause analysis
- Sprint planning support
- Mentoring junior engineers
- Performance tuning
- Security review participation
- Release governance
- Documentation and knowledge sharing

Engineering culture followed:

- Ownership
- Technical excellence
- Effective communication
- Retrospectives
- Continuous improvement
- Learning and sharing
- Customer-first mindset

---

## 34. Non-Functional Requirements

| Area | Target |
|---|---|
| Availability | 99.95%+ |
| Read latency | < 300 ms for cached reads |
| Checkout reliability | No duplicate order/payment |
| Scalability | Horizontal scaling |
| Security | Enterprise-grade controls |
| Observability | End-to-end tracing |
| Resilience | Circuit breaker, retry, DLQ |
| Maintainability | Domain-oriented services |
| Recovery | Automated backup and IaC-based rebuild |
| Cost | Right-sized and scalable infrastructure |

---

## 35. Project Structure

```text
fashionhub-platform
|
|-- services
|   |-- identity-service
|   |-- customer-service
|   |-- product-service
|   |-- inventory-service
|   |-- cart-service
|   |-- pricing-service
|   |-- promotion-service
|   |-- order-service
|   |-- payment-service
|   |-- shipment-service
|   |-- notification-service
|   |-- review-service
|   |-- recommendation-service
|   `-- audit-service
|
|-- infrastructure
|   |-- cloudformation
|   |-- ecs-task-definitions
|   `-- config
|
|-- devops
|   |-- Jenkinsfile
|   |-- docker
|   `-- deployment-scripts
|
|-- docs
|   |-- architecture
|   |-- adr
|   |-- api-contracts
|   |-- runbooks
|   `-- rca
|
`-- ai-enablement
    |-- mcp-server
    `-- skills
```

---