# Breach 17 — Vulnerable & Outdated Components — *no flag*

**OWASP:** A06:2021 – Vulnerable & Outdated Components

## What it is
Running old, or deliberately weakened, dependencies with known issues.

## How the breach works
The stack advertises exact versions (`uvicorn/0.24.0`, `FastAPI/0.104`,
PocketBase `0.22.4`), and the deploy-log HTML comment admits:
```text
v1.3.4 — wil — "disabled defusedxml temporarily" (6 months ago)
v1.3.7 — emilie reported: replace stdlib xml.etree with defusedxml
```
Running stdlib `xml.etree` instead of `defusedxml` is the **root cause** of the
XXE (breach 07).

## Impact
Disabled/old components are the direct cause of exploitable bugs (XXE) and
expose the app to known CVEs in the pinned versions.

## How it could have been avoided (remediation)
- Re-enable `defusedxml` (or `lxml` with entity resolution off).
- Keep dependencies patched and pinned to maintained versions; don't advertise
  versions in headers.
