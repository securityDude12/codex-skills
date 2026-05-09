# Recon Report Template

Use this structure for the final report when fingerprinting a target. Keep it concise and evidence-driven.

## Commands Used

List unique commands only. Omit repeated retries unless they changed the result.
Redact tokens, cookies, credentials, local filesystem paths, usernames, hostnames, private IPs, and shell prompts.

## Scope and Assumptions

Include:
- target
- allowed host/path scope
- active probing limits
- unavailable tools
- whether DNS, headers, stack ID, and light endpoint discovery were performed

## Key Findings

Summarize confirmed facts first:
- service reachability
- status and redirect behavior
- server or CDN indicators
- major framework or hosting signals
- obvious exposed endpoints or artifacts

## Identified Stack

Recommended fields:

| Component | Evidence | Confidence |
| --- | --- | --- |

Examples:
- web server
- language/runtime
- frontend framework
- backend framework
- database hint
- CDN/WAF
- analytics or third-party services

## Endpoint and Artifact Observations

Recommended fields:

| Path | Type | Status | Source | Evidence | Confidence |
| --- | --- | --- | --- | --- | --- |

Types:
- page
- API
- auth
- admin
- status
- docs
- static asset
- build artifact
- config or metadata
- unknown

Do not retrieve or print sensitive artifact contents. Record only enough to support the observation.
Do not include operator-local environment details or unrelated personal identifiers.

## Observed Behavior

Include:
- redirects
- status code patterns
- cookies
- notable headers
- soft-404 or directory-listing behavior
- rate limiting, blocking, or instability

## Confidence and Limitations

Use:
- confirmed
- likely
- possible
- inconclusive

List only meaningful limitations, such as auth not tested, CDN masking, excluded paths, unstable responses, or low-volume discovery limits.
