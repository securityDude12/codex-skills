# Interpretation Guide

## Purpose

Translate raw recon output into meaningful indicators.

Do not assume certainty. Identify signals and possibilities.

Use confidence labels:
- confirmed: directly observed in command output
- likely: strongly indicated by multiple signals
- possible: weak or indirect signal
- inconclusive: insufficient or conflicting evidence

---

## Headers

Missing:
- Content Security Policy -> possible hardening gap, not confirmed XSS
- Strict Transport Security -> possible HTTPS hardening gap, not proof of downgrade exposure

Exposed:
- Server header -> possible stack disclosure
- X-Powered-By -> possible framework disclosure

Redirect and transport:
- HTTP to HTTPS redirect -> confirmed transport preference
- HTTPS failure -> possible TLS, routing, or filtering issue; do not assume outage without corroboration
- Set-Cookie without Secure on HTTPS -> possible cookie hardening gap

---

## Stack Indicators

React / Angular / Vue:
- frontend-heavy application
- API backend possible, not guaranteed

Firebase:
- hosted services possible
- do not test rules or access controls unless explicitly authorized

Node / Express:
- route-based backend possible
- do not infer endpoint existence from framework alone

CMS indicators:
- WordPress, Drupal, Joomla, or similar fingerprints indicate platform family
- treat version strings as unverified unless directly observed from reliable output

---

## Infrastructure

Cloudflare:
- CDN present
- origin hidden

AWS / Google / Azure:
- cloud hosted
- shared infrastructure likely

Generic IP:
- may indicate direct hosting

Certificate mismatch:
- possible misconfiguration, proxying issue, or wrong virtual host

---

## Endpoint Signals

Common:
- /api
- /login
- /admin
- /dashboard
- /health
- /status

Indicators:
- 200 -> confirmed response at path
- 401/403 -> path likely exists or is filtered; inspect body length and headers before concluding
- 301/302 -> redirect logic observed
- identical status and body length across many paths -> possible soft-404 or CDN behavior

---

## Defensive Signals

WAF indicators:
- 403 on normal requests
- inconsistent responses
- vendor-specific headers or challenge pages

Rate limiting:
- delayed responses
- blocked after repeated attempts
- 429 or equivalent throttling response

CDN:
- masked headers
- generic responses
- edge-specific cache or trace headers

---

## Uncertainty Rule

Always classify findings as:

- confirmed
- likely
- possible
- inconclusive

Never assume full visibility.
