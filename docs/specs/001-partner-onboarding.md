# 001: Partner onboarding (KYB)

Status: **Draft**. The outline is set; the sections marked *To write* are yours to fill before any code.

## Why

An institutional partner (an external asset manager, a fintech, an introducing broker) can't use the
bank's APIs until the bank knows who it is: Know Your Business (KYB). Today that would be email and
spreadsheets. Without a tracked process, there's no audit trail of who approved whom, when, and why.

## Scope

**In:** a partner applies with its Swiss company UID; the service looks the company up in the federal
registry; a compliance reviewer moves the application through its lifecycle; every transition is audited.

**Out (later slices):** document upload and the AI check (002), API keys (003), events (004), the
back-office GraphQL view (005).

## Behaviour

### Lifecycle

```
SUBMITTED ──► UNDER_REVIEW ──► APPROVED ──► ACTIVE ──► SUSPENDED ──► ACTIVE
                   │                           │            │
                   ▼                           ▼            ▼
               REJECTED                    TERMINATED   TERMINATED
```

*To write:* who can trigger each transition (partner, reviewer, system) and whether it needs a reason.
Which states are final? Can a rejected partner apply again with the same UID?

### Registry lookup

The UID (format `CHE-123.456.789`) is checked against the Swiss central business index,
[Zefix](https://www.zefix.admin.ch/). In tests, WireMock plays Zefix.

*To write:* what happens when the UID is malformed, unknown, or belongs to a deleted company, and when
Zefix is down or slow (timeout? retry? accept and check later?).

### API

*To write:* the endpoints, for example `POST /partners`, `GET /partners/{id}`,
`POST /partners/{id}/transitions`. Request and response bodies, status codes, the error format.

## Acceptance criteria

*To write.* Number them so tests can point at them. Two to start you off:

- **AC1.** Given a valid UID of an active company in Zefix, when a partner applies, then the application is
  stored as `SUBMITTED` with the legal name from Zefix, and the response is `201 Created` with its location.
- **AC2.** Given a partner in `REJECTED`, when anyone tries to move it to `ACTIVE`, then the request is refused
  with `409 Conflict` and the state does not change.

## Rules a bank cares about

*To write.* Points to cover:

- **Audit:** who did what, when, from which state to which, and why. Can audit rows ever be changed?
- **Concurrency:** two reviewers act on the same partner at once (optimistic locking with `@Version`).
- **Idempotency:** the same application is sent twice, because the client retried.
- **Data protection:** which personal data is stored (signatories), and for how long.

## Open questions

- Is the reviewer identity real authentication now, or a header until slice 003?
- Does approval need two people (four-eyes principle)?

## Decisions

*None yet.*
