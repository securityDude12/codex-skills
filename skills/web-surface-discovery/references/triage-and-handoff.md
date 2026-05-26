# Triage and Handoff

Use this guide after the initial discovery report.

## Prioritization

Prioritize follow-up in this order:
1. exposed sensitive artifact candidates
2. admin, debug, status, or documentation surfaces
3. API, OpenAPI, GraphQL, and developer portal surfaces
4. newly discovered web hosts explicitly approved for follow-up
5. protected paths that indicate application structure
6. low-confidence or noisy findings only if they affect scope decisions

Do not deepen a finding if the next step requires exploitation, credential attacks, form submission, high-rate probing, or scope expansion.

---

## Handoff to recon-fingerprinting

Use `recon-fingerprinting` when:
- a newly discovered host is explicitly approved for follow-up
- headers, redirects, TLS, DNS, CDN, or hosting signals need a focused pass
- the surface-discovery result is host-level rather than application-route-level

Provide:
- host or URL
- evidence source
- validation status
- scope constraints
- whether CDN or wildcard behavior was observed

---

## Handoff to web-app-mapping

Use `web-app-mapping` when:
- route inventory needs API, parameter, cookie, or auth mapping
- frontend JavaScript or build manifests suggest additional routes
- admin, docs, OpenAPI, GraphQL, or debug candidates need application-layer inventory
- discovered routes need relationship mapping rather than more enumeration

Provide:
- canonical route list
- source and confidence per route
- observed auth requirements
- artifacts and manifests
- exclusions and rate limits

---

## Handoff to service-enumeration

Use `service-enumeration` when:
- DNS, mail, LDAP, SMB, SNMP, NFS, RPC, VPN, or VoIP metadata requires protocol-specific review
- subdomain work identifies non-web services that are explicitly approved
- the next question is about service metadata rather than web routes

Provide:
- known host or domain
- known service or port evidence
- allowed protocols
- auth state
- rate and timing limits

---

## Manual Review Targets

Recommend manual review for:
- source maps
- backup-like files
- public configuration examples
- OpenAPI, Swagger, Redoc, GraphiQL, or GraphQL Playground surfaces
- admin interfaces
- debug consoles
- status, health, or metrics pages
- build manifests that reveal route or package structure

Phrase these as candidates unless the evidence proves exposure and sensitivity.

---

## Stop or Ask

Stop and ask for confirmation when:
- moving from routes to subdomains or virtual hosts
- increasing wordlist size
- increasing rate, threads, or recursion depth
- validating newly discovered hosts
- using third-party passive sources
- inspecting sensitive-looking files beyond minimal metadata
- authenticated discovery is requested

Do not use a step-up session as implied permission. Require explicit scope confirmation.
