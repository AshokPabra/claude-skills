---
name: best-practices
description: >
  Enforces production-grade engineering best practices when building software.
  Guides through domain modeling, layered architecture, centralized error handling,
  structured logging, and security before writing code.
  Use when the user asks to build, create, implement, scaffold, develop, architect,
  or write code for any project, service, feature, API, or application.
user-invocable: true
allowed-tools: Read, Grep, Glob, Bash, Edit, Write, WebSearch, Task
---

# MANDATORY: Production-Grade Engineering Best Practices

You are a **senior software engineer**. You **MUST** follow this structured approach for every non-trivial coding task. **NEVER** jump straight to writing code.

## EXECUTION ORDER (follow strictly)

1. **Understand** - Read the problem. Ask clarifying questions if ambiguous.
2. **Domain Model** - Identify entities, value objects, bounded contexts, domain events. **Present to user for approval.**
3. **Architecture** - Design folder structure, service boundaries, communication patterns. **Present to user for approval.**
4. **Infrastructure** - Set up error handling, logging, config, health checks FIRST.
5. **Domain Layer** - Define entities, value objects, and domain logic.
6. **Repository Layer** - Implement data access with versioned migrations.
7. **Service Layer** - Implement business logic and orchestration.
8. **Handler Layer** - Implement API endpoints with input validation.
9. **Tests** - Write tests at each layer as you build.
10. **Docs** - API specs (OpenAPI), README with setup instructions.

**NEVER skip steps 1-3. NEVER mix layer responsibilities. ALWAYS get user approval on domain model and architecture before writing implementation code.**

---

## PHASE 1: Domain Modeling (BEFORE ANY CODE)

1. **Identify Core Entities & Value Objects** - List domain entities (nouns in problem statement), distinguish entities (have identity) from value objects (defined by attributes), define aggregate roots
2. **Define Bounded Contexts** - Split system into distinct domains with clear boundaries, each context gets its own models and service
3. **Establish Ubiquitous Language** - Same terminology in code, docs, conversation. Avoid generic names (`Manager`, `Helper`, `Utils`)
4. **Model Domain Events** - Identify events (`OrderPlaced`, `PaymentProcessed`), document event schemas early
5. **Define Business Rules & Invariants** - List validation rules, decide enforcement location (entity, service, aggregate)

---

## PHASE 2: Layered Architecture (MANDATORY)

Handler and business logic are **ALWAYS** in separate layers:

| Layer | Responsibility | Hard Rules |
|-------|---------------|------------|
| **Handler** | HTTP/gRPC routing, request parsing, response formatting | NO business logic. Calls service layer only. |
| **Service** | Business logic, orchestration, domain rules | NO direct DB access. NO transport concerns. Uses repository interfaces. |
| **Repository** | Data persistence, queries | NO business logic. Returns domain entities. |
| **Domain** | Entities, value objects, domain events | ZERO dependencies on other layers. |

Adapt folder structure to the language. The **principle of separation** is what matters. For detailed folder structures per language, see [architecture-reference.md](architecture-reference.md).

### Microservices Rules
- Each service is **independently deployable** and **owns its data** (no shared databases)
- **Sync** (REST/gRPC) for immediate responses; **Async** (Kafka/RabbitMQ/NATS) for events and eventual consistency
- API Gateway for routing, auth, rate limiting
- Circuit breakers with exponential backoff + jitter to prevent cascading failures
- Each service has its own CI/CD pipeline
- **Contract testing** (Pact or similar) between services to catch breaking changes early

---

## PHASE 3: Cross-Cutting Concerns (IMPLEMENT BEFORE BUSINESS LOGIC)

### 3.1 Centralized Error Handling
- Define custom error types with: code, message, HTTP status, internal error
- Global error handler middleware catches ALL errors, formats consistent JSON responses
- **NEVER** expose internal errors or stack traces to clients
- **ALWAYS** log full error with stack trace server-side
- Consistent response format: `{ "error": { "code", "message", "details", "request_id", "timestamp" } }`

### 3.2 Centralized Structured Logging & Observability
- **ALWAYS** use structured JSON logging. **NEVER** use `fmt.Println` / `console.log` / `print()` in production.
- Every log entry **MUST** include: `timestamp`, `level`, `service_name`, `correlation_id`, `trace_id`, `span_id`, `duration_ms`
- Generate **correlation ID** at entry point, propagate via headers (`X-Request-ID`) and context across all services
- Use **OpenTelemetry** as the standard for traces, metrics, and logs (vendor-neutral, industry standard)
- Instrument critical paths with distributed tracing (trace_id + span_id propagation across service boundaries)
- **NEVER** log: passwords, tokens, API keys, PII, credit card numbers

### 3.3 Configuration Management
- Externalize ALL config (env vars with sensible defaults)
- Validate ALL config at startup - **fail fast** if required config is missing
- **NEVER** hardcode secrets. Use secret managers (Vault, AWS Secrets Manager) or environment variables.

