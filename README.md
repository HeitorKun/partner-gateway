# partner-gateway

A Java service that **onboards institutional partners** (external asset managers, fintechs, introducing
brokers) and gives them **API access** to an online bank. It is built the way a regulated bank needs:
every decision audited, every integration tested against real infrastructure, every feature specified
before it's coded.

It is built **spec-first with a coding agent (Claude Code)**. Each feature starts as a spec in
[`docs/specs/`](docs/specs/); its acceptance criteria become failing tests; the agent works from the spec
and the tests; every change is reviewed. [`CLAUDE.md`](CLAUDE.md) holds the rules the agent follows.

## Stack

| Concern | Choice |
| --- | --- |
| Language, build | Java 21, Maven (wrapper included) |
| Framework | Spring Boot 4.1, Spring MVC on embedded Tomcat, virtual threads |
| Persistence | PostgreSQL 17, JPA/Hibernate 7, Flyway migrations (Hibernate only validates the schema) |
| Cache and rate limits | Redis 7 |
| Events | RabbitMQ 4 |
| Tests | TestNG, Testcontainers (real Postgres, Redis, RabbitMQ), WireMock for third parties, JaCoCo coverage |
| Operations | Actuator health probes, Prometheus metrics, JSON logs (Logstash encoder) for Elastic/Kibana |
| Delivery | Multi-stage Dockerfile (non-root, layered), docker-compose, Jenkinsfile with a SonarQube quality gate |

## Roadmap

| # | Slice | Status |
| --- | --- | --- |
| 0 | Scaffold: build, infrastructure, smoke tests, CI | Done |
| 1 | [Partner onboarding (KYB)](docs/specs/001-partner-onboarding.md): Swiss registry (Zefix) lookup, audited lifecycle | Spec in draft |
| 2 | AI document check: an LLM extracts data from the commercial register extract; mismatches with the registry go to a human; every model call is auditable | Planned |
| 3 | API credentials: hashed keys, scopes, rotation, revocation, Redis rate limiting | Planned |
| 4 | Domain events on RabbitMQ through a transactional outbox, with an idempotent consumer and a dead-letter queue | Planned |
| 5 | Back-office GraphQL read API | Planned |
| 6 | Kubernetes manifests, Grafana dashboard | Planned |

## Run it

Requirements: Java 21 and Docker.

```bash
docker compose up -d          # Postgres, Redis, RabbitMQ (management UI on :15672, guest/guest)
./mvnw spring-boot:run        # the service on :8080
curl localhost:8080/actuator/health
```

Or everything in containers: `docker compose --profile app up -d --build`.

## Test it

```bash
./mvnw verify                 # TestNG suite; Testcontainers starts its own Postgres, Redis and RabbitMQ
```

Reports: `target/surefire-reports/index.html` (TestNG), `target/site/jacoco/index.html` (coverage).
