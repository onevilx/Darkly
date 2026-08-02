# Darkly v2 — 42 Web Security Project

An offensive security audit of the **Darkly** platform (42 Network, subject v6.0).
The target is the web application at `http://localhost:4942` (with a PocketBase
backend at `http://localhost:8090`). Everything here was performed against the
sanctioned web target only — **the appliance/VM/OS was never touched**, per the
subject's scope.

> **Combined walk-through:** [`walkthrough.md`](walkthrough.md) — the full,
> illustrated audit (recon → every flag → bonus weaknesses).

## Repository layout

Each vulnerability lives in **its own folder** (as suggested by the subject),
containing:

- `exploit.sh` — a script that reproduces the exploitation (my own; **no** turn-key tools like sqlmap)
- `explanation.md` — how it works, its **impact**, and **how it could have been avoided** (remediation)
- `flag` — the recovered token, when the breach yields one
- `Resources/` — screenshots / evidence

## Flags recovered (9)

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

## Additional weaknesses explained (no flag)

| # | Breach | OWASP |
|---|--------|-------|
| 10 | [Reflected XSS (Newsletter)](10-reflected-xss/) | A03 |
| 11 | [Open Redirect](11-open-redirect/) | A01 |
| 12 | [Weak/leaked JWT secret → session forgery](12-jwt-weak-secret-forgery/) | A02/A07 |
| 13 | [Weak passwords + exposed MD5 hints](13-weak-passwords-md5-hints/) | A02/A07 |
| 14 | [PocketBase filter injection](14-pocketbase-filter-injection/) | A03 |
| 15 | [Sensitive data disclosure & BOLA](15-sensitive-data-disclosure-bola/) | A01/A05 |
| 16 | [Security misconfiguration](16-security-misconfiguration/) | A05 |
| 17 | [Vulnerable & outdated components](17-outdated-components/) | A06 |
| 18 | [Security logging & monitoring failures](18-logging-monitoring-failures/) | A09 |
| 19 | [Insecure design](19-insecure-design/) | A04 |

**Total: 9 flags recovered, 19 vulnerabilities explained.**

## Running an exploit

```bash
# most scripts take a BASE url and, where needed, a SESSION cookie
BASE=http://localhost:4942 ./01-broken-access-control-idor/exploit.sh
SESSION=<student-cookie> ./06-mass-assignment/exploit.sh
```

## A note on the 10th flag

The subject says there are 10 flags. i found 9. i looked for the last one
everywhere i could on the web target: dumped every PocketBase collection as
admin, read every file i could reach with the LFI, hit every route as every
role from guest up to god, and tried every vuln class the original Darkly uses.
it just isn't there in this build, so my guess is the 10th flag only gets wired
up on the graded appliance. full reasoning is at the end of
[`walkthrough.md`](walkthrough.md). i did **not** touch or reverse-engineer the
appliance, since the subject says not to.
