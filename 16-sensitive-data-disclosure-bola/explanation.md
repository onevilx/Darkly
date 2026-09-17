# Breach 16 — Sensitive Data / Schema Disclosure & BOLA — *no flag*

**OWASP:** A01 – Broken Access Control (BOLA) / A05 – Misconfiguration

## What it is
Endpoints leak internal information: an API schema/debug endpoint, and a
single-object endpoint that returns private fields for **any** object id
(Broken Object-Level Authorisation).

## How the breach works
```bash
# 1) Internal schema: writable fields, role enum, and the injection sink
curl -s http://localhost:4942/api/docs-internal
# 2) BOLA: private fields for ANY id
curl -s http://localhost:4942/api/users/<victim_id>   # private_note, pw_hint, recovery_code
```
`/api/docs-internal` hands an attacker the mass-assignment field list (breach 6)
and the grades-injection point (breach 15).

## Impact
A roadmap for every other attack plus direct disclosure of other users' private
notes and password hints.

## How it could have been avoided (remediation)
- Remove internal/debug documentation endpoints from production.
- Enforce **per-object authorisation** on `/api/users/{id}` (return only the
  caller's own private fields).
