# Interpretation Guide

Use confidence labels:
- confirmed: directly observed in command output
- likely: strongly indicated by multiple signals
- possible: weak or indirect signal
- inconclusive: insufficient, timed out, filtered, or conflicting evidence

---

## Port States

Open:
- confirmed only when the scanner or single-port check reports the port open
- service name may still be a guess unless version detection or protocol response confirms it

Closed:
- confirmed closed only for the tested target and timing profile
- not a statement about other hosts, protocols, or later service state

Filtered:
- possible firewall, network filtering, packet loss, or scan timing issue
- do not treat as proof that no service exists

Open|filtered:
- inconclusive unless corroborated
- do not retry repeatedly

Timeout or DNS failure:
- inconclusive for service exposure
- report the failure and avoid guessing

---

## Service Labels

Nmap service names:
- likely when inferred by port number alone
- confirmed only when protocol behavior or version output supports the label

Version strings:
- confirmed as observed strings
- possible risk only until manually validated against reliable evidence and patch context

HTTP-like services:
- confirmed when a header or valid HTTP response is observed
- likely when nmap identifies `http`, `http-proxy`, or related service but no HTTP confirmation was run

TLS services:
- possible when a port convention suggests TLS
- confirmed only when TLS or HTTPS behavior is directly observed

---

## Safety Signals

Stop or slow down when output suggests:
- rate limiting
- packet loss or unstable responses
- service resets after probing
- unexpected 5xx behavior on HTTP-like services
- user scope no longer matches observed targets

Do not claim a vulnerability from:
- an open port alone
- a banner alone
- a filtered state
- a missing service on one timing profile