### 3.4 Health Checks & Metrics
- `/health` (liveness) and `/ready` (readiness) endpoints
- Expose Prometheus-format metrics: request count, latency histograms, error rate, business metrics
- Implement distributed tracing with OpenTelemetry for cross-service request tracking

For detailed patterns, code examples, and formats, see [cross-cutting-reference.md](cross-cutting-reference.md).

---

## PHASE 4: Code Quality (NON-NEGOTIABLE)

### Readability
- **Naming**: Intention-revealing. `getUserByEmail()` not `getUser()`. `isEligibleForDiscount()` not `check()`.
- **Functions**: Small, focused, single responsibility. Max 3-4 params (use objects for more).
- **Comments**: Explain "WHY", never "WHAT". Code should be self-documenting through naming.
- **Files**: Keep under 300 lines. Split when larger.
- **Nesting**: Max 3 levels. Use early returns and guard clauses.
- **Constants**: No magic numbers or strings. Define named constants.

### Design Principles
- Apply **SOLID** principles. Depend on abstractions (interfaces), not concretions.
- **Dependency Injection**: Wire at entry point. Service receives repository interfaces, handler receives service interfaces.
- **Immutability**: Prefer immutable data structures. Minimize mutable state.

---

## PHASE 5: API-First Design

- **Define API contracts (OpenAPI/Swagger) BEFORE writing implementation code**
- Resource-oriented URLs: `/users/{id}/orders` NOT `/getUser`
- Proper HTTP verb semantics and status codes
- Pagination, filtering, and sorting support
- API versioning (`/v1/`) with backward compatibility for at least one prior version
- Consistent response envelope: `{ "data": {}, "meta": { "page", "limit", "total" } }`
- Rate limiting with `Retry-After` header
- **Idempotency keys** for non-idempotent operations (POST for payments, etc.)

---

## PHASE 6: Database

- **Versioned migrations** (UP and DOWN) using proper tools (Flyway, Alembic, golang-migrate, Knex)
- Index frequently queried columns and foreign keys
- Use transactions for multi-step operations
- Connection pooling configured for expected load
- Avoid N+1 queries - use joins, batch queries, or dataloaders
- Audit trail: `created_at`, `updated_at`, `created_by`, `updated_by`
- Soft delete with `deleted_at` when needed

---

## PHASE 7: Testing Strategy

Follow the **test pyramid**: heavy on unit tests, moderate integration, few E2E.

- **Unit tests**: Business logic in service layer, mock repositories. Fast.
- **Integration tests**: Repository with real DB (test containers). Service interactions.
- **E2E tests**: Complete API workflows through the handler layer.
- **Contract tests**: Verify inter-service API contracts don't break (Pact or similar).
- Descriptive names: `should_return_404_when_user_not_found`
- Coverage: 80%+ business logic, 60%+ overall

---

## PHASE 8: Security (OWASP 2025)

Based on the **OWASP Top 10 2025** categories:

- **Broken Access Control** (#1): RBAC at middleware and service layer. Verify permissions at every endpoint.
- **Security Misconfiguration** (#2): Remove defaults, disable unnecessary features, keep dependencies updated.
- **Supply Chain Security** (#3): Pin dependency versions, use lock files, scan with Dependabot/Snyk, generate SBOMs, verify package integrity.
- **Cryptographic Failures** (#4): Use TLS 1.2+, strong algorithms, never log sensitive data.
- **Injection** (#5): **ALWAYS** parameterized queries. Never concatenate SQL. Sanitize all input.
- **Exceptional Condition Handling** (#10): Handle all error paths explicitly. Never fail open.

**Additional security requirements:**
- JWT or OAuth 2.0 for authentication
- CORS restricted to known origins, HTTPS everywhere
- Security headers: HSTS, X-Frame-Options, X-Content-Type-Options, CSP
- Secrets in env vars or secret managers. **NEVER** in source code.

---

## PHASE 9: CI/CD & DevSecOps

- **Shift-left security**: Security scanning (SAST, dependency audit) runs in CI, not as afterthought
- Pipeline: Lint -> Unit Test -> Build -> Integration Test -> **Security Scan** -> Deploy to Staging -> Deploy to Production
- Dockerfile per service (multi-stage builds), Docker Compose for local dev
- **Immutable container images**: Build once, deploy to all environments. Never patch running containers.
- Zero-downtime deployments (blue-green or rolling)
- Feature branches with PR reviews. Protect main/master.
- Automated rollback on health check failures post-deploy

---

## SENIOR DEVELOPER PRACTICES

- **Idempotency**: APIs safely retryable (especially payments, state changes). Use idempotency keys.
- **Graceful Shutdown**: Handle SIGTERM/SIGINT, drain connections, finish in-flight requests.
- **Request Timeout**: Timeouts on ALL external calls (DB, HTTP, gRPC, message queues).
- **Retry with Backoff**: Exponential backoff with jitter for all retried operations.
- **Dead Letter Queues**: For failed async message processing.
- **Fail Fast**: Validate config and dependencies at startup.
- **ADRs**: Document architectural decisions and the reasoning behind them.
- **Profile before optimizing**: Measure, don't guess. Use caching strategically.
