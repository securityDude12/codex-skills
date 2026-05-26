---
name: web-surface-discovery
description: Use for authorized, production-aware web content discovery, route/directory refinement, exposed artifact checks, and cautious subdomain or virtual-host discovery on explicitly in-scope web targets. Use after recon-fingerprinting or during web-app-mapping when Codex needs bounded enumeration with Gobuster, ffuf, feroxbuster, curl, dig, or similar tooling. Do not use for exploitation, vulnerability fuzzing, credential attacks, brute force authentication, high-rate scanning, stealth/evasion, denial-of-service behavior, recursive runaway crawling, or targets without clear authorization.
---

# Web Surface Discovery

## Required Reads

Before any active network action:
- Read `codex-limitations.md` and follow it as the controlling guardrail.

When selecting commands or interpreting results:
- Read `references/tool-recipes.md` for conservative command patterns.
- Read `references/interpretation.md` for confidence labels and classification rules.

When preparing deliverables:
- Read `references/report-template.md` and use its inventory fields.
- Read `references/triage-and-handoff.md` for follow-up recommendations.

---

## Objective

Refine and expand the known public web surface of an authorized target without crossing into aggressive scanning or exploitation.

Produce:
- discovered routes and directories
- exposed artifact and metadata candidates
- candidate admin, debug, docs, and API surfaces
- cautious subdomain or virtual-host observations when explicitly authorized
- validation notes, confidence labels, limitations, and handoff recommendations

This skill sits between `recon-fingerprinting` and deeper `web-app-mapping`. It is narrower than full application mapping and deeper than first-pass fingerprinting.

---

## Authorization Gate

Proceed with active discovery only when the user provides:
- explicit confirmation that the target is authorized and in scope
- target URL, domain, host, or allowed base path
- approved discovery type: paths, artifacts, subdomains, virtual hosts, or a stated combination
- allowed paths, hostnames, domains, and exclusions
- approved wordlists or permission to use a small local default
- rate, thread, timeout, and recursion limits, or permission to use the skill defaults
- whether passive third-party sources, archived URLs, or certificate transparency sources are allowed
- current assessment phase and whether production-safe pacing is required

If scope is missing or ambiguous:
- ask for scope before active probing
- perform only passive local planning
- do not infer authorization from public availability, account ownership, or prior conversation

---

## Data Handling

Treat discovery output as task-local evidence, not skill content.

Do not store or publish:
- command output from real targets
- raw endpoint dumps, subdomain lists, scan logs, crawled pages, or browser exports from real targets
- cookies, bearer tokens, API keys, passwords, session identifiers, credential material, or hidden form values
- local filesystem paths, usernames, shell prompts, VM names, hostnames, private IPs, or workspace structure
- personal identifiers, account handles, or email addresses unless explicitly in-scope target evidence

When sensitive content is encountered:
- stop retrieving that content
- do not print the full value
- record only the minimum metadata needed to support the observation
- redact secrets, credential material, and operator-local details from reports

---

## Operating Limits

Allowed:
- review of standard declared paths such as `robots.txt`, `sitemap.xml`, and `.well-known/security.txt`
- bounded route and directory discovery
- bounded artifact discovery for obvious public files and build metadata
- low-volume virtual-host checks when host discovery is explicitly in scope
- cautious DNS/subdomain enumeration when the parent domain and method are explicitly approved
- validation of discovered paths with safe `GET` or `HEAD` requests
- correlation with prior `recon-fingerprinting` or `web-app-mapping` output

Not allowed:
- exploitation or proof-of-exploit payloads
- vulnerability fuzzing, parameter fuzzing, or injection probes
- credential attacks, brute force authentication, password guessing, or session manipulation
- high-rate scanning, stress testing, or denial-of-service behavior
- stealth, evasion, spoofing, decoys, or WAF bypass attempts
- recursive crawling or recursion without explicit bounded depth
- expanding to sibling domains, subdomains, IPs, cloud assets, or third-party systems without approval
- downloading large files or retrieving sensitive content beyond confirming minimal metadata
- submitting forms, mutating state, logout actions, checkout/payment actions, or admin actions

---

## Discovery Profiles

Use the smallest approved profile that answers the question.

- `declared-only`: standard declared files and linked route manifests
- `route-minimal`: small common path list, one thread, low rate, no recursion
- `artifact-minimal`: small public artifact list, one thread, low rate, no recursion
- `subdomain-passive`: approved passive sources only, no active probing beyond validation approval
- `subdomain-small`: small approved DNS wordlist, one thread, low rate, wildcard checks first
- `vhost-small`: small approved virtual-host list, one thread, low rate, only for an approved host/IP/domain
- `custom`: user-provided list, explicit bounds, and explicit exclusions

Default behavior:
- one base URL or parent domain at a time
- one small curated wordlist first
- one thread unless the user approves more
- low request rate
- short timeouts
- no recursion unless explicitly approved and bounded

---

## Workflow

### Step 1 - Scope and Baseline

Normalize:
- canonical scheme, host, port, and base path
- parent domain for subdomain work
- allowed hosts and excluded hosts
- allowed paths and excluded paths
- approved discovery types and wordlists
- rate, thread, timeout, and recursion limits

