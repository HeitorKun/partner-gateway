# CLAUDE.md

Rules for coding agents working in this repository. Humans follow them too.

## What this is

`partner-gateway`: a Spring Boot 4.1 service (Java 21, Spring MVC, JPA/Hibernate, PostgreSQL, Redis,
RabbitMQ) that onboards institutional partners of a bank and gives them API access. See `README.md`.

## Workflow: spec first

1. No feature code without a spec in `docs/specs/` whose status is `Ready` or `In progress`.
   If the spec is missing or unclear, stop and ask; don't invent requirements.
2. Write the failing tests from the spec's acceptance criteria first. Name each test after its criterion
   (`ac2_rejectedPartnerCannotBeActivated`).
3. Implement the smallest change that makes them pass, then refactor.
4. A change in behaviour = a change in the spec, made in the same PR, with an entry under its `Decisions`.

## Tests

- **TestNG only** (JUnit is excluded from the build). Spring tests extend `AbstractTestNGSpringContextTests`.
- Integration tests use real infrastructure through `TestcontainersConfiguration`. Never use H2 or
  in-memory fakes of Postgres, Redis or RabbitMQ.
- Third-party HTTP APIs (Zefix, the LLM provider) are stubbed with **WireMock**. Tests never call the internet.
- **Never weaken, skip or delete a failing test to make a build pass.** Report it instead.
- Run `./mvnw verify` before calling anything done, and quote the result.

## Code

- Package by feature under `io.github.heitorkun.partnergateway` (`partner`, `credentials`, ...), not by layer.
- Schema changes only through a new Flyway migration (`V<n>__description.sql`). Never edit a merged one.
  `ddl-auto` stays `validate`.
- Money is `BigDecimal`, time is `Instant` in UTC, identifiers are UUIDs.
- Constructor injection only; no field `@Autowired` in production code.
- Errors leave the API as RFC 9457 problem details (`ProblemDetail`).

## Banking rules

- Every state change of a partner, a key or a document writes an **audit record**: who, what, when, from, to, why.
  Audit rows are append-only.
- **No secrets, real client data or personal data in code, tests, fixtures or logs.** Test data is invented.
  API keys and tokens are stored hashed and never logged.
- Every LLM call is recorded: model, prompt version, input hash, output. LLM output is a suggestion
  that a human confirms, never a final decision.

## Commits

- Small commits, imperative subject (`Add partner lifecycle transitions`), explaining *why* in the body.
- Keep the `Co-Authored-By` trailer on agent-written commits.
