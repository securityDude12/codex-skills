# Interpretation Guide

Translate raw enumeration output into evidence-driven observations.

Use confidence labels:
- confirmed: directly observed in command output
- likely: strongly indicated by multiple signals
- possible: weak or indirect signal
- inconclusive: insufficient or conflicting evidence

---

## General

Open service:
- confirmed only when the queried host and port respond
- do not infer adjacent hosts, ranges, or related services

Access denied:
- confirmed access control behavior for that query
- not proof that the service is secure

Timeout:
- possible filtering, UDP loss, host firewall, service issue, or wrong protocol
- do not retry repeatedly

Version strings:
- confirmed as observed strings
- possible version risk only until manually validated against reliable evidence

---

## DNS

AXFR success:
- confirmed zone transfer exposure for the queried zone and nameserver

AXFR refused:
- confirmed refusal for that nameserver
- not proof that all nameservers refuse

DNSSEC NSEC chain:
- possible zone walking exposure when records allow traversal

SPF, DMARC, DKIM, and CAA:
- hardening signals, not application vulnerabilities by themselves

---

## SMB and NetBIOS

Guest or anonymous access:
- confirmed only when the session succeeds
- possible exposure if shares or metadata are listable

SMB signing disabled:
- possible hardening gap
- do not claim relay exploitability without separate validation and scope

Share list visible:
- confirmed share metadata exposure
- do not treat share existence as sensitive data exposure unless contents or permissions prove it

User or group names visible:
- sensitive identity metadata
- include only what is needed for evidence

---

## SNMP

Accepted community:
- confirmed SNMP read access for that community and OID scope
- treat the community value as secret

System, interface, route, process, or software data:
- possible sensitive management information exposure
- do not collect more than needed to confirm scope and impact

SNMP timeout:
- inconclusive for UDP unless corroborated

---

## LDAP and Active Directory

RootDSE anonymous response:
- confirmed LDAP service metadata exposure
- usually expected behavior; not a vulnerability by itself

Anonymous directory object read:
- possible directory information exposure
- validate scope and sensitivity before collecting more

Password policy attributes:
- confirmed policy metadata when directly returned
- do not infer password attack feasibility from policy alone

Large user, group, or computer dumps:
- avoid by default
- if explicitly authorized, summarize counts and patterns

---

## NFS and RPC

RPC program list:
- confirmed RPC service exposure

NFS export list:
- confirmed export metadata exposure
- possible misconfiguration if exports are world-readable or broadly scoped

Mount access:
- do not test unless explicitly authorized
- file contents are out of default scope

---

## NTP

Mode 6 response:
- confirmed NTP control query response
- possible metadata exposure depending on returned fields

Peer list:
- possible infrastructure relationship disclosure

Rejected query:
- confirmed hardening or filtering for that query

---

## SMTP

EHLO capabilities:
- confirmed mail service capabilities
- STARTTLS, AUTH, SIZE, and PIPELINING are service features, not vulnerabilities alone

VRFY or EXPN enabled:
- possible user enumeration exposure only if explicitly tested and confirmed

Open relay:
- do not test from this skill unless explicitly authorized by a later mail security test

---

## IPsec and IKE

Vendor ID or transform response:
- confirmed IKE metadata exposure

Weak transforms:
- possible VPN hardening gap
- do not claim exploitability without policy context and validation

Aggressive mode hash capture:
- not allowed in this skill

---

## VoIP and SIP

SIP method response:
- confirmed SIP service behavior

Extension enumeration:
- not allowed in this skill

Registration challenge:
- confirmed auth surface
- do not attempt credentials

---

## COA Language

Prefer:
- candidate manual validation target
- possible hardening gap
- confirmed metadata exposure
- follow-on validation recommended

Avoid:
- confirmed vulnerability unless the evidence directly proves it
- exploit path unless the action is authorized and validated
- assumptions about impact from a banner alone
