# MicroserviceShop

![Architecture](img.png)

This repository follows a microservices reference architecture for an e-commerce domain on **.NET 8**, designed to run with **Docker**.

## High-level architecture

1. **Client App** calls a single entrypoint.
2. **API Gateway (YARP)** routes requests to the right backend microservice.
3. **Microservices** own their data and communicate via HTTP/gRPC and async messaging.
4. **Infrastructure services** (databases, cache, broker) run as containers.

## Components (from the diagram)

### Client Apps

- **Shopping.Web**
	- Web front-end that consumes the platform through the API Gateway.

### API Gateway

- **YARP API Gateway** (ASP.NET Core)
	- Single entrypoint for client traffic.
	- Routes/aggregates requests to downstream services.
	- Central place for concerns like auth, rate limiting, caching, resiliency policies, and header propagation.

### Microservices

Each service is independently deployable and owns its persistence.

- **Catalog**
	- Product browsing/search, categories, and catalog management.
	- **Database:** PostgreSQL.

- **Basket**
	- Shopping cart management.
	- **Database:** PostgreSQL.
	- **Cache:** Redis (distributed cache).
	- Publishes events (e.g., checkout initiated) to the message broker.

- **Discount**
	- Discount/coupon rules.
	- **Database:** SQLite.

- **Ordering**
	- Order creation, processing, and lifecycle.
	- **Database:** SQL Server.
	- Often consumes events from other services (e.g., basket checkout) via the broker.

### Messaging & integration

- **RabbitMQ**
	- Message broker used for asynchronous communication.

- **MassTransit**
	- Messaging abstraction used to publish/consume events over RabbitMQ.

- **gRPC**
	- Efficient service-to-service communication (often used for synchronous calls where needed).

### Data & ORM

- **Entity Framework Core**
	- Data access layer (primarily for relational stores).

## Tech stack

- .NET 8 / ASP.NET Core
- YARP (API Gateway)
- PostgreSQL, Redis, SQLite, SQL Server
- RabbitMQ + MassTransit
- gRPC
- Entity Framework Core
- Docker / Docker Compose

## Running locally (Docker)

Prerequisites:

- Docker Desktop
- (Optional) .NET SDK 8.x for running/debugging services without containers

Start everything with Compose:

```bash
docker compose up -d --build
```

Stop:

```bash
docker compose down
```

## Notes

- The diagram represents the target/reference architecture; depending on the current repo state, some services may be present as a subset.

