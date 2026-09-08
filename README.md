# CareerConnect

# LinkedIn Project Microservices

A Spring Boot microservices-based project inspired by LinkedIn functionality, built with Java 21, Spring Cloud, Kafka, PostgreSQL, and Kubernetes deployment manifests. The system is divided into independent services for user management, posts, notifications, connections, and file upload, with an API Gateway and Eureka discovery service in front.

## Architecture Overview

The application follows a service-oriented architecture:

- API Gateway: routes incoming requests and validates JWT token access for protected services
- Discover Server: service discovery via Spring Eureka
- User Service: sign up, login, JWT issuance, and user identity management
- Posts Service: create and fetch posts, with image/file handling
- Connections Service: manage first-degree connections and connection requests
- Notification Service: listens to events and stores notifications
- Uploader Service: handles uploaded files
- Kubernetes manifests: deployment configuration for the services and databases

High-level flow:

- Clients call the gateway at port 8080
- The gateway forwards requests to the required microservice
- Eureka registers all services and enables service discovery
- Kafka handles asynchronous events between services
- PostgreSQL stores service-specific data

## Repository Structure

```text
linkedInProject/
├── APIGateway/
├── ConnectionsService/
├── DiscoverServer/
├── notification-service/
├── postsService/
├── uploader-service/
├── userService/
├── k8s/
├── README.md
└── .git/
```

## Services

### 1. API Gateway
Location: `APIGateway/`

- Runs on port `8080`
- Uses Spring Cloud Gateway
- Exposes endpoint prefixes:
  - `/api/v1/users/**`
  - `/api/v1/posts/**`
  - `/api/v1/connections/**`
- Applies JWT-based authentication for protected routes

### 2. Discover Server
Location: `DiscoverServer/`

- Runs on port `8761`
- Uses Spring Eureka Server
- Enables service discovery for all microservices

### 3. User Service
Location: `userService/`

- Runs on port `9020`
- Handles:
  - user registration
  - login
  - JWT generation
  - user authentication
- Uses PostgreSQL and Kafka

### 4. Posts Service
Location: `postsService/`

- Runs on port `9010`
- Handles:
  - creating posts
  - fetching posts
  - listing posts by user
  - file-based uploads for post content
- Integrates with OpenFeign and Kafka

### 5. Connections Service
Location: `ConnectionsService/`

- Runs on port `9030`
- Handles:
  - first-degree connections
  - sending connection requests
  - accepting/rejecting requests

### 6. Notification Service
Location: `notification-service/`

- Runs on port `9040`
- Stores notifications in PostgreSQL
- Consumes Kafka events and processes user notifications

### 7. Uploader Service
Location: `uploader-service/`

- Runs on port `9050`
- Used for uploading files/assets

## Tech Stack

- Java 21
- Spring Boot 3.x
- Spring Cloud Gateway
- Spring Cloud Netflix Eureka
- Spring Data JPA
- PostgreSQL
- Kafka
- Maven
- Docker / Jib
- Kubernetes manifests under `k8s/`
- JWT with JJWT
- Lombok

## Prerequisites

Before running the project locally, ensure these are installed:

- Java 21+
- Maven 3.9+
- PostgreSQL
- Kafka
- Docker (optional, for container builds)
- Kubernetes tools (optional, for `k8s/` deployments)

## Local Development Setup

### 1. Start the Discovery Server

From the project root:

```bash
cd DiscoverServer
./mvnw spring-boot:run
```

The server will run at:

- http://localhost:8761

### 2. Start the Required Databases and Kafka

The services are configured to use PostgreSQL databases named such as:

- `userDB`
- `postsDB`
- `notificationDB`

Create the databases locally, then update credentials in each service's `application.properties` if needed.

Kafka should also be running locally so services can publish/consume events.

### 3. Start the Microservices

Run each service in a separate terminal:

```bash
cd userService
./mvnw spring-boot:run
```

```bash
cd postsService
./mvnw spring-boot:run
```

```bash
cd ConnectionsService
./mvnw spring-boot:run
```

```bash
cd notification-service
./mvnw spring-boot:run
```

```bash
cd uploader-service
./mvnw spring-boot:run
```

```bash
cd APIGateway
./mvnw spring-boot:run
```

### 4. Access the API

The public gateway is available at:

- http://localhost:8080

## API Route Examples

The gateway strips the prefix and forwards traffic to the downstream services.

### User APIs

- `POST /api/v1/users/auth/signup`
- `POST /api/v1/users/auth/login`

### Post APIs

- `POST /api/v1/posts/core`
- `GET /api/v1/posts/core/{postId}`
- `GET /api/v1/posts/core/users/{userId}/allPosts`

### Connections APIs

- `GET /api/v1/connections/core/{userId}/first-degree`
- `POST /api/v1/connections/core/request/{userId}`
- `POST /api/v1/connections/core/accept/{userId}`
- `POST /api/v1/connections/core/reject/{userId}`

> The actual route names depend on the gateway configuration and may be shifted slightly when running in a different environment.

## Security

The gateway and user service use JWT-based authentication.

- User registration and login are exposed through the user service
- Protected routes are filtered in the API Gateway using the `AuthenticationFilter`
- Tokens are validated before allowing access to protected endpoints

## Kubernetes Deployment

The `k8s/` directory contains deployment YAML files for:

- API gateway
- Connections DB
- Connections service
- Kafka
- Notification DB
- Notification service
- Posts DB
- Posts service
- Uploader service
- User DB
- User service
- Ingress

These manifests can be used to deploy the platform to a Kubernetes cluster.

## Useful Commands

Run a service with Maven:

```bash
./mvnw clean package
./mvnw spring-boot:run
```

Build a Docker image with Jib (used by the services):

```bash
./mvnw clean package
```

## Notes

This project is a backend-focused LinkedIn-style app and is structured as a learning or sample microservices project. It demonstrates:

- service decomposition
- API gateway routing
- service discovery
- event-driven communication via Kafka
- persistence separation by service
- cloud-native deployment patterns

## Future Improvements

Possible enhancements include:

- adding API documentation with Swagger/OpenAPI
- improving notification delivery and email integration
- adding unit/integration tests for each service
- adding Docker Compose for local orchestration
- introducing distributed tracing and monitoring

## License

This project does not include a license file yet. If you plan to share or publish it publicly, add an appropriate open-source license.
