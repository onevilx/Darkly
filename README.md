# Darkly v2 — 42 Web Security Project

An offensive security audit of the **Darkly** platform (42 Network, subject v6.0).
The target is the web application at `http://localhost:4942` (with a PocketBase
backend at `http://localhost:8090`). Everything here was performed against the
sanctioned web target only — **the appliance/VM/OS was never touched**, per the
subject's scope.

> **Combined walk-through:** [`walkthrough.md`](walkthrough.md) — the full,
> illustrated audit (recon → every flag → bonus weaknesses).

The subject seeds the platform with **19 vulnerabilities hiding 10 flags**, split
in two: the **mandatory part** asks for 6 flags (4 won by escalating privilege
inside the app step by step up to administrator, 2 more of your choice) across
10 explained vulnerabilities, and the **bonus part** — evaluated only if the
mandatory part is perfect — asks for 4 additional flags (10 total) across 5 more
vulnerabilities, at least one of which mints no flag. This repo clears both: all
10 flags are recovered in breaches 01–10, and the bonus is nearly doubled with
nine more weaknesses (11–19) explained with no flag attached — bringing the
running total to 19 distinct vulnerabilities, exactly matching the subject's
stated platform-wide count. Breach 20 is an extra, on top of that: a
design-level write-up (OWASP A04) that synthesizes several of the above bugs
rather than introducing a new instance of its own.

## Repository layout

Each vulnerability lives in **its own folder** (as suggested by the subject),
containing:

- `exploit.sh` — a script that reproduces the exploitation (my own; **no** turn-key tools like sqlmap)
- `explanation.md` — how it works, its **impact**, and **how it could have been avoided** (remediation)
- `flag` — the recovered token, when the breach yields one
- `Resources/` — screenshots / evidence

## Flags recovered (10)

| # | Breach | Flag |
|---|--------|------|
| 01 | [Broken Access Control / IDOR](01-broken-access-control-idor/) | `FLAG{1d0r_ur_pr0f1l3_1s_m1n3}` |
| 02 | [Unrestricted File Upload](02-unrestricted-file-upload/) | `FLAG{unr3str1ct3d_upl0ad_g0_brrr}` |
| 03 | [Stored XSS (moderation bot)](03-stored-xss/) | `FLAG{xss_st0r3d_1s_n0t_4_f34tur3_w1l}` |
| 04 | [Insecure Password Reset (md5 token)](04-insecure-password-reset/) | `FLAG{r3s3t_t0k3n_w4s_just_md5_lol}` |
| 05 | [Hidden API endpoint](05-hidden-api-endpoint/) | `FLAG{md5_1s_4_n4m3pl4t3_n0t_4_l0ck}` |
| 06 | [Mass Assignment](06-mass-assignment/) | `FLAG{just_p4tch_y0ur_0wn_r0l3_lol}` |
| 07 | [XXE → SSRF](07-xxe-to-ssrf/) | `FLAG{d3fus3dxml_n3xt_spr1nt_pr0m1s3}` |
| 08 | [Privilege Escalation via PocketBase](08-privilege-escalation-pocketbase/) | `FLAG{th3_und3rsc0r3_sl4sh_kn0ws_th3_w4y}` |
| 09 | [LFI / Path Traversal](09-lfi-path-traversal/) | `FLAG{d0t_d0t_sl4sh_4ll_th3_w4y_d0wn}` |
| 10 | [CSRF — no Origin/Referer validation](10-csrf-origin-validation/) | `FLAG{csrf_4ny_0r1g1n_1s_w3lc0m3}` |

## Additional weaknesses explained (no flag)

| # | Breach | OWASP |
|---|--------|-------|
| 11 | [Reflected XSS (Newsletter)](11-reflected-xss/) | A03 |
| 12 | [Open Redirect](12-open-redirect/) | A01 |
| 13 | [Weak/leaked JWT secret → session forgery](13-jwt-weak-secret-forgery/) | A02/A07 |
| 14 | [Weak passwords + exposed MD5 hints](14-weak-passwords-md5-hints/) | A02/A07 |
| 15 | [PocketBase filter injection](15-pocketbase-filter-injection/) | A03 |
| 16 | [Sensitive data disclosure & BOLA](16-sensitive-data-disclosure-bola/) | A01/A05 |
| 17 | [Security misconfiguration](17-security-misconfiguration/) | A05 |
| 18 | [Vulnerable & outdated components](18-outdated-components/) | A06 |
| 19 | [Security logging & monitoring failures](19-logging-monitoring-failures/) | A09 |
| 20 | [Insecure design](20-insecure-design/) *(synthesis of 04/07/14/16/19, not a new instance)* | A04 |

**Total: 10 flags recovered (6 mandatory + 4 bonus), 19 vulnerabilities explained (10 mandatory + 9 bonus) — matching the subject's stated platform total exactly — plus a 20th, cross-cutting design-level write-up.**

## Running an exploit

```bash
# most scripts take a BASE url and, where needed, a SESSION cookie
BASE=http://localhost:4942 ./01-broken-access-control-idor/exploit.sh
SESSION=<student-cookie> ./06-mass-assignment/exploit.sh
SESSION=<student-cookie> ./10-csrf-origin-validation/exploit.sh
```
