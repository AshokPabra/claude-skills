# Architecture Reference - Folder Structures by Language

## Go / Golang

```
project-root/
├── cmd/                        # Application entry points
│   └── server/
│       └── main.go             # Bootstrap, DI, server startup
├── internal/
│   ├── handler/                # LAYER 1: Transport/Handler Layer
│   │   ├── http/               # HTTP handlers (REST controllers)
│   │   ├── grpc/               # gRPC handlers
│   │   └── middleware/         # Auth, logging, rate-limit, CORS
│   ├── service/                # LAYER 2: Business/Service Layer
│   │   ├── user_service.go     # Business logic, orchestration
│   │   └── order_service.go    # Domain rules, validations
│   ├── repository/             # LAYER 3: Data Access Layer
│   │   ├── user_repo.go        # Database operations
│   │   └── interfaces.go       # Repository interfaces
│   ├── domain/                 # LAYER 0: Domain Models
│   │   ├── entities/           # Core business entities
│   │   ├── valueobjects/       # Value objects
│   │   └── events/             # Domain events
│   └── dto/                    # Data transfer objects
├── pkg/                        # Shared libraries (exported)
│   ├── errors/                 # Centralized error definitions
│   ├── logger/                 # Centralized logging
│   ├── config/                 # Configuration management
│   └── middleware/             # Shared middleware
├── migrations/                 # Database migrations (versioned)
├── configs/                    # Config files per environment
├── api/                        # API specs (OpenAPI/protobuf)
├── scripts/                    # Build, deploy, seed scripts
├── tests/                      # Integration & E2E tests
├── docker/                     # Dockerfiles per service
└── docker-compose.yml          # Local development environment
```

## Python (FastAPI / Django)

```
project-root/
├── src/
│   ├── main.py                 # Application entry point
│   ├── config/                 # Settings, env loading
│   │   ├── settings.py
│   │   └── __init__.py
│   ├── api/                    # LAYER 1: Handler Layer
│   │   ├── routes/             # Route definitions
│   │   ├── middleware/         # Auth, logging, CORS
│   │   ├── dependencies.py    # Dependency injection
│   │   └── schemas/           # Request/response models (Pydantic)
│   ├── services/               # LAYER 2: Business Layer
│   │   ├── user_service.py
│   │   └── order_service.py
│   ├── repositories/           # LAYER 3: Data Access Layer
│   │   ├── user_repo.py
│   │   └── interfaces.py
│   ├── domain/                 # LAYER 0: Domain Models
│   │   ├── entities/
│   │   ├── value_objects/
│   │   └── events/
│   └── shared/                 # Cross-cutting concerns
│       ├── errors/
│       ├── logger/
│       └── utils/
├── migrations/                 # Alembic migrations
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docker/
├── docker-compose.yml
└── pyproject.toml
```

## Node.js / TypeScript (Express / NestJS)

```
project-root/
├── src/
│   ├── index.ts                # Entry point
│   ├── config/                 # Configuration
│   ├── modules/                # Feature modules
│   │   ├── user/
│   │   │   ├── user.controller.ts   # LAYER 1: Handler
│   │   │   ├── user.service.ts      # LAYER 2: Business
│   │   │   ├── user.repository.ts   # LAYER 3: Data Access
│   │   │   ├── user.entity.ts       # Domain Model
│   │   │   ├── user.dto.ts          # DTOs
│   │   │   └── user.module.ts       # Module wiring
│   │   └── order/
│   │       └── ...
│   ├── shared/                 # Cross-cutting
│   │   ├── errors/
│   │   ├── logger/
│   │   ├── middleware/
│   │   └── guards/
│   └── common/                 # Shared types, interfaces
├── migrations/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docker/
├── docker-compose.yml
├── package.json
└── tsconfig.json
```

## Java / Spring Boot

```
project-root/
├── src/main/java/com/company/project/
│   ├── Application.java             # Entry point
│   ├── config/                      # Configuration beans
│   ├── controller/                  # LAYER 1: Handler Layer
│   │   ├── UserController.java
│   │   └── advice/                  # Global error handler
│   ├── service/                     # LAYER 2: Business Layer
│   │   ├── UserService.java
│   │   └── impl/
│   │       └── UserServiceImpl.java
│   ├── repository/                  # LAYER 3: Data Access Layer
│   │   └── UserRepository.java
│   ├── domain/                      # LAYER 0: Domain Models
│   │   ├── entity/
│   │   ├── valueobject/
│   │   └── event/
│   ├── dto/                         # Request/response DTOs
│   ├── exception/                   # Custom exceptions
│   ├── filter/                      # Servlet filters (middleware)
│   └── util/                        # Shared utilities
├── src/main/resources/
│   ├── application.yml
│   └── db/migration/               # Flyway migrations
├── src/test/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docker/
├── docker-compose.yml
└── pom.xml / build.gradle
```

## Microservices Layout (Multi-Service)

```
project-root/
├── services/
│   ├── user-service/               # Independent service
│   │   ├── cmd/ or src/
│   │   ├── internal/ or src/
│   │   ├── migrations/
│   │   ├── Dockerfile
│   │   └── go.mod / package.json / pom.xml
│   ├── order-service/              # Independent service
│   │   └── ... (same structure)
│   ├── payment-service/
│   │   └── ...
│   └── notification-service/
│       └── ...
├── gateway/                        # API Gateway
│   └── ...
├── shared/                         # Shared proto files, contracts
│   ├── proto/                      # gRPC definitions
│   └── events/                     # Event schemas
├── infra/                          # Infrastructure as Code
│   ├── terraform/
│   ├── k8s/
│   └── docker-compose.yml
└── docs/                           # Architecture docs, ADRs
    ├── adr/
    └── architecture.md
```
