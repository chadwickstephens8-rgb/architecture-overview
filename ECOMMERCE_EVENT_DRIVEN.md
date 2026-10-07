# E-Commerce Event-Driven Architecture

This diagram illustrates an event-driven architecture for an e-commerce platform where services communicate asynchronously through events.

## Architecture Diagram

```mermaid
graph TB
    subgraph Client["Client Layer"]
        Web[Web App]
        Mobile[Mobile App]
        Admin[Admin Dashboard]
    end

    subgraph API["API Layer"]
        Gateway[API Gateway]
    end

    subgraph Services["Microservices"]
        User[User Service]
        Product[Product Service]
        Cart[Shopping Cart Service]
        Order[Order Service]
        Payment[Payment Service]
        Inventory[Inventory Service]
        Shipping[Shipping Service]
        Notification[Notification Service]
    end

    subgraph EventBus["Event Bus / Message Broker"]
        Kafka[Apache Kafka / RabbitMQ]
    end

    subgraph Events["Domain Events"]
        UserCreated["👤 UserCreated"]
        ProductUpdated["📦 ProductUpdated"]
        CartUpdated["🛒 CartUpdated"]
        OrderCreated["📋 OrderCreated"]
        PaymentProcessed["💳 PaymentProcessed"]
        OrderConfirmed["✅ OrderConfirmed"]
        InventoryReserved["🔒 InventoryReserved"]
        ShippingInitiated["🚚 ShippingInitiated"]
        EmailSent["📧 EmailSent"]
    end

    subgraph Data["Data Storage"]
        UserDB[(User DB)]
        ProductDB[(Product DB)]
        OrderDB[(Order DB)]
        Cache[(Redis Cache)]
        SearchIndex[(Elasticsearch)]
    end

    subgraph Background["Background Services"]
        Analytics[Analytics Engine]
        Reporting[Reporting Service]
        Audit[Audit Logger]
    end

    Client --> Gateway
    
    Gateway --> User
    Gateway --> Product
    Gateway --> Cart
    Gateway --> Order
    Gateway --> Payment

    User --> Kafka
    Product --> Kafka
    Cart --> Kafka
    Order --> Kafka
    Payment --> Kafka
    Inventory --> Kafka
    Shipping --> Kafka
    Notification --> Kafka

    Kafka --> UserCreated
    Kafka --> ProductUpdated
    Kafka --> CartUpdated
    Kafka --> OrderCreated
    Kafka --> PaymentProcessed
    Kafka --> OrderConfirmed
    Kafka --> InventoryReserved
    Kafka --> ShippingInitiated
    Kafka --> EmailSent

    UserCreated --> User
    ProductUpdated --> Product
    CartUpdated --> Cart
    OrderCreated --> Order
    PaymentProcessed --> Payment
    OrderConfirmed --> Notification
    InventoryReserved --> Inventory
    ShippingInitiated --> Shipping
    EmailSent --> Notification

    User --> UserDB
    Product --> ProductDB
    Order --> OrderDB
    Product --> Cache
    Product --> SearchIndex
    Inventory --> OrderDB

    UserCreated --> Analytics
    OrderCreated --> Analytics
    PaymentProcessed --> Analytics
    OrderConfirmed --> Reporting
    PaymentProcessed --> Audit
    OrderCreated --> Audit
```

## Event Flow Example: Order Placement

1. **OrderCreated** - Customer places order via API
2. **PaymentProcessed** - Payment Service confirms payment
3. **InventoryReserved** - Inventory Service reserves stock
4. **OrderConfirmed** - Order Service confirms order
5. **ShippingInitiated** - Shipping Service picks and packs
6. **EmailSent** - Notification Service sends confirmation email
7. **Analytics** - Analytics Engine tracks conversion

## Benefits

- **Decoupling**: Services don't directly call each other
- **Scalability**: Each service can scale independently
- **Resilience**: Services can fail without cascading failures
- **Auditability**: Complete event log for compliance
- **Real-time Updates**: Clients get notifications via WebSockets/polling
