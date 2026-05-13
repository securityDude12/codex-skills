---
name: service-enumeration
description: Use for authorized post-scanning service enumeration when Codex has explicitly in-scope hosts, domains, or IPs plus known services/ports, and needs to perform read-only protocol-specific enumeration or correlate prior artifacts to identify service metadata, users, shares, DNS data, SNMP data, LDAP/AD objects, NFS exports, SMTP capabilities, RPC endpoints, VPN/VoIP signals, and next course of action. Do not use for broad network scanning, exploitation, credential attacks, password guessing, account harvesting, high-rate probing, bypassing access controls, or targets without clear authorization.
---

# Service Enumeration

## Required Reads

Before any active network action:
- Read `codex-limitations.md` and follow it as the controlling guardrail.

When planning or interpreting work:
- Read `references/service-recipes.md` for conservative protocol-specific command patterns.
- Read `references/interpretation.md` for confidence labels and service-specific interpretation rules.
- Read `references/triage-and-coa.md` when prioritizing services or recommending next actions.

When preparing deliverables:
- Read `references/report-template.md` and use its standard fields.

---

## Objective

Perform authorized, read-only enumeration of known services after reconnaissance or network scanning has identified targets of interest.

Produce:
- service inventory
- exposed metadata summary
- account, group, share, export, DNS, and directory observations when authorized
- misconfiguration candidates
- prioritized course of action for follow-on validation

This skill starts after `recon-fingerprinting`, `web-app-mapping`, or network scanning has identified specific in-scope assets and services. It may analyze prior artifacts without active probing, but it must not become a broad network scanning workflow.

---

## Authorization Gate

Proceed with active enumeration only when the user provides:
- explicit confirmation that the target is authorized and in scope
- target host, domain, or IP
- known service or port list, scan output, or a clearly bounded protocol target
- allowed protocols and excluded protocols
- rate limits and timing constraints
- whether anonymous, unauthenticated, or credentialed enumeration is allowed
- any supplied read-only credentials or community strings, if credentialed enumeration is requested

If scope is missing or ambiguous:
- ask for scope before active probing
- perform only passive local analysis of provided artifacts

Do not treat ownership of an account, a public IP, or a future engagement as authorization.

---

## Data Handling

Treat enumeration output as task-local evidence, not skill content.

Do not store or publish:
- command output from real targets
- scan logs, directory dumps, share listings, or service dumps from real targets
- passwords, hashes, tickets, API keys, cookies, bearer tokens, session identifiers, SNMP communities, or bind credentials
- operator-local filesystem paths, usernames, shell prompts, VM names, hostnames, private IPs, or workspace structure
- personal identifiers, account handles, or email addresses unless they are explicitly in-scope target evidence

When sensitive content is encountered:
- stop retrieving that content
- do not print full values
- record only the minimum metadata needed to support the observation
- redact secrets, credential material, and operator-local details from reports

Usernames, group names, share names, hostnames, and directory objects can be sensitive. Include them only when needed for evidence, and prefer counts, patterns, or representative redacted examples when possible.

---

## Operating Limits

Allowed:
- read-only protocol enumeration of known in-scope services
- capability and banner checks
- anonymous metadata checks when explicitly permitted
- credentialed read-only enumeration with user-supplied credentials
- low-volume DNS, LDAP, SMB, SNMP, NFS, NTP, SMTP, RPC, IPsec, and VoIP checks
- correlation of outputs from other skills or tools

Not allowed:
- broad network scanning or expanding target ranges
- exploitation or proof-of-exploit payloads
- credential attacks, password guessing, password spraying, hash capture, Kerberoasting, AS-REP roasting, or relay attacks
- username harvesting with large lists
- brute force, high-rate fuzzing, or denial-of-service activity
- bypassing access controls
- downloading or traversing file shares beyond minimal metadata confirmation
- changing directory, share, device, or service state
- using exploitation frameworks, exploit modules, or proof-of-exploit tooling
- generating custom attack automation for exploitation or credential attacks

---

## Workflow

### Step 1 - Artifact Intake

Use existing evidence first:
- recon outputs
- web mapping outputs
- network scan results
- DNS records
- user-provided asset inventories
- screenshots or logs from manual testing

Build a service table with:
- asset
- protocol and port
- evidence source
- auth state
- scope notes
- enumeration priority
- planned command or manual action

