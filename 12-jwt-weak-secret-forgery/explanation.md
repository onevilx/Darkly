# Breach 12 — Weak & Leaked JWT Secret → Session Forgery — *no flag*

**OWASP:** A02:2021 – Cryptographic Failures / A07 – Auth Failures

## What it is
The session cookie is HS256-signed with the secret **`42network`** — guessable
and leaked twice (base64 in a forum post; `jwt_secret` in the XXE config).

## How the breach works (verified)
The server **does** validate the signature — `alg:none`, wrong-key, and
unsigned tokens are all rejected (302 → `/login`). But it derives the effective
role from the **DB record identified by `sub`**, ignoring the token's `role`
claim. So the exploit is **forging a validly-signed token for any `sub`**:

```python
sig = HMAC_SHA256("42network", header + "." + payload)   # see exploit.sh
```

Impersonating `sub=k1asdfeditojrb4` (wil) returns `200` on `/admin` and
`/staff/dashboard` — full auth bypass, no password.

## Impact
Become any user at any privilege level without credentials.

## How it could have been avoided (remediation)
- Long, random, **secret-managed** signing key; rotate it; never expose it via
  config endpoints.
- Prefer server-side sessions or short-lived asymmetric (RS256) tokens.
