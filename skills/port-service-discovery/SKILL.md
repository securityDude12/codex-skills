---
name: port-service-discovery
description: Use for authorized, bounded TCP port discovery and light service identification on explicitly in-scope hosts, domains, or IPs before handing results to recon-fingerprinting, web-app-mapping, or service-enumeration. Do not use for exploitation, vulnerability scanning, credential attacks, broad range scanning, UDP scanning without explicit approval, stealth/evasion, high-rate probing, or targets without clear authorization.
---

# Port Service Discovery

## Required Reads

Before any active network action:
- Read `codex-limitations.md` and follow it as the controlling guardrail.

When selecting commands or interpreting results:
- Read `references/tool-recipes.md` for conservative command patterns.
- Read `references/interpretation.md` for confidence labels and scan-result handling.

When preparing deliverables:
- Read `references/report-template.md` and use its inventory fields.
- Read `references/triage-and-handoff.md` for next-step recommendations.

---

## Objective

Discover open TCP ports and basic service metadata for explicitly authorized, bounded targets.

Produce:
- port and service inventory
- scan profile and scope summary
- open, closed, filtered, and inconclusive observations
- HTTP-like service notes when confirmed with low-volume checks
- handoff recommendations for follow-on skills

This skill fills the gap between web recon and protocol-specific service enumeration. It discovers services; it does not validate vulnerabilities.

---

## Authorization Gate

Proceed with active discovery only when the user provides:
- explicit confirmation that the target is authorized and in scope
- target host, domain, or IP
- allowed port list, port range, or named scan profile
- excluded hosts, ports, or protocols
- rate and timing limits, or permission to use the skill defaults
- whether light service/version detection is allowed
- whether HTTP-like service confirmation is allowed

If scope is missing or ambiguous:
- ask for scope before active probing
- perform only passive local planning

Do not treat account ownership, public availability, or a future engagement as authorization.

---

## Scan Profiles

Use the smallest approved profile that answers the question.

- `web-minimal`: `80,443,8080,8443`
- `web-common`: `80,443,3000,5000,5173,8000,8080,8081,8443,8888,9000`
- `top-limited`: top 100 TCP ports only, when explicitly authorized
- `custom`: user-provided TCP port list or range

Default behavior:
- one host at a time
- TCP only
- no UDP unless explicitly authorized
- no full `-p-` scan unless explicitly authorized with timing limits
- no network ranges unless explicitly authorized and tightly bounded

---

## Workflow

### Step 1 - Scope and Tool Check

Confirm:
- target is in scope
- port profile is approved
- excluded ports and protocols are understood
- rate and timing limits are clear
- required tools are installed

Use:
- `nmap`
- `nc` or `ncat`
- `curl`

Skip missing tools unless the user approves installation.

### Step 2 - Target Normalization

If a domain is supplied:
- resolve it only as needed for the approved scan
- do not expand to sibling hosts, subdomains, or address ranges

If multiple A/AAAA records exist:
- ask whether all returned addresses are in scope before scanning multiple resolved addresses
- otherwise scan the hostname as provided

### Step 3 - Bounded TCP Discovery

Run one conservative TCP discovery command using the approved profile.

Review output before any follow-up. Identify:
- open ports
- closed ports
- filtered or timed-out ports
- scan instability or blocking

Stop or ask for guidance if responses indicate instability, rate limiting, or scope ambiguity.

### Step 4 - Light Service Identification

Run light service/version detection only when:
- open ports were found
- the user authorized version/service detection
- the command is limited to confirmed open ports

Do not run exploit or vulnerability scripts.

### Step 5 - HTTP-Like Confirmation

When open ports appear HTTP-like and HTTP confirmation is authorized:
- send one header-only `curl` request per HTTP-like port
- use short timeouts
- do not crawl, fuzz, submit forms, or follow broad redirects

Hand HTTP services to `recon-fingerprinting` or `web-app-mapping` for deeper app-layer work.

### Step 6 - Synthesis and Handoff

Deduplicate results by host, protocol, port, and service label.

Recommend:
- `recon-fingerprinting` for web infrastructure and headers
- `web-app-mapping` for route, API, auth, and artifact inventory
- `service-enumeration` for protocol-specific metadata on confirmed services

---

## Prohibited Behavior

Do not:
- exploit or run proof-of-exploit checks
- run vulnerability NSE categories
- brute force, password guess, or test credentials
- run broad network scans without explicit scope
- use stealth, evasion, spoofing, decoys, fragmentation, or source-port tricks
- perform DoS-style, high-rate, or stress testing
- scan UDP unless explicitly authorized
- run `nmap -A`
- run broad script categories such as `default`, `vuln`, `exploit`, `brute`, `auth`, `dos`, or `intrusive`
- install tools, download lists, or modify system configuration without approval

---

## Output Rules

Return:

1. commands used
2. scope and assumptions
3. scan profile selected
4. port and service inventory
5. HTTP-like service notes
6. closed, filtered, and inconclusive observations
7. candidate follow-up actions
8. handoff recommendations
9. confidence labels and limitations

Keep reports concise and factual. Distinguish confirmed observations from likely or possible interpretations. Redact operator-local details and secret values.

---

## Step-Up Session

Offer a step-up session only after the initial discovery report has been created and delivered.

Do not begin step-up work automatically. Ask whether the user wants to step up, then require explicit confirmation of:
- target hosts, domains, or IPs
- allowed ports or ranges
- excluded ports, protocols, and hosts
- TCP or UDP authorization
- service/version detection authorization
- rate and timing limits
- whether HTTP-like confirmation is allowed

Step-up work must remain bounded discovery and handoff preparation. Do not exploit, brute force, use intrusive scripts, or expand beyond approved scope.