Do not re-run discovery just because prior artifacts are incomplete. Ask for scope or recommend a scanning skill if service discovery is the missing step.

### Step 2 - Scope and Tool Check

Confirm:
- target and service are in scope
- enumeration method is allowed
- rate and timing limits are clear
- required tool is installed

Use only tools needed for the current question. If a tool is missing, skip it or ask before installing anything.

### Step 3 - Passive Correlation

Before active queries, infer priorities from available data:
- identity and directory services first when authorized
- exposed file/resource services
- network management protocols
- DNS and mail services
- VPN and VoIP services
- web services only when protocol enumeration adds value beyond `web-app-mapping`

Label every inference with a confidence level from `references/interpretation.md`.

### Step 4 - Protocol-Specific Enumeration

Read `references/service-recipes.md` and run only the recipe that matches an observed and authorized service.

Default behavior:
- one host and one service at a time
- low timeouts
- no recursion
- no large wordlists
- no credential guessing
- no repeated retries after denial, blocking, or instability

When credentials or SNMP community strings are supplied:
- do not echo secrets in reports
- prefer interactive prompts or environment-safe handling when possible
- confirm the credentials are intended for read-only enumeration

### Step 5 - Evidence Review

After each command:
- identify confirmed facts
- mark possible misconfiguration candidates
- redact sensitive values
- decide whether the next query is justified

Stop active enumeration if:
- access is denied and the next step would require guessing or bypass
- the service becomes unstable
- rate limiting or blocking appears
- sensitive data is exposed beyond the minimum needed to confirm the issue
- the next step would leave the approved scope

### Step 6 - COA Synthesis

Use `references/triage-and-coa.md` to prioritize:
- likely attack paths for authorized manual validation
- high-value services and assets
- hardening gaps
- evidence that needs confirmation
- recommended next skills or testing phases

Do not call something a vulnerability unless current evidence proves the condition. Prefer `candidate`, `exposure`, `misconfiguration signal`, or `manual validation target`.

---

## Tool Rules

- verify tool existence before use
- use only service-specific read-only commands
- do not install tools, download wordlists, or modify system configuration without approval
- do not run broad `nmap -A`, UDP sweeps, host discovery, or range scans from this skill
- use `nmap` only for a known host and known port when a named safe script answers a specific enumeration question
- avoid exploitation frameworks and exploit modules for this phase
- do not use TLS verification bypass flags unless the user explicitly asks for TLS troubleshooting; label any such result clearly

---

## Output Rules

Return:

1. commands used
2. scope and assumptions
3. service inventory
4. per-service observations
5. sensitive data handling notes
6. misconfiguration candidates
7. prioritized course of action
8. confidence labels and limitations

Keep reports factual and concise. Distinguish confirmed observations from likely or possible interpretations. Redact operator-local details and secret values.

---

## Step-Up Session

Offer a step-up session only after the initial service enumeration report has been created and delivered.

Do not begin step-up work automatically. Ask whether the user wants to step up the session, then require explicit confirmation of:
- target hosts, domains, or IPs
- services and ports in scope
- allowed and excluded protocols
- auth state and whether approved read-only credentials or community strings are supplied
- rate and timing limits
- maximum depth for metadata, share, export, or directory review

Step-up options for this skill:
- perform credentialed read-only enumeration for specific services
- deepen SMB, NFS, SNMP, LDAP, DNS, SMTP, RPC, VPN, or VoIP metadata review
- validate exposed share, export, policy, or management metadata to an approved shallow depth
- correlate identity, topology, resource, mail, DNS, and management-service signals across the scoped services
- produce a prioritized course of action for follow-on manual validation or hardening review

Step-up work must remain read-only enumeration and correlation. Do not exploit, guess credentials, capture authentication material, retrieve file contents, mount filesystems, harvest accounts at scale, or expand beyond approved services.

If this skill is used inside an app or workflow, keep the Step Up action disabled until an initial report exists. The confirmation screen must show the selected findings, proposed checks, target services, auth mode, rate limits, depth limits, exclusions, and authorization requirement before execution.

---

## Rules

- stay within scope
- no exploitation
- no brute force
- no credential attacks
- no account harvesting
- no destructive actions
- no scope expansion
- do not rely on prior conversation for authorization
