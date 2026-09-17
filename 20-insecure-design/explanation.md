# Breach 20 — Insecure Design — *no flag*

**OWASP:** A04:2021 – Insecure Design

## What it is
Insecure Design covers weaknesses that are **deliberate design choices** rather
than isolated implementation bugs — the system is insecure *as specified*, so no
amount of careful coding of the current design would fix it. In Darkly several
security decisions are unsound by construction, independent of any single
endpoint.

Note: this isn't a 20th distinct bug on the platform — every point below traces
back to a breach already explained elsewhere (04, 07, 14, 16, 19). It's a
synthesis, written to name the design-level pattern those bugs share.

## How it manifests (verified, design-level)
- **MD5 used as if it were a secret.** The password-reset token is
  `md5(email)` (breach 04) and every user's `pw_hint` is `md5(password)`
  (breach 14). MD5 is a *fast, unsalted, public* function — using it anywhere a
  secret or an unpredictable token is required is a design error, not a bug.
- **Predictable-by-construction tokens.** Because the reset token is derived
  deterministically from a public identifier, it can be reproduced offline for
  any user. A token that an attacker can *compute* is not a token.
- **No anti-automation anywhere in the auth design.** There is no rate-limiting,
  lockout, or CAPTCHA on login or on the reset flow (see breach 19). The design
  simply never accounted for automated abuse.
- **Secrets reachable by design.** An internal config endpoint returns the JWT
  signing secret and the PocketBase admin credentials (breach 07); the schema
  and writable fields are published at `/api/docs-internal` (breach 16). The
  application is *designed* to expose information an attacker needs.

## Impact
The platform cannot be made safe by patching individual endpoints, because the
threat model was never applied to the design: predictable tokens, fast hashes as
secrets, and freely reachable internals mean multiple breaches are the *expected*
behaviour of the system as designed.

## How it could have been avoided (remediation)
- Threat-model the design up front: enumerate abuse cases (token forgery,
  credential stuffing, secret exposure) and design controls for them.
- Use **CSPRNG, single-use, server-bound** tokens; hash passwords with
  **bcrypt/argon2**; never derive secrets/tokens from public data.
- Build anti-automation (rate-limit + lockout) into the auth flows by design.
- Keep secrets and schema out of any reachable endpoint.
