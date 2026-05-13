# Service Enumeration Report Template

Use this structure for final reports. Keep it concise and evidence-driven.

## Commands Used

List unique commands only. Omit repeated retries unless they changed the result.

Redact:
- passwords
- SNMP communities
- LDAP bind credentials
- hashes, tickets, tokens, cookies, and session identifiers
- operator-local filesystem paths, usernames, shell prompts, hostnames, private IPs, and workspace structure

## Scope and Assumptions

Include:
- target assets
- services and ports in scope
- protocols excluded from scope
- auth state: anonymous, unauthenticated, or credentialed
- rate and timing limits
- unavailable tools
- source artifacts used

## Service Inventory

Recommended fields:

| Asset | Service | Port | Auth State | Evidence Source | Confidence |
| --- | --- | --- | --- | --- | --- |

## Observations

Recommended fields:

| Service | Observation | Evidence | Sensitivity | Confidence |
| --- | --- | --- | --- | --- |

Sensitivity values:
- low: routine banner or capability metadata
- moderate: topology, policy, share, export, or service relationship metadata
- high: identity data, host inventory, device configuration, sensitive object names
- secret: credential material, tokens, hashes, keys, or private content; redact and stop

## Misconfiguration Candidates

Recommended fields:

| Candidate | Affected Service | Why It Matters | Evidence | Confidence |
| --- | --- | --- | --- | --- |

Use candidate language unless the condition is directly proven.

## Prioritized Course of Action

Recommended fields:

| Priority | Action | Rationale | Required Authorization |
| --- | --- | --- | --- |

Examples:
- validate SMB share permissions
- review SNMP ACLs and community configuration
- confirm DNS zone transfer restrictions
- perform AD security review with approved credentials
- hand off HTTP services to `web-app-mapping`

## Confidence and Limitations

Use:
- confirmed
- likely
- possible
- inconclusive

List meaningful limitations:
- authentication not tested
- UDP loss or filtering
- CDN/proxy masking
- excluded protocols
- read-only limits
- missing tools
- low-volume sampling
- sensitive data intentionally not retrieved
