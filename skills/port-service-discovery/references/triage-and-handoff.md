# Triage and Handoff

Use this guide after discovery results are available.

## Prioritization

Prioritize follow-up by:
1. externally exposed web services
2. admin or management interfaces
3. identity, file-sharing, or remote-access services
4. database or queue services exposed outside expected boundaries
5. unknown services with consistent open-port evidence
6. filtered or inconclusive results that matter to the user's objective

Open ports are not vulnerabilities by themselves. Treat them as attack-surface inventory until follow-on validation proves risk.

---

## Recommended Handoffs

HTTP or HTTPS:
- `recon-fingerprinting` for headers, redirects, DNS, stack, and hosting signals
- `web-app-mapping` for routes, APIs, auth flows, cookies, parameters, and artifacts

Known non-web services:
- `service-enumeration` for protocol-specific read-only metadata and capability checks

Unknown services:
- request approval for light service detection if not already performed
- otherwise recommend manual validation with a bounded plan

Filtered ports:
- report as inconclusive
- ask before changing timing, retry, or scan profile

Multiple resolved addresses:
- ask whether each address is in scope before scanning them individually

---

## Follow-Up Language

Prefer:
- "confirmed open service"
- "likely HTTP-like service"
- "possible management interface"
- "candidate for protocol-specific enumeration"
- "manual validation target"

Avoid:
- "confirmed vulnerability" unless separate evidence proves it
- exploitation steps
- credential attack suggestions
- bypass or evasion guidance

---

## Step-Up Conditions

Ask for explicit authorization before:
- increasing the port range
- scanning multiple hosts or resolved addresses
- running full-range TCP discovery
- running UDP discovery
- running service/version detection if it was not already approved
- adding HTTP confirmation for discovered web-like ports
- adjusting timing in a way that materially increases traffic
