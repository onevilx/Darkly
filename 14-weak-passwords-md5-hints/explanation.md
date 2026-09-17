# Breach 14 — Weak Passwords + Exposed MD5 Hints — *no flag*

**OWASP:** A02:2021 – Cryptographic Failures / A07 – Auth Failures

## What it is
Every user carries a `pw_hint` that is the **unsalted MD5 of the password**,
readable via the IDOR/PocketBase exposure — an offline-crackable password oracle.

## How the breach works
```python
# md5(pw_hint) cracked against rockyou.txt  (see exploit.sh)
# jdoe     => abc123
# benjamin => b3njamin!
```
`benjamin:b3njamin!` is accepted by PocketBase
`/api/collections/users/auth-with-password`, confirming hint == password.

## Impact
Account takeover of any user with a weak password; unsalted MD5 offers no
protection against wordlists/rainbow tables.

## How it could have been avoided (remediation)
- **Never** store a hint derived from the password.
- Hash passwords with **bcrypt/argon2** (salted, slow); enforce strength.
