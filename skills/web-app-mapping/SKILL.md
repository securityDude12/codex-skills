---
name: web-app-mapping
description: Use for authorized web application mapping and endpoint discovery when Codex is given an explicitly in-scope target URL or domain and needs to inventory routes, APIs, parameters, cookies, auth surfaces, exposed build artifacts, GraphQL hints, admin/debug interfaces, frontend-discovered endpoints, and technology indicators for later manual testing. Do not use for exploitation, credential attacks, bypassing access controls, high-rate scanning, generic network scanning, or targets without clear authorization.
---

# Web App Mapping

## Required Reads

Before any active network action:
- Read `codex-limitations.md` and follow it as the controlling guardrail.

When selecting tools or interpreting results:
- Read `references/tool-recipes.md` for conservative command patterns.
- Read `references/interpretation.md` for confidence labels and classification rules.

When preparing deliverables:
- Read `references/report-template.md` and use its inventory fields.

---

## Objective

Map the application layer of an authorized web target without exploitation.

Produce:
- endpoint inventory
- API and GraphQL surface map
- parameter, cookie, and header map
- authentication and session surface map
- technology and exposed artifact summary
- candidate manual-testing areas

This skill starts where `recon-fingerprinting` ends. Use prior fingerprinting output when available, but verify facts from current command output.

---

## Authorization Gate

Proceed only when the user provides:
- target URL or domain
- confirmation the target is authorized and in scope
- any scope constraints, such as path prefixes, excluded paths, allowed methods, rate limits, auth state, or whether archived URLs are allowed

If scope is missing or ambiguous:
- ask for scope before active probing
- do only passive local planning until scope is clear

Do not treat account ownership, identity verification, or a possible future engagement as authorization.

---

## Data Handling

Treat mapping output as task-local evidence, not skill content.

Do not store or publish:
- command output from real targets
- endpoint dumps, scan logs, archived URL lists, crawled pages, or browser exports from real targets
- cookies, bearer tokens, API keys, passwords, session identifiers, or credential material
- local filesystem paths, usernames, shell prompts, VM names, hostnames, private IPs, or build/workspace structure
- personal identifiers, account handles, or email addresses unless they are explicitly in-scope target evidence

When sensitive content is encountered:
- stop retrieving that content
- do not print the full value
- record only the minimum metadata needed to support the observation
- redact secrets and operator-local details from reports

---

## Operating Limits

Stay application-focused.

Allowed:
- content discovery with conservative limits
- safe crawling
- robots and sitemap review
- frontend HTML and JavaScript endpoint extraction
- API route discovery
- GraphQL endpoint detection without introspection unless explicitly authorized
- auth flow observation without login attempts unless credentials and permission are provided
- request and response metadata analysis

Not allowed:
- exploitation or proof-of-exploit payloads
- credential attacks
- brute force or high-rate fuzzing
- bypassing access controls
- destructive methods unless explicitly authorized
- parameter fuzzing for vulnerabilities
- recursive crawling without bounded depth and rate
- nuclei templates that attempt exploitation or intrusive checks

---

## Workflow

### Step 1 - Scope and Baseline

Normalize the target:
- canonical scheme and host
- allowed paths and exclusions
- authenticated or unauthenticated context
- rate limit and recursion bounds
- whether robots.txt should be honored as a hard boundary

Collect baseline responses:
- root path
- one known application path if supplied
- headers and redirects
- cookies set before authentication

Stop active probing if the service is unstable, unavailable, or clearly blocks normal requests.

### Step 2 - Declared Routes

Check only standard declaration files first:
- `robots.txt`
- `sitemap.xml`
- `.well-known/security.txt`
- framework manifests or build manifests when linked from HTML

Classify each route as:
- declared
- discovered
- inferred
- archived

### Step 3 - Crawl and Link Extraction

Use safe crawling within scope:
- start from the canonical URL
- limit depth
- limit rate
- avoid form submission unless explicitly authorized
- avoid logout, delete, update, checkout, payment, and admin actions

Extract:
- anchor links
- form actions and methods
- script and asset URLs
- JavaScript-discovered paths
- API-like strings
- route manifests

Record source evidence for each endpoint.

### Step 4 - Content Discovery

Use content discovery only after baseline and declared routes are understood.

Defaults:
- one small wordlist
- low rate
- one thread
- no recursion unless explicitly needed and bounded
- no vhost fuzzing unless scope includes host discovery
- no parameter fuzzing unless the user explicitly asks and scope permits

Stop if soft-404 behavior, CDN blocking, rate limiting, or noisy wildcard responses make results unreliable.

### Step 5 - API and GraphQL Surface

Look for:
- `/api`, `/api/v1`, `/v1`, `/graphql`, `/graphiql`, `/swagger`, `/openapi.json`, `/docs`, `/redoc`
- JSON responses
- CORS and auth-related headers
- frontend fetch/XHR route strings
- OpenAPI or GraphQL references

For GraphQL:
- detect endpoint presence with safe requests only
- do not run introspection unless explicitly authorized
- label GraphiQL, GraphQL Playground, Apollo Sandbox, and similar UIs as debug/interface exposure candidates

### Step 6 - Auth and Session Surface

Observe without attacking:
- login, registration, reset, invite, SSO, MFA, logout, session, refresh-token, and account endpoints
- cookies, SameSite, Secure, HttpOnly, domain, path, and expiration attributes
- redirects between unauthenticated and authenticated areas
- frontend-only route guards or visible role checks

Do not attempt credentials, bypasses, lockout testing, MFA testing, or token manipulation unless a later skill and explicit authorization cover that phase.

### Step 7 - Technology and Build Artifacts

Classify:
- frameworks and libraries
- CMS and plugins
- frontend bundlers
- cloud and serverless signals
- AI-generated stack indicators such as template naming, generated comments, exposed `.env` references, vibe-coded API glue patterns, or default scaffolding routes

Check for exposed artifacts only by requesting obvious paths:
- source maps
- static manifests
- build metadata
- config examples
- public docs
- debug or status pages

Do not retrieve secrets or sensitive data beyond confirming a path exists. If sensitive content appears, stop and report minimally.

### Step 8 - Synthesis

Deduplicate by canonical path, method, and parameter set.

For each finding, include:
- confidence label
- evidence source
- auth requirement if observed
- candidate manual-testing area
- limitations

Recommend next skills only as preparation, such as:
- authentication-testing
- session-analysis
- input-validation-testing
- idor-testing
- api-security-testing
- ai-web-vulnerability-analysis
- web-report-generation

---

## Output Rules

Return:

1. commands used
2. scope and assumptions
3. endpoint inventory
4. parameter, cookie, and header map
5. auth surface map
6. artifact inventory
7. technology and artifact summary
8. candidate manual-testing areas
9. confidence labels and limitations

Keep the report factual. Distinguish confirmed observations from likely or possible interpretations.

Do not call something a vulnerability unless the current phase actually proves it. Prefer phrases such as `candidate area`, `manual testing target`, or `possible hardening gap`.
Redact operator-local details and secret values.
