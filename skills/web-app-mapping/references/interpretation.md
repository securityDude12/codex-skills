# Interpretation Guide

## Confidence Labels

Use:
- confirmed: directly observed in command output
- likely: strongly indicated by multiple signals
- possible: weak or indirect signal
- inconclusive: insufficient or conflicting evidence

Do not upgrade confidence because an endpoint name looks interesting.

---

## Endpoint Classification

Route source:
- declared: robots, sitemap, OpenAPI, app docs, manifests
- discovered: crawler, content discovery, HTML, JavaScript, redirects
- inferred: suggested by naming patterns or framework conventions
- archived: found in historical sources such as gau or waybackurls

Route type:
- page
- API
- asset
- auth
- admin
- debug
- status
- documentation
- GraphQL
- unknown

Auth state:
- public: observed without authentication
- requires-auth: 401, 403, login redirect, or auth challenge observed
- mixed: differs by method, path, cookie, or redirect state
- unknown: not tested enough to classify

---

## Status Codes

2xx:
- confirmed response at path
- not proof that access control is correct

3xx:
- redirect behavior observed
- record target and whether it appears to be login, canonicalization, or app routing

401/403:
- endpoint may exist or may be filtered
- compare body length, headers, and redirect patterns before concluding

404:
- path not found only for the tested request shape
- watch for SPA fallback and soft-404 behavior

429:
- stop or slow down
- report rate limiting

5xx:
- stop aggressive probing
- report instability

---

## Soft-404 and SPA Behavior

Signals:
- many unknown paths return the same 200 response
- identical body length across unrelated paths
- root app shell returned for missing routes
- CDN or framework fallback behavior

If present:
- do not count every matched path as confirmed
- require stronger evidence, such as unique title, body, JSON shape, redirect, or linked source

---

## API Signals

Indicators:
- JSON content type
- fetch, XHR, axios, GraphQL client, or tRPC strings in JavaScript
- `/api`, `/v1`, `/rpc`, `/graphql`, `/openapi`, `/swagger`, `/redoc`
- CORS headers
- auth or session cookies

Do not infer allowed methods unless directly observed or declared by docs.

---

## Auth Surface Signals

Auth-related endpoints include:
- login
- logout
- register
- reset password
- change password
- invite
- verify email
- SSO callback
- OAuth callback
- MFA challenge
- session
- refresh token
- account settings

Possible candidate areas:
- frontend-only route guards
- inconsistent login redirects
- missing cookie hardening attributes
- exposed reset or invite flows
- unauthenticated metadata endpoints

Phrase these as candidate areas, not vulnerabilities.

---

## GraphQL Signals

Confirmed GraphQL surface:
- response clearly indicates GraphQL endpoint or UI
- GraphQL-specific content type, error shape, or page title

Possible GraphQL surface:
- route name, script string, or archived URL suggests GraphQL

Do not run introspection unless explicitly authorized. If introspection is authorized, keep it bounded and record that authorization.

---

## Build Artifact Signals

Interesting artifacts:
- source maps
- static build manifests
- OpenAPI specs
- docs
- status pages
- debug interfaces
- example config files
- public environment variable references

Do not retrieve or print secrets. Confirm exposure minimally and stop.

---

## AI-Generated Stack Indicators

Possible indicators:
- default scaffold routes
- inconsistent client and server validation hints
- generated comments in bundled JavaScript
- exposed framework templates
- frontend route guards with thin backend evidence
- glue-code endpoints with broad trust in client-provided fields

These are only prioritization signals for manual review.
