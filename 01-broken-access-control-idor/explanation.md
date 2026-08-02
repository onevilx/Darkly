# Breach 01 — Broken Access Control (IDOR)

**OWASP:** A01:2021 – Broken Access Control
**Flag:** `FLAG{1d0r_ur_pr0f1l3_1s_m1n3}`

## What it is
Broken Access Control is the failure to enforce *what an authenticated (or
unauthenticated) actor is allowed to do*. The concrete shape here is an
**Insecure Direct Object Reference (IDOR)**: a resource is addressed by an
identifier in the request, and the server serves it to *whoever asks*, without
verifying that the requester is authorised for that specific object.

## How the breach works
1. As a **guest** (no login), the forum contains a "scheduled maintenance" post
   with a **View profile** link.

   ![forum link](Resources/idor1.png)

2. Following it loads another user's profile — internal data exposed to an
   unauthenticated visitor. The profile is addressed by an opaque id, so any id
   can be substituted.

   ![victim profile](Resources/idor2.png)

3. The profile even carries a private note captioned *"Only visible to wil"* —
   visible to us — and the flag.

   ![flag](Resources/idor3.png)

Run `./exploit.sh` to reproduce with a raw HTTP request and no session cookie.

## Impact
Any unauthenticated attacker can read every user's private data (email, private
notes, role). This is also the **first link in the kill chain**: the leaked
email feeds the account-takeover in breach 02/04.

## How it could have been avoided (remediation)
- Enforce authorisation **server-side, per object, on every request** — verify
  the session owner is allowed to read *this* record before returning it.
- Return only fields the caller is entitled to (a public view vs. an owner view).
- Never rely on the obscurity of an id; treat every object reference as attacker-controlled.
