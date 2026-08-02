# Breach 18 — Security Logging & Monitoring Failures — *no flag*

**OWASP:** A09:2021 – Security Logging & Monitoring Failures

## What it is
No meaningful logging, alerting, or anti-automation — attacks run indefinitely
and undetected.

## How the breach works
```bash
# 6 wrong logins -> 302,302,302,302,302,302  (no lockout/captcha/throttle)
# telemetry: accepts anything; deploy log admits "it's just a console.log"
```
The entire audit — credential guessing, session forgery, XSS against the bot,
full DB dumps — ran unthrottled and (observably) unalerted.

## Impact
Brute force, enumeration, and injection can run indefinitely with no forensic
trail acted upon.

## How it could have been avoided (remediation)
- Real server-side logging of security events + alerting/anomaly detection.
- Login **rate-limiting** + lockout/captcha; access control on log data.
