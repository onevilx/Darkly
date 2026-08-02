# Breach 10 — Reflected XSS (Newsletter) — *no flag*

**OWASP:** A03:2021 – Injection (XSS)

## What it is
User input reflected straight into the HTML response without output encoding,
executing script in the victim's session when they open a crafted link.

## How the breach works
The `/newsletter` form POSTs and 302-redirects to
`/newsletter?email=<input>&msg=subscribed`. The "subscribed with:" banner
renders `email` **unescaped** (the `<input value>` attribute is escaped, the
banner `<div>` is not — the developers even argue about it in an HTML comment).

```text
http://localhost:4942/newsletter?email=<script>alert(document.cookie)</script>&msg=subscribed
```

The script executes and pops the session cookie (non-`HttpOnly`, breach 16).

## Impact
Arbitrary JS in a victim's session; a crafted link sent to a higher-privileged
user steals their session token.

## How it could have been avoided (remediation)
- Context-aware **output encoding** of the banner (escape exactly like the
  `value` attribute); never build HTML from raw request parameters.
- Add a strict **Content-Security-Policy**.
