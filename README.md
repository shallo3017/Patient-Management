# 🏥 Patient Management System

A production-ready, microservices-based **Patient Management System** built with **Java Spring Boot**. The system is designed for scalability, maintainability, and independent deployability of each service.

## 📐 Architecture Overview

                        ┌─────────────────┐
                        │   API Gateway   │
                        │   (Port: 4004)  │
                        └────────┬────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
     ┌────────▼───────┐ ┌────────▼───────┐ ┌───────▼────────┐
     │  Auth Service  │ │Patient Service │ │Billing Service │
     │  (Port: 8081)  │ │  (Port: 8082)  │ │  (Port: 8083)  │
     └────────────────┘ └───────┬────────┘ └───────▲────────┘
                                │  gRPC             │
                                └───────────────────┘
                                         │
                                  Kafka Events
                                         │
                              ┌──────────▼─────────┐
                              │ Analytics Service  │
                              │   (Port: 8085)     │
                              └────────────────────┘

## 🧩 Services

### 1. `api-gateway`
The single entry point for all client requests. Routes traffic to the appropriate microservice and handles cross-cutting concerns like authentication validation.

- **Technology:** Spring Cloud Gateway
- **Responsibilities:** Request routing, JWT validation, rate limiting

### 2. `auth-service`
Handles user registration, login, and JWT token generation/validation.

- **Technology:** Spring Boot, Spring Security, JWT
- **Endpoints:**
  - `POST /auth/register` — Register a new user
  - `POST /auth/login` — Authenticate and receive a JWT token
  - `GET /auth/validate` — Validate an existing JWT token

### 3. `patient-service`
Core service for managing patient records. Publishes events to Kafka and communicates with billing-service via gRPC.

- **Technology:** Spring Boot, PostgreSQL, Kafka, gRPC
- **Endpoints:**
  - `GET /api/patients` — List all patients
  - `GET /api/patients/{id}` — Get patient by ID
  - `POST /api/patients` — Create a new patient
  - `PUT /api/patients/{id}` — Update patient details
  - `DELETE /api/patients/{id}` — Delete a patient

### 4. `billing-service`
Manages billing accounts and invoice generation. Exposes a gRPC server for inter-service communication.

- **Technology:** Spring Boot, gRPC, PostgreSQL
- **Responsibilities:** Billing account creation, invoice management, payment tracking

### 5. `analytics-service`
Consumes Kafka events from other services and processes data to generate insights and reports.

- **Technology:** Spring Boot, Apache Kafka
- **Responsibilities:** Patient registration trends, billing analytics, event-driven reporting

### 6. `integration-tests`
End-to-end integration tests validating communication between services.

- **Technology:** JUnit 5, REST Assured, Spring Boot Test

---

## 🛠️ Tech Stack

| Category | Technology |
|---|---|
| Language | Java 17 |
| Framework | Spring Boot |
| API Gateway | Spring Cloud Gateway |
| Authentication | Spring Security + JWT |
| Databases | PostgreSQL (prod), H2 (tests) |
| Messaging | Apache Kafka |
| Inter-service RPC | gRPC + Protocol Buffers |
| Containerization | Docker |
| API Documentation | OpenAPI / Swagger |
| Testing | JUnit 5, REST Assured |

---

## 🚀 Getting Started

### Prerequisites

- Java 17+
- Maven 3.8+
- Docker & Docker Compose
- PostgreSQL

### Clone the Repository

```bash
git clone https://github.com/shallo3017/Patient-Management.git
cd Patient-Management
```

### Run with Docker Compose

```bash
docker-compose up --build
```

### Run a Service Individually

```bash
cd patient-service
mvn clean package
mvn spring-boot:run
```


## 📖 API Documentation

Each service exposes its own Swagger UI for interactive API exploration.

| Service | Swagger URL |
|---|---|
| Patient Service | `http://localhost:8082/swagger-ui.html` |
| Auth Service | `http://localhost:8081/swagger-ui.html` |
| Billing Service | `http://localhost:8083/swagger-ui.html` |
| Analytics Service | `http://localhost:8085/swagger-ui.html` |


## 🔐 Authentication Flow

Client → POST /auth/login → Auth Service → JWT Token
Client → Request + JWT → API Gateway → Validates Token → Routes to Service
```

All protected endpoints require a valid JWT token in the `Authorization` header:

```
Authorization: Bearer <your_token>
```


## 📡 Inter-Service Communication

### gRPC (Synchronous)
Used between **Patient Service** and **Billing Service** for real-time billing account lookups when a patient is created.

### Apache Kafka (Asynchronous)
Used for event-driven communication:

| Event | Producer | Consumer |
|---|---|---|
| `PatientCreated` | Patient Service | Billing Service, Analytics Service |
| `PatientUpdated` | Patient Service | Analytics Service |


## 🧪 Running Tests

### Unit Tests
```bash
mvn test
```

### Integration Tests
```bash
cd integration-tests
mvn test


## 📁 Project Structure

```
Patient-Management/
├── api-gateway/          # Spring Cloud Gateway
├── auth-service/         # JWT Authentication
├── patient-service/      # Patient CRUD + Kafka producer
├── billing-service/      # Billing + gRPC server
├── analytics-service/    # Kafka consumer + reporting
├── integration-tests/    # End-to-end tests
└── docker-compose.yml    # Local orchestration


## 📄 License

This project is open source and available under the [MIT License](LICENSE).
