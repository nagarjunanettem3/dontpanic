
Q1. What is the structure of an HTTP request?

<METHOD> <PATH> <VERSION>
<Headers>
<Body>

- 	Why: Shows you understand the basics (method, URL, headers, body).
- 	How: In Go, you deal with http.Request which encapsulates all these parts.


Q2. What are HTTP headers and why are they important?
- 	Answer: Metadata that accompanies a request or response.
- 	Why: They control caching, authentication, content negotiation, etc.
- 	How: Examples:
- 	Authorization: Bearer <JWT> → auth.
- 	Content-Type: application/json → body format.
- 	Cache-Control: no-cache → caching rules.

⸻

Q3. Difference between HTTP/1.1, HTTP/2, and HTTP/3?
- 	Answer:
- 	HTTP/1.1: text-based, one request per connection (keep-alive helps).
- 	HTTP/2: binary protocol, multiplexed streams, header compression.
- 	HTTP/3: built on QUIC (UDP-based), better for mobile/unstable networks.
- 	Why: Shows awareness of modern web protocols.
- 	How: Go’s net/http natively supports HTTP/2.

⸻

🔹 2. REST API Design

Q4. What makes a good RESTful URL design?
- 	Answer: Use nouns, hierarchical structure, no verbs.
- 	Why: Makes APIs predictable and intuitive.
- 	How:
- 	✅ GET /users/123/orders/456
- 	❌ GET /getUserOrders?id=123&orderId=456

⸻

Q5. How do you design query params vs path params?
- 	Answer:
- 	Path → identify resource (/users/123).
- 	Query → filtering, sorting, pagination (/users?status=active&page=2).
- 	Why: Separation avoids confusion.
- 	How: REST convention = path = “what,” query = “how.”

⸻

Q6. How do you handle versioning in REST APIs?
- 	Answer:
- 	URI versioning → /api/v1/users (most common).
- 	Header-based → Accept: application/vnd.api.v1+json.
- 	Why: APIs evolve; versioning avoids breaking clients.
- 	How: URI versioning is simplest to maintain.

⸻

Q7. How do you handle pagination in REST APIs?
- 	Answer:
- 	Offset-based → /users?page=2&limit=20.
- 	Cursor-based → /users?after=user123&limit=20.
- 	Why: Prevents performance issues with large datasets.
- 	How: Cursor-based is better for real-time streams.

⸻

Q8. How do you design error responses in REST?
- 	Answer: Structured JSON with error code + message.
- 	Why: Clients need actionable feedback.

Q9. How do you secure REST APIs?
- 	Answer: HTTPS everywhere, JWT/OAuth2 for authentication, RBAC for authorization, input validation.
- 	Why: APIs are often exposed publicly.
- 	How: In Go → middleware for JWT validation; store secrets in ConfigMaps/Secrets in Kubernetes.

⸻

Q10. REST vs RESTful — what’s the difference?
- 	Answer: REST is the architectural principle; RESTful means strictly following it.
- 	Why: Many APIs misuse “REST” while using RPC-like design.
- 	How: RESTful → resource-based endpoints, proper HTTP verbs, stateless.

⸻

🔹 3. HTTP Methods & Status Codes

Q11. What’s idempotency and why is it important?
- 	Answer: Operation can be repeated without changing the result beyond the first call.
- 	Why: Makes retries safe.
- 	How: PUT /users/123 → multiple calls update same user, not duplicate them.

⸻

Q12. What are common status codes and their meanings?
- 	200 OK → success.
- 	201 Created → resource created.
- 	204 No Content → success, no body.
- 	400 Bad Request → invalid input.
- 	401 Unauthorized → no/invalid token.
- 	403 Forbidden → not allowed.
- 	404 Not Found → resource missing.
- 	409 Conflict → duplicate or versioning issue.
- 	429 Too Many Requests → rate limited.
- 	500 Internal Server Error → generic server failure.
- 	Why: Using correct status codes makes APIs easier to consume.

⸻

🔹 4. Middleware in Go

Q13. What is middleware and why do we use it?
- 	Answer: Function that runs before/after a request handler.
- 	Why: Centralizes cross-cutting concerns like logging, security, rate limiting.
- 	How: In Go → wrap http.Handler.

⸻

Q14. Common middleware in REST APIs?
- 	Logging → track requests & response times.
- 	Authentication → check JWT, API key.
- 	Authorization → RBAC/ACL enforcement.
- 	Rate limiting → prevent abuse.
- 	Request validation → sanitize inputs.

⸻

🔹 5. REST vs gRPC

Q15. REST vs gRPC — when do you choose one over the other?
- 	Answer: REST = human-readable, good for public APIs. gRPC = binary, faster, good for service-to-service.
- 	Why: Tradeoffs matter in architecture.
- 	How:
- 	REST → mobile apps, browsers.
- 	gRPC → internal microservices, low-latency APIs, streaming.

⸻

