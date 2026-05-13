---
name: recon-fingerprinting
description: Use for authorized web application reconnaissance when Codex is given an explicitly in-scope target URL or domain and needs to fingerprint infrastructure, DNS, headers, technology stack, redirects, and a small set of obvious exposed endpoints without exploitation. Do not use for exploitation, credential attacks, bypassing access controls, high-rate scanning, or targets without clear authorization.
---

# Recon Fingerprinting

## Required Reads

Before any active network action:
- Read `codex-limitations.md` and follow it as the controlling guardrail.

When interpreting results or preparing the final report:
- Read `references/interpretation.md` and apply its confidence labels.
- Read `references/report-template.md` and use its standard sections.

---

## Objective

Identify, using only controlled and non-destructive reconnaissance:
- infrastructure
- technology stack
- DNS and hosting signals
- HTTP headers and redirects
- a small set of obvious exposed endpoints

Do not exploit.

---

## Authorization Gate

Proceed only when the user provides:
- target URL or domain
- confirmation the target is authorized and in scope

If scope is missing or ambiguous:
- ask for scope before active probing
- allow only passive local planning until scope is clear

Treat identity verification or platform account verification as unrelated to target authorization.

---

## Data Handling

Treat recon output as task-local evidence, not skill content.

Do not store or publish:
- command output from real targets
- cookies, bearer tokens, API keys, passwords, or session identifiers
- local filesystem paths, usernames, shell prompts, VM names, hostnames, private IPs, or build/workspace structure
- personal identifiers, account handles, or email addresses unless they are explicitly in-scope target evidence

When sensitive content is encountered:
- do not print the full value
- record only the minimum metadata needed to support the finding
- redact secrets and operator-local details from reports

---

## Execution Strategy

Work step by step.

After each step:
- review output
- extract indicators
- decide next step

Do not run all tools blindly.
Stop when the next step would no longer add meaningful fingerprinting value.

---

## Workflow

### Step 1 - Connectivity and Headers

Use:
- curl

Goal:
- confirm connectivity
- collect headers
- observe status codes
- follow at most one redirect

Start header-only:
- `curl -sS -D - -o /dev/null --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url>`

If HTTP and HTTPS both fail:
- stop active probing
- report unreachable
- continue only with DNS

---

### Step 2 - Stack Identification

Use:
- whatweb

Goal:
- identify technologies
- detect frameworks

Use low aggression:
- `whatweb -a 1 --no-errors <url>`

Skip if connectivity is unstable.

---

### Step 3 - Infrastructure

Use:
- dig
- whois

Goal:
- resolve IP
- identify provider
- review DNS

Use minimal DNS lookups:
- `dig +short <host> A`
- `dig +short <host> AAAA`
- `dig +short <host> CNAME`
- `dig +short <host> NS`

Run `whois` once for the most relevant domain or IP.

---

### Step 4 - Endpoint Discovery (Light)

Use:
- ffuf

Goal:
- identify obvious paths

Use only after the target responds and scope allows path discovery.

Use conservative ffuf defaults:
- one small, common wordlist only
- `-rate 10`
- `-t 1`
- `-timeout 5`
- no recursion
- no vhost fuzzing
- no parameter fuzzing
- no authenticated forms

Do not install or download wordlists without explicit approval.

Stop if:
- target is static
- CDN is present
- responses are limited
- many results look like the same soft-404 response

---

## Decision Logic

If response is:

- 2xx:
  proceed normally

- 3xx:
  follow redirect once, then continue

- 4xx:
  continue recon but limit endpoint discovery

- 5xx:
  stop stack and endpoint probing
  report service unavailable

---

## Tool Rules

- verify tool exists
- confirm correct tool type
- use only the tools listed in this skill unless the user explicitly authorizes another tool
- do not retry repeatedly if blocked
- do not install tools or modify system configuration without explicit approval
- do not use TLS verification bypass flags unless the user explicitly asks for TLS troubleshooting; label any such result clearly

Prefer:
- curl over httpx
- minimal commands

---

## Environment Awareness

Assume:
- filtering may occur
- CDN may mask origin
- results may be incomplete

---

## CDN Handling

If CDN detected:
- treat headers as edge-level only
- do not assume missing headers are weaknesses
- mark origin as unknown
- distinguish edge observations from origin observations

---

## Output Format

Return:

1. commands used (minimal, no duplicates)
2. scope and assumptions
3. key findings (facts only)
4. identified stack (best effort, no guessing)
5. endpoint and artifact observations
6. observed behavior (what actually happened)
7. confidence labels: confirmed, likely, possible, or inconclusive
8. limitations (clear and short)

Rules:
- do not repeat the same idea twice
- do not explain obvious headers
- avoid long lists unless meaningful
- distinguish edge/CDN observations from origin observations
- redact operator-local details and secret values
- use `references/report-template.md` when artifacts, endpoints, or candidate routes are discovered

---

## Step-Up Session

Offer a step-up session only after the initial recon report has been created and delivered.

Do not begin step-up work automatically. Ask whether the user wants to step up the session, then require explicit confirmation of:
- target URL or domain
- allowed host and path scope
- active probing limits
- excluded paths, hosts, or methods
- whether additional endpoint discovery is allowed

Step-up options for this skill:
- validate redirects, headers, TLS, DNS, CDN, and proxy behavior more thoroughly
- compare multiple user-provided hostnames or subdomains that are already in scope
- check obvious standard metadata endpoints at low volume
- produce a hardened infrastructure summary and follow-on targets for `web-app-mapping`

Step-up work must remain non-destructive reconnaissance. Do not broaden scope, run high-rate discovery, fuzz parameters, attempt credentials, or exploit findings.

If this skill is used inside an app or workflow, keep the Step Up action disabled until an initial report exists. The confirmation screen must show the proposed checks, target scope, rate limits, exclusions, and authorization requirement before execution.

---

## Rules

- stay within scope
- no exploitation
- no brute force
- no destructive actions
- no alternate network suggestions
- do not rely on prior conversation
