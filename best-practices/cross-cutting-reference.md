# Cross-Cutting Concerns - Detailed Patterns & Examples

## Centralized Error Handling

### Error Package Structure
```
errors/
├── errors.go / errors.ts / exceptions.py   # Custom error types
├── codes.go / codes.ts / codes.py          # Error code constants
└── handler.go / handler.ts / handler.py    # Global error handler middleware
```

### Custom Error Type Pattern

```go
// Go example
type AppError struct {
    Code       string `json:"code"`
    Message    string `json:"message"`
    HTTPStatus int    `json:"-"`
    Internal   error  `json:"-"`
}

func (e *AppError) Error() string { return e.Message }
```

```typescript
// TypeScript example
export class AppError extends Error {
  constructor(
    public code: string,
    public message: string,
    public httpStatus: number,
    public internal?: Error,
  ) {
    super(message);
  }
}
```

```python
# Python example
class AppError(Exception):
    def __init__(self, code: str, message: str, http_status: int, internal: Exception = None):
        self.code = code
        self.message = message
        self.http_status = http_status
        self.internal = internal
        super().__init__(message)
```

### Domain Error Constants

```
ErrNotFound        = AppError("NOT_FOUND", "Resource not found", 404)
ErrValidation      = AppError("VALIDATION_ERROR", "Invalid input", 400)
ErrUnauthorized    = AppError("UNAUTHORIZED", "Authentication required", 401)
ErrForbidden       = AppError("FORBIDDEN", "Insufficient permissions", 403)
ErrConflict        = AppError("CONFLICT", "Resource already exists", 409)
ErrInternal        = AppError("INTERNAL_ERROR", "An unexpected error occurred", 500)
ErrServiceDown     = AppError("SERVICE_UNAVAILABLE", "Service temporarily unavailable", 503)
```

### Consistent Error Response Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable message for the client",
    "details": [
      {"field": "email", "message": "Invalid email format"},
      {"field": "age", "message": "Must be at least 18"}
    ],
    "request_id": "req-550e8400-e29b-41d4-a716-446655440000",
    "timestamp": "2025-01-01T00:00:00Z"
  }
}
```

---

## Centralized Structured Logging & Observability

### Logger Package Structure
```
logger/
├── logger.go / logger.ts / logger.py            # Logger init and config
├── context.go / context.ts / context.py          # Context-aware logging
├── middleware.go / middleware.ts / middleware.py   # Request logging middleware
└── tracing.go / tracing.ts / tracing.py          # OpenTelemetry setup
```

### Required Log Fields

Every log entry MUST include:
```json
{
  "timestamp": "2025-01-01T12:00:00.000Z",
  "level": "INFO",
  "service_name": "user-service",
  "correlation_id": "req-550e8400-e29b-41d4-a716-446655440000",
  "trace_id": "4bf92f3577b34da6a3ce929d0e0e4736",
  "span_id": "00f067aa0ba902b7",
  "method": "POST",
  "path": "/api/v1/users",
  "duration_ms": 42,
  "user_id": "usr-123",
  "message": "User created successfully",
  "data": {
    "user_email": "j***@example.com"
  }
}
```

### Log Level Guidelines

| Level | Use For | Example |
|-------|---------|---------|
| DEBUG | Development details, variable values | `"Parsing request body"` |
| INFO | Significant business events | `"Order #123 placed by user #456"` |
| WARN | Recoverable issues, degradation | `"Retry 2/3 succeeded for payment gateway"` |
| ERROR | Failures requiring attention | `"Failed to connect to database after 3 retries"` |
| FATAL | System cannot continue | `"Required config DATABASE_URL not set, shutting down"` |

### Observability: Three Pillars

**Logs** - Structured JSON with correlation IDs (described above)

**Traces** - Use OpenTelemetry SDK for distributed tracing:
- Instrument at service entry points (HTTP handlers, gRPC interceptors, message consumers)
- Propagate `trace_id` and `span_id` across service boundaries via headers (`traceparent` W3C standard)
- Create child spans for significant operations (DB queries, external API calls, message publishing)
- Sample intelligently: 100% for errors, 1-10% for successful requests in high-traffic services

**Metrics** - Expose in Prometheus format (see Health Checks section below)

### Correlation & Trace ID Flow

```
Client Request
    │
    ▼
API Gateway ──── generates X-Request-ID + starts root trace span
    │             traceparent: 00-{trace_id}-{span_id}-01
    ▼
User Service ──── logs with correlation_id + trace_id + span_id
    │                creates child span, propagates via HTTP header
    ▼
Order Service ─── logs with same trace_id, new span_id
    │                propagates via message header (Kafka/RabbitMQ)
    ▼
Payment Service ── logs with same trace_id, new span_id
```

### NEVER Log These
- Passwords (plain or hashed)
- API keys and tokens
- Credit card numbers (mask to last 4 digits if needed)
- Social security numbers
- Personal health information (PHI)
- Full IP addresses (in GDPR regions)
- Session tokens
- JWT token contents

---

## Configuration Management

### Pattern
```
config/
├── config.go / config.ts / settings.py    # Config struct/class
├── .env.example                            # Template (committed to git)
├── .env                                    # Local overrides (gitignored)
└── configs/
    ├── development.yaml
    ├── staging.yaml
    └── production.yaml
```

### Config Loading Order (highest priority first)
1. Environment variables
2. `.env` file (local development only)
3. Config file for current environment
4. Default values in code

### Startup Validation
- Validate ALL required config exists at startup
- Validate formats (URLs, ports, durations)
- Fail fast with clear error message if anything is missing
- Log loaded config (redact secrets) at startup for debugging

---

## Health Checks

### Liveness Endpoint (`/health`)
Returns 200 if process is running. No dependency checks.
```json
{ "status": "ok", "service": "user-service", "version": "1.2.3" }
```

### Readiness Endpoint (`/ready`)
Returns 200 only if ALL dependencies are reachable.
```json
{
  "status": "ready",
  "checks": {
    "database": { "status": "up", "latency_ms": 2 },
    "redis": { "status": "up", "latency_ms": 1 },
    "kafka": { "status": "up", "latency_ms": 5 }
  }
}
```

### Metrics Endpoint (`/metrics`)
Expose in Prometheus format:
- `http_requests_total{method, path, status}` - Counter
- `http_request_duration_seconds{method, path}` - Histogram
- `active_connections` - Gauge
- `business_orders_total{status}` - Counter (business metrics)
- `db_pool_active_connections` - Gauge
- `db_pool_idle_connections` - Gauge
- `external_api_request_duration_seconds{service}` - Histogram
