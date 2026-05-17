# Port Service Discovery Report Template

Use this structure for final reports. Keep it concise and evidence-driven.

## Commands Used

List unique commands only. Omit repeated retries unless they changed the result.

Redact:
- credentials, tokens, cookies, and session identifiers
- local filesystem paths, usernames, shell prompts, VM names, private IPs, and workspace structure

## Scope and Assumptions

Include:
- target hosts, domains, or IPs
- allowed port profile, list, or range
- excluded ports, protocols, or hosts
- TCP or UDP authorization
- rate and timing limits
- whether service/version detection was allowed
- whether HTTP-like confirmation was allowed
- unavailable tools

## Scan Profile Selected

Recommended fields:

| Profile | Ports | Reason | Approved By Scope |
| --- | --- | --- | --- |

## Port and Service Inventory

Recommended fields:

| Target | Protocol | Port | State | Service | Evidence | Confidence |
| --- | --- | --- | --- | --- | --- | --- |

## HTTP-Like Service Notes

Recommended fields:

| Target | Port | Scheme Tested | Status/Behavior | Header Signal | Confidence |
| --- | --- | --- | --- | --- | --- |

## Closed, Filtered, and Inconclusive Observations

Summarize patterns instead of dumping every closed port unless the list is short and meaningful.

Recommended fields:

| Target | Ports | Observation | Interpretation | Confidence |
| --- | --- | --- | --- | --- |

## Candidate Follow-Up Actions

Recommended fields:

| Priority | Action | Rationale | Required Authorization |
| --- | --- | --- | --- |

## Handoff Recommendations

Recommended fields:

| Service or Finding | Recommended Skill or Phase | Reason |
| --- | --- | --- |

## Confidence and Limitations

Use:
- confirmed
- likely
- possible
- inconclusive

List meaningful limitations:
- low-volume sampling
- excluded ports or protocols
- UDP not tested
- service detection not authorized
- filtered or unstable responses
- scan profile was not full-range