Q16. What’s the difference in payloads between REST and gRPC?
- 	Answer: REST → JSON (text-based, heavier). gRPC → Protobuf (binary, compact, strongly typed).
- 	Why: Impacts performance & compatibility.
- 	How: REST is easier to debug, gRPC is more efficient.

⸻

🔹 6. Additional Questions (Often Asked)

Q17. What’s HATEOAS in REST?
- 	Answer: Hypermedia As The Engine Of Application State → responses include links to next possible actions.
- 	Why: Makes APIs self-explanatory.
- 	How: Example: GET /orders/123 returns a link to /orders/123/cancel.

⸻

Q18. How do you cache responses in REST APIs?
- 	Answer: Use HTTP caching headers (ETag, Cache-Control, Last-Modified).
- 	Why: Improves performance & reduces load.
- 	How: Clients revalidate cached responses before fetching.

⸻

Q19. How do you handle concurrency in REST (optimistic vs pessimistic)?
- 	Answer:
- 	Optimistic → use ETags/If-Match headers to prevent overwrites.
- 	Pessimistic → lock resources until update is complete.
- 	Why: Prevents race conditions.
- 	How: ETag → server returns version hash; update only if hash matches.

⸻

Q20. How do you test REST APIs?
- 	Answer: Unit tests, integration tests (httptest in Go), Postman, curl, automated regression tests.
- 	Why: Reliability.
- 	How: Simulate real-world requests with test cases.

⸻

✅ Expanded Summary of Must-Know REST Topics
- 	HTTP basics: request structure, headers, versions.
- 	REST principles: stateless, resource-based, cacheable.
- 	REST vs RESTful: strict vs loose implementation.
- 	URL design: nouns, hierarchy, path vs query params.
- 	HTTP methods + idempotency: GET, POST, PUT, PATCH, DELETE.
- 	Status codes: use the right 2xx/4xx/5xx.
- 	Middleware in Go: logging, auth, rate limiting.
- 	REST vs gRPC: tradeoffs, payload formats.
- 	Add-ons: versioning, pagination, error handling, security, caching, concurrency (ETags).
- 	Advanced: HATEOAS, testing strategies.

🎯 Routing & HTTP Layer

Q1. When do you choose net/http vs chi vs gin?
- 	Answer:
- 	net/http: smallest deps, max control (tiny services, libs).
- 	chi: stdlib feel + robust middleware + nesting; great default.
- 	gin: huge ecosystem, batteries included, fast dev velocity.
- 	Why: Router choice affects ergonomics, not capability.
- 	How to decide: Team familiarity, middleware needs, performance profiling.

Q2. Why is gorilla/mux discouraged today?
- 	Answer: It’s archived; prefer chi/gin.
- 	Why: Maintenance & future-proofing.

Q3. What must a router middleware stack include?
- 	Answer: Request logging, panic recovery, request ID, timeout/context, CORS, auth, rate limiting.
- 	Why: Baseline operability & safety.

⸻

🔐 Auth, CORS, Security

Q4. Which JWT/OIDC libs do you mention and why?
- 	Answer: golang-jwt/jwt/v5 for JWT, coreos/go-oidc for OIDC.
- 	Why: Actively maintained; OIDC handles issuer/audience/keys properly.
- 	How: Validate issuer, audience, expiry, signature, and algorithm; cache JWKs.

Q5. CORS & CSRF packages to name?
- 	Answer: go-chi/cors for CORS, go-chi/csrf for cookie-based browser flows.
- 	Why: Browser security; CSRF only relevant with cookies.

Q6. Authorization layer?
- 	Answer: casbin for RBAC/ABAC policies.
- 	Why: Central policy enforcement, auditable rules.

⸻

🧰 Validation, Binding, Serialization

Q7. Preferred validation lib?
- 	Answer: go-playground/validator/v10.
- 	Why: De-facto standard; tag-based; good errors.
- 	How: Validate input structs; return 400 with field errors.

Q8. JSON choices and tradeoffs?
- 	Answer: encoding/json (safe default), jsoniter (faster), easyjson (codegen for hot paths).
- 	Why: Performance vs simplicity.
- 	Gotcha: Number decoding & omitempty surprises → know your model.

Q9. When do you bring in Protobuf/gRPC?
- 	Answer: Internal microservices, strict contracts, high throughput, streaming.
- 	Why: Smaller payloads, schema guarantees.
- 	How: Keep REST for external/public; gRPC for service-to-service.

⸻

🗄️ Database, Migrations, Caching

Q10. DB stack options and when to pick each?
- 	Answer:
- 	Driver: pgx (Postgres) via database/sql or native.
- 	Thin layer: sqlx (ergonomics, still SQL-first).
- 	ORM: gorm (feature-rich), ent (schema-as-code, compile-time safety), bun (perf+features).
- 	Why: Balance control, speed, and schema management.

Q11. Migrations tool you’d name?
- 	Answer: golang-migrate/migrate or pressly/goose.
- 	Why: Widely used, CI-friendly.

