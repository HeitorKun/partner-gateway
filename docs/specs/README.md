# Specs

Every slice of `partner-gateway` starts here, **before any code**. A spec is the contract that the code,
the tests and the coding agent all work from.

## The loop

1. **Write the spec** (`NNN-short-name.md`, from [the template](#template)). Its acceptance criteria are
   numbered and testable.
2. **Review it** before coding: missing states, error cases, security and audit needs.
3. **Turn the acceptance criteria into tests**: JBehave stories for behaviour seen from outside, TestNG for
   rules inside. They fail first.
4. **Implement** until they pass. Claude Code works from the spec and the failing tests; every change is reviewed.
5. **Close the spec:** set its status to `Done` and record any decision that changed along the way.

A spec changes only through a reviewed edit. If the code and the spec disagree, one of them is wrong; fix
whichever one is wrong, on purpose.

## Index

| Spec | Slice | Status |
| --- | --- | --- |
| [001-partner-onboarding.md](001-partner-onboarding.md) | Partner onboarding (KYB) | Draft |

## Template

```markdown
# NNN: <Title>

Status: Draft | Ready | In progress | Done

## Why
One paragraph: who needs this and what goes wrong without it.

## Scope
In: ... Out: ...

## Behaviour
States, flows, API (method, path, request, responses), events published.

## Acceptance criteria
AC1. Given ..., when ..., then ...
AC2. ...

## Rules a bank cares about
Audit trail, data protection, idempotency, permissions, retention.

## Open questions
- ...

## Decisions
- YYYY-MM-DD: ... because ...
```
