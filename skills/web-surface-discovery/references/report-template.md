# Report Template

Use this structure for web surface discovery reports.

## Commands Used

List minimal commands only. Omit duplicates and redact secrets or operator-local details.

## Scope and Assumptions

Include:
- approved targets
- approved discovery types
- excluded paths, hosts, or domains
- rate, thread, timeout, and recursion limits
- approved wordlists or source type
- whether passive sources, archived URLs, or certificate transparency sources were allowed

## Discovery Profile

State the selected profile:
- declared-only
- route-minimal
- artifact-minimal
- subdomain-passive
- subdomain-small
- vhost-small
- custom

Explain why it was selected in one sentence.

## Baseline and Filtering

Record:
- baseline status codes
- redirect behavior
- soft-404 or app-shell behavior
- wildcard DNS or default-vhost behavior
- filtering logic used to remove noise

## Route and Directory Inventory

Use this table shape:

| Host | Path | Status | Source | Confidence | Notes |
| --- | --- | --- | --- | --- | --- |

Source examples:
- declared
- discovered
- inferred
- archived
- user-provided

## Artifact and Metadata Candidates

Use this table shape:

| Host | Path | Type | Status | Confidence | Notes |
| --- | --- | --- | --- | --- | --- |

Type examples:
- build-manifest
- source-map
- openapi
- docs
- status
- backup-candidate
- config-example
- framework-metadata
- debug-candidate

## Subdomain and Virtual-Host Observations

Use this table only when host discovery is in scope:

| Name | Type | Evidence | Validation | Confidence | Notes |
| --- | --- | --- | --- | --- | --- |

Type examples:
- dns-name
- cname
- http-host
- virtual-host
- wildcard-noise

## Validation Notes

Summarize:
- candidates validated
- candidates filtered out
- requests intentionally not made
- sensitive content avoided

## Sensitive Data Handling

State whether sensitive content appeared. If yes, describe only minimal metadata and redaction applied.

## Limitations

Include:
- scope limits
- wordlist limits
- rate/concurrency limits
- CDN, WAF, soft-404, wildcard DNS, or default-vhost uncertainty
- unauthenticated-only limitations if applicable

## Recommended Handoffs

List next steps:
- `recon-fingerprinting` for newly approved web hosts
- `web-app-mapping` for route, API, auth, cookie, parameter, and artifact inventory
- `service-enumeration` for DNS or non-web protocol metadata
- manual review for candidate admin, debug, backup, source-map, docs, or build-artifact exposures