Q12. Redis client & cache libs?
- 	Answer: redis/go-redis/v9 (client), ristretto or bigcache (in-memory).
- 	Why: Distributed vs local caching layers.
- 	Gotcha: TTL + jitter to avoid stampede; cache-aside pattern.

⸻

📊 Observability (Logs, Metrics, Tracing, Profiling)

Q13. Logging libraries to call out?
- 	Answer: std slog (Go 1.21+), zap, zerolog.
- 	Why: Structured JSON logs; high throughput.

Q14. Metrics?
- 	Answer: prometheus/client_golang.
- 	Why: Standard in Go; counters, histograms, gauges; /metrics endpoint.

Q15. Tracing?
- 	Answer: OpenTelemetry (go.opentelemetry.io/otel/...).
- 	Why: Distributed tracing across services; context propagation.

Q16. Profiling?
- 	Answer: net/http/pprof, runtime/pprof.
- 	Why: CPU/heap/goroutine profiling under load to find hotspots.
- 	Gotcha: Protect pprof endpoints; don’t expose publicly.

⸻

🌐 HTTP Client, Resilience & Timeouts

Q17. HTTP client packages?
- 	Answer: std http.Client (with timeouts), go-retryablehttp, resty (ergonomics).
- 	Why: Retries/backoff for idempotent requests; connection reuse.
- 	Gotcha: Reuse one http.Client; set dial/read/write timeouts; always close bodies.

Q18. Circuit breaker / backoff libs?
- 	Answer: sony/gobreaker (breaker), cenkalti/backoff/v4 (exponential w/ jitter).
- 	Why: Prevent cascading failures; smooth retries.

⸻

🧾 Contracts & Docs

Q19. OpenAPI toolchain you’d mention?
- 	Answer: swaggo/swag (doc gen), deepmap/oapi-codegen (contract-first), goa (design-first framework).
- 	Why: Keep API contracts versioned; generate clients/servers; enforce compatibility in CI.

⸻

⏱️ Background Jobs & Scheduling

Q20. Background processing in Go?
- 	Answer: hibiken/asynq (Redis-backed jobs), robfig/cron/v3 (scheduling), Temporal (go.temporal.io/sdk) for long-running workflows.
- 	Why: Move non-critical, retryable work off request path.

⸻

🔁 Real-time & File Handling

Q21. Realtime comms libs?
- 	Answer: nhooyr.io/websocket (modern WS), SSE (stdlib).
- 	Why: Push updates; keep REST for CRUD, WS/SSE for events.

Q22. File uploads/storage?
- 	Answer: stdlib mime/multipart + cloud SDKs (AWS/GCP/Azure).
- 	Why: Offload to object storage; scan files out-of-band.

⸻

🧪 Testing

Q23. Test stack names?
- 	Answer: testify (assert/require), ginkgo/gomega (BDD), httptest (HTTP), gomock (mocks), httpexpect (black-box API tests).
- 	Why: Clear, repeatable API tests; contract tests for clients.

⸻

🧯 Deployment & Config

Q24. Config & secrets packages?
- 	Answer: spf13/viper (flex config), envconfig (env-first).
- 	Why: 12-factor apps; separate config from code.
- 	Gotcha: Use K8s Secrets/ASM/SSM in prod; avoid .env in production.

Q25. Graceful shutdown essentials?
- 	Answer: std context + os signals; drain, finish in-flight, close.
- 	Why: Prevent request loss & leaks.

⸻

⚠️ Pitfalls interviewers love (call these out)
- 	Creating a new http.Client per request → no connection pooling, leaks.
- 	No server/client timeouts → hung goroutines, resource exhaustion.
- 	Skipping input validation → 500s, security issues; always return 400 with details.
- 	Ambiguous status codes → clients can’t react properly.
- 	Ad-hoc auth checks in handlers → centralize in middleware; check issuer/audience/expiry/alg for JWT.
- 	No metrics/tracing → blind in prod; add latency histograms & trace IDs.
- 	Overusing ORMs for hot paths → know when to drop to raw SQL/pgx.
- 	CORS misconfig (wildcard with credentials) → security risk.
- 	Cache without TTL/jitter → stampedes.
- 	Unbounded concurrency → memory pressure; use pools/limits.

⸻

🧭 “If asked to design a REST service in Go, what stack do you propose and why?”
- 	Router: chi — lightweight, composable middleware.
- 	Logs: slog or zap — structured JSON.
- 	Validation: validator — standard.
- 	DB: pgx + sqlx — performance + ergonomics; migrate for schema.
- 	Cache: go-redis — cache-aside with TTL+jitter.
- 	Security: go-oidc / golang-jwt/jwt — proper token validation.
- 	Metrics/Tracing: Prometheus + OpenTelemetry — SLOs & tracing.
- 	HTTP client: single http.Client with timeouts + retryablehttp for idempotent calls.
- 	Docs: OpenAPI with oapi-codegen (contract-first).
- 	Testing: testify + httptest + gomock.
