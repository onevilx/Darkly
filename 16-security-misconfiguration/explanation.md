# Breach 16 — Security Misconfiguration — *no flag*

**OWASP:** A05:2021 – Security Misconfiguration

## What it is
Multiple hardening failures that each weaken the platform and shortcut other
breaches.

## How the breach works
```bash
curl -s -D - -o /dev/null http://localhost:4942/
# x-powered-by: Python/3.11 FastAPI/0.104   <- stack/version disclosure
# x-pocketbase: http://localhost:8090        <- points at the backend
# x-42-internal: campus=paris                <- internal metadata
curl -s -D - -o /dev/null http://localhost:4942/backup
# x-backup-exclude: data/private_notes.txt   <- the exact LFI target (breach 09)
```
- **Session cookie is not `HttpOnly`** → `document.cookie` returns the JWT
  (proven in breach 10) — any XSS steals the session.
- **PocketBase admin UI reachable** at `:8090/_/` (+ leaked creds → breach 08).

## Impact
Information leakage that shortcuts almost every other breach; XSS-to-account-
takeover made trivial by the missing cookie flag.

## How it could have been avoided (remediation)
- Strip `X-Powered-By` and custom `X-*` headers in production.
- Set cookies `HttpOnly`, `Secure`, `SameSite=Strict`.
- Never expose the admin console to untrusted networks; remove backup metadata.
