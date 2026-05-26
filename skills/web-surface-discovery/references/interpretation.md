# Interpretation

Use confidence labels consistently:
- confirmed: directly observed with current evidence
- likely: strong evidence, but not fully validated
- possible: weak or indirect signal
- inconclusive: insufficient or conflicting evidence

Do not call a discovery a vulnerability unless current evidence proves the security condition.

---

## Route and Directory Findings

2xx response:
- confirmed route when the response differs from known soft-404 behavior
- likely route when response size and redirect behavior are plausible but content is not inspected

3xx response:
- confirmed redirect behavior
- likely route if the redirect target is stable and differs from soft-404 redirects
- possible route if many paths redirect identically to login or root

401 or 403 response:
- confirmed protected or denied path
- content and function are unknown unless declared elsewhere
- do not infer vulnerability from access denial alone

404 or 410 response:
- no finding unless the response differs meaningfully from baseline

5xx response:
- inconclusive service behavior
- do not keep probing if instability repeats

Soft-404 or app-shell response:
- possible route only when there are distinct secondary signals
- otherwise filter as noise

---

## Artifact Findings

Public docs, manifests, source maps, config examples, status pages, and build metadata:
- confirmed artifact when status, content type, and minimal body evidence match
- likely artifact when status and filename strongly suggest the type but content was not retrieved
- possible artifact when only a name, route string, redirect, or archived source suggests it

Sensitive-looking files:
- confirm only minimal metadata
- do not retrieve full contents
- report as a sensitive artifact candidate or exposure signal

Backup-like names:
- likely backup artifact only when status and content type support it
- possible backup artifact when only filename pattern suggests it

---

## Admin, Debug, and API Surfaces

Admin paths:
- confirmed when an admin interface, login, or access-denied response is observed at an admin-labeled route
- likely when stable redirects or titles indicate admin behavior
- possible when only route naming suggests it

Debug paths:
- confirmed when debug interface text, stack traces, or framework debug responses are minimally observed
- likely when path and response metadata strongly match known debug interfaces
- possible when only naming suggests it

API paths:
- confirmed when JSON, OpenAPI, GraphQL, REST, or RPC behavior is observed
- likely when route names and content type point to API behavior
- possible when only frontend strings or declared docs suggest it

Do not run vulnerability probes, GraphQL introspection, or authenticated API actions from this skill unless a later approved phase covers them.

---

## Subdomain and Virtual-Host Findings

DNS name:
- confirmed when DNS returns A, AAAA, or CNAME records for the exact name
- likely when a passive source reports the name but current DNS validation is not complete
- possible when an archived source, certificate entry, or wordlist match suggests the name

Wildcard DNS:
- if random names resolve similarly, classify active DNS discovery as noisy or inconclusive
- do not treat every resolved name as confirmed unless it differs from wildcard behavior

HTTP-like host:
- confirmed when an allowed validation request receives a web response for that host
- likely when DNS resolves and prior evidence suggests web service
- possible when only vhost response differences are observed

Virtual host:
- confirmed when host-header validation produces a stable, distinct response
- likely when status, size, redirect, or title differs from default behavior
- possible when differences are weak or inconsistent

CDN or shared hosting:
- treat observations as edge-level unless origin is proven
- do not assume a missing header, default page, or shared response belongs to the origin app

---

## Noise Handling

Filter or downgrade:
- identical app-shell responses
- login-wall duplicates
- default-vhost responses
- wildcard DNS results
- status-only matches without size, title, redirect, or content-type differences
- CDN block pages
- repeated timeout patterns

Keep the report focused on candidates that survived baseline comparison.
