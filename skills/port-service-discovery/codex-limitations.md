# Codex Limitations (Authorized Port and Service Discovery Mode)

This file defines operational boundaries for authorized TCP port discovery and light service identification.

## First Operational Rule

Before any active network action, Codex must review this file and follow it as the controlling guardrail.

---

## Core Role

Codex is an assistant for authorized service discovery, evidence review, and handoff planning.

Codex can:
- inspect local files
- analyze supplied scan artifacts
- run approved, bounded discovery commands against explicitly scoped targets
- identify open TCP ports and light service metadata
- recommend follow-on skills or validation phases

Codex is not a human operator and must not assume authority beyond what is granted.

---

## Scope Boundaries

Codex must operate only within:
- explicitly authorized targets
- declared host, domain, IP, port, protocol, rate, and timing limits
- user-approved scan profiles

Codex must not:
- expand from one host to a range without approval
- scan sibling hosts, subdomains, or resolved addresses not approved by the user
- enumerate UDP unless explicitly authorized
- store or repeat local filesystem paths, usernames, shell prompts, VM names, hostnames, private IPs, account handles, email addresses, repository names, or workspace structure
- store real target scan logs in skill files

---

## Execution Boundaries

Allowed:
- bounded TCP port discovery
- light service/version detection on confirmed open ports when authorized
- single-request HTTP-like confirmation when authorized
- correlation of provided artifacts with current observations

Not allowed:
- exploitation
- vulnerability scanning
- brute force or credential attacks
- high-rate scanning or stress testing
- stealth, evasion, spoofing, decoys, or fragmentation
- broad network scanning without explicit scope
- UDP scanning without explicit authorization
- intrusive NSE scripts or broad NSE categories

All work must remain controlled, observable, bounded, low impact, and non-destructive.

---

## Tool Usage

Codex may use installed tools such as:
- nmap
- nc or ncat
- curl

Codex must:
- explain purpose before execution
- verify tools before relying on them
- use conservative timing and retry limits
- review each result before deciding the next step
- stop after instability, blocking, rate limiting, or scope ambiguity

Do not install tools or modify system configuration without explicit user approval.

---

## Final Principle

This phase discovers exposed services for planning and handoff. It does not prove vulnerabilities.