Collect a baseline:
- root or approved base path
- one definitely missing path to identify soft-404 behavior
- headers and redirect behavior
- response size patterns for successful, missing, forbidden, and redirected paths when available

Stop active discovery if the service is unstable, blocks normal requests, or scope is unclear.

### Step 2 - Select Strategy

Choose one discovery profile.

Prefer this order:
1. declared files and linked manifests
2. route and artifact discovery against the approved base URL
3. passive subdomain review if allowed
4. small active DNS or virtual-host discovery only if explicitly approved

Do not run path, DNS, and virtual-host discovery all at once. Review evidence between phases.

### Step 3 - Declared Sources

Check standard public declarations first:
- `robots.txt`
- `sitemap.xml`
- `.well-known/security.txt`
- linked route manifests, build manifests, OpenAPI files, or documentation files already referenced by HTML

Classify each observation as:
- declared
- discovered
- inferred
- archived
- user-provided

### Step 4 - Route and Artifact Discovery

Use bounded content discovery only after baseline behavior is understood.

Defaults:
- one small wordlist
- one thread
- low rate
- no recursion
- no extension explosion unless the user approves a small extension set
- no parameter fuzzing
- no form submission
- no authenticated actions unless read-only scope and credentials are explicitly provided

Pause after the first meaningful result set. Remove soft-404s, duplicate redirects, login-wall duplicates, and wildcard responses before continuing.

### Step 5 - Subdomain and Virtual-Host Discovery

Run this step only when host discovery is explicitly in scope.

For subdomains:
- check wildcard DNS first
- use passive sources only when approved
- use a small approved DNS wordlist before any larger list
- validate discovered names minimally with DNS
- confirm HTTP-like services only when allowed

For virtual hosts:
- confirm the target host or IP is in scope
- check wildcard or default-vhost behavior first
- use a small approved vhost list
- treat status, size, title, and redirect differences as signals, not proof

Do not expand active probing to newly discovered hosts unless the user confirms those hosts are in scope for follow-up.

### Step 6 - Validation and Evidence Review

For each candidate:
- validate with one low-volume request when safe
- compare against baseline behavior
- assign a confidence label
- record source, status, redirect target, content type, and short notes
- avoid retrieving sensitive content

Stop if:
- rate limiting, blocking, instability, or soft-404 noise makes results unreliable
- the next step would require a larger wordlist, more concurrency, recursion, or scope expansion
- sensitive content appears beyond what is needed to confirm exposure

### Step 7 - Synthesis and Handoff

Deduplicate by:
- canonical host
- canonical path
- method if observed
- redirect target
- content type
- parameter set if already declared

Recommend follow-up:
- `recon-fingerprinting` for newly approved web hosts or infrastructure signals
- `web-app-mapping` for route, API, auth, cookie, parameter, and artifact inventory
- `service-enumeration` for DNS or non-web service metadata
- manual review for candidate admin, debug, documentation, backup, source-map, or build-artifact exposures

Do not call a discovery a vulnerability unless the evidence proves the condition. Prefer `candidate`, `exposure signal`, `manual review target`, or `misconfiguration candidate`.

---

## Tool Rules

- verify tool existence before use
- use only tools needed for the approved discovery type
- prefer `curl`, `ffuf`, `gobuster`, `feroxbuster`, and `dig`
- do not install tools, download wordlists, or modify system configuration without approval
- do not use large wordlists unless explicitly approved
- do not use recursion unless explicitly approved and bounded
- do not retry repeatedly after denial, blocking, or instability
- do not use TLS verification bypass flags unless the user explicitly asks for TLS troubleshooting; label any such result clearly

---

## Output Rules

Return:

1. commands used
2. scope and assumptions
3. discovery profile selected
4. baseline behavior and filtering logic
5. discovered route and directory inventory
6. exposed artifact and metadata candidates
7. subdomain and virtual-host observations, if in scope
8. validation notes and confidence labels
9. sensitive data handling notes
10. limitations and recommended handoffs

Keep reports concise and factual. Distinguish confirmed observations from likely or possible interpretations. Redact operator-local details and secret values.

---

## Step-Up Session

Offer a step-up session only after the initial discovery report has been created and delivered.

Do not begin step-up work automatically. Ask whether the user wants to step up, then require explicit confirmation of:
- target URLs, hosts, domains, or parent domains
- allowed path, subdomain, and virtual-host scope
- approved wordlists
- excluded paths, hosts, and domains
- rate, thread, timeout, and recursion limits
- whether passive sources, archived URLs, or certificate transparency sources are allowed
- whether validation of newly discovered hosts is allowed

Step-up options for this skill:
- expand from `declared-only` to `route-minimal`
- expand from route discovery to artifact discovery
- run a small approved subdomain or vhost pass
- validate a selected set of candidates
- prepare a handoff package for `web-app-mapping`

Step-up work must remain bounded discovery and validation. Do not exploit, brute force, bypass controls, submit forms, retrieve sensitive content, or expand beyond approved scope.

---

## Rules

- stay within scope
- use production-safe pacing
- no exploitation
- no vulnerability fuzzing
- no credential attacks
- no destructive actions
- no stealth or evasion
- no recursive runaway scanning
- do not rely on prior conversation for authorization
