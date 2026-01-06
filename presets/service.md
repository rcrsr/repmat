# Service

Preset for backend services and APIs. Auto-detects API style and framework.

## Detection Signals

Identify API style from:
- REST: Route decorators, HTTP method handlers, path parameters
- GraphQL: Schema definitions (`.graphql`), resolvers, type definitions
- gRPC: Proto files (`.proto`), generated stubs, service definitions
- WebSocket: Connection handlers, event emitters

Identify framework from:
- Python: FastAPI, Flask, Django, aiohttp
- Node/TS: Express, Fastify, NestJS, Hono
- Go: Gin, Echo, Chi, Fiber
- Rust: Actix, Axum, Rocket

## Search Keywords

Based on detected API style:
- "[api-style] API design best practices [year]"
- "[api-style] error handling patterns"
- "[api-style] authentication patterns"

Based on detected framework:
- "[framework] project structure"
- "[framework] middleware patterns"
- "[framework] dependency injection"

General service patterns:
- "microservice patterns"
- "API versioning strategies"
- "rate limiting patterns"

## Investigation Focus

When analyzing local service code, examine:
- **Route organization** — How are endpoints grouped? By resource? By version?
- **Request/response patterns** — Validation, serialization, DTOs
- **Authentication/authorization** — How is auth handled? Middleware? Decorators?
- **Error handling** — Standard error responses? Error codes? Logging?
- **Database access** — ORM patterns, repository pattern, connection management
- **Middleware stack** — What middleware is used? In what order?
- **Background tasks** — Queue usage, async workers, scheduled jobs
- **Health endpoints** — `/health`, `/ready`, `/live` patterns
- **API documentation** — OpenAPI/Swagger specs, inline documentation
- **Message queues** — Kafka, RabbitMQ, SQS integration patterns

## Policy Considerations

Common sections for service policies:
- API design conventions (paths, methods, status codes)
- Request/response format
- Authentication/authorization patterns
- Error response format
- Logging and observability
- Database access patterns
- Testing (unit, integration, e2e)

## CI/CD Detection

Look for pipeline configurations:
- GitHub Actions: `.github/workflows/*.yml`
- GitLab CI: `.gitlab-ci.yml`
- Dockerfile: `Dockerfile`, `docker-compose.yml`
- Kubernetes: `k8s/`, `*.yaml` with `kind: Deployment`

## Security Investigation

Examine security-related patterns:
- **Authentication** — JWT, OAuth, API keys, session management
- **Authorization** — RBAC, ABAC, permission middleware
- **Input validation** — Schema validation, sanitization
- **Secrets management** — Vault, AWS Secrets Manager, environment variables
- **Rate limiting** — Throttling configuration, abuse prevention

## Observability

Look for instrumentation patterns:
- **Logging** — Structured logging, log levels, correlation IDs
- **Metrics** — Prometheus, StatsD, custom metrics
- **Tracing** — OpenTelemetry, Jaeger, Zipkin integration
- **Error tracking** — Sentry, Rollbar, Bugsnag

## Defaults

- **meta_type**: stateless
- **author_role**: engineer
- **typical_patterns**: `**/api/**/*`, `**/routes/**/*`, `**/services/**/*`, `**/handlers/**/*`
