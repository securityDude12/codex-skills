# Report Template

Use this structure for the final mapping report. Keep it concise and evidence-driven.

## Commands Used

List unique commands only. Omit repeated retries unless they changed the result.
Redact tokens, cookies, credentials, local filesystem paths, usernames, hostnames, private IPs, and shell prompts.

## Scope and Assumptions

Include:
- target
- allowed host/path scope
- auth state tested
- rate/depth bounds
- whether archived URLs were used
- exclusions or unavailable tools

## Endpoint Inventory

Recommended fields:

| Method | Path | Type | Source | Status | Auth State | Parameters | Evidence | Confidence |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |

Use `GET` only when observed or declared. Use `unknown` when method was not established.

## Parameter Map

Recommended fields:

| Endpoint | Location | Name | Observed Value Shape | Source | Notes |
| --- | --- | --- | --- | --- | --- |

Locations:
- query
- path
- body
- header
- cookie
- fragment

Do not include secrets or full token values.
Do not include operator-local environment details or unrelated personal identifiers.

## Cookie and Header Map

For cookies, include:
- name
- domain/path when observed
- Secure
- HttpOnly
- SameSite
- expiration/session
- source endpoint

For headers, include only relevant security, auth, CORS, framework, and caching headers.

## Artifact Inventory

Recommended fields:

| Path | Artifact Type | Source | Status | Sensitivity | Review Action | Confidence |
| --- | --- | --- | --- | --- | --- | --- |

Artifact types:
- OpenAPI or API docs
- GraphQL UI or schema hint
- build manifest
- JavaScript bundle
- source map
- debug trace
- status output
- config example
- backup
- database file
- log
- metadata

Sensitivity:
- public: intended public artifact
- internal-hint: names, routes, or stack details but no secret values observed
- sensitive-candidate: may contain secrets or private data; do not print values
- unknown: not enough evidence

Review actions:
- record only
- map linked endpoints
- manual review
- stop and ask user before retrieving more

Do not print sensitive artifact contents. Record only enough metadata to support the observation.

## Auth Surface Map

Group by flow:
- login
- logout
- registration
- password reset
- invitation
- SSO/OAuth
- MFA
- session refresh
- account management

For each flow, include observed endpoints, redirects, cookies, and candidate manual-testing areas.

## Technology and Artifact Summary

Include:
- confirmed stack indicators
- likely framework or CMS indicators
- exposed docs or manifests
- source map or build artifact observations
- AI-generated stack indicators when relevant

## Candidate Manual-Testing Areas

List only areas supported by observed evidence.

Examples:
- auth flow consistency
- session cookie hardening
- frontend-only authorization assumptions
- API authorization and IDOR review
- input validation review
- CORS review
- GraphQL configuration review
- exposed debug or admin interface review

## Limitations

Include:
- tools unavailable
- paths excluded by scope
- auth state not tested
- soft-404 or SPA ambiguity
- CDN/WAF/rate-limit effects
- archived URLs not live-confirmed
