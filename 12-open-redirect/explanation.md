# Breach 12 — Open Redirect — *no flag*

**OWASP:** A01:2021 – Broken Access Control (unvalidated redirect)

## What it is
A redirect endpoint that sends users to a URL taken from a request parameter
without validating it against an allow-list.

## How the breach works
```bash
curl -s -D - "http://localhost:4942/redirect?next=https://evil.example.com"
# HTTP/1.1 307 Temporary Redirect
# location: https://evil.example.com
```
`next` is followed to any external host (protocol-relative `//evil.com` works).

## Impact
Phishing / credential harvesting under a trusted domain; can leak tokens when
chained with OAuth-style flows.

## How it could have been avoided (remediation)
- Allow-list **internal paths only**; reject absolute, scheme, and `//` URLs.
- If external links are required, show an interstitial and sign the target.
