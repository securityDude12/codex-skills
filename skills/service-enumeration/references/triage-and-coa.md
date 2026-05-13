# Triage and Course of Action

Use this guide after artifact intake or after each active enumeration step.

## Prioritization

Prioritize services that can expose identity, access, topology, or configuration data:

1. LDAP/AD, Kerberos-adjacent services, SMB/RPC
2. SNMP and network management
3. NFS and file/resource sharing
4. DNS and mail infrastructure
5. VPN/IPsec and VoIP
6. Web management interfaces, only when not already covered by `web-app-mapping`
7. Generic banners and low-signal services

Adjust priority based on:
- production criticality
- external exposure
- authentication state
- sensitivity of returned metadata
- presence of default, anonymous, or broadly scoped access
- user-provided testing goals

---

## Service Signals and COA

LDAP/AD:
- RootDSE only: map naming context and stop unless broader LDAP enumeration is authorized.
- Anonymous object reads: recommend scoped directory exposure validation.
- Readable password policy: recommend identity policy review, not password attacks.
- Credentialed read-only access: enumerate only approved object classes and summarize counts.

SMB/NetBIOS:
- Anonymous session denied: record access control and stop.
- Guest or anonymous listing allowed: validate share visibility and permissions only to the approved depth.
- Signing disabled: mark as hardening candidate and recommend relay-resistance review.
- Legacy SMB versions: recommend protocol hardening validation.

SNMP:
- Community accepted: identify whether access is read-only, then collect minimal system/interface metadata.
- Default community accepted: confirmed management exposure if explicitly authorized to test.
- Sensitive OIDs visible: recommend SNMP hardening and access control review.

NFS/RPC:
- Export list visible: review client restrictions and export options.
- Broad export scope: candidate misconfiguration.
- Mounting needed for impact: request explicit authorization and use read-only handling.

DNS:
- AXFR success: confirmed zone transfer exposure; recommend restricting zone transfers.
- Misconfigured SPF/DMARC/DKIM/CAA: candidate mail/domain hardening gap.
- DNSSEC zone walking: candidate information disclosure depending on records exposed.

SMTP:
- EHLO capabilities only: record service metadata.
- VRFY/EXPN enabled: candidate user enumeration surface if explicitly tested.
- AUTH advertised without TLS: possible transport hardening issue.

NTP:
- Mode 6 responses with peer/system details: candidate metadata exposure.
- Amplification-prone behavior: do not stress test; recommend defensive validation.

IPsec/IKE:
- Vendor and transform data: record metadata.
- Weak transforms: candidate VPN hardening gap.
- PSK capture or cracking: out of scope for this skill.

VoIP/SIP:
- SIP service visible: record methods and auth challenge behavior.
- Extension enumeration: out of scope for this skill unless a separate authorized VoIP test covers it.

---

## Attack Path Reasoning

Keep attack path reasoning bounded and non-operational.

Good:
- "SMB guest share listing plus LDAP naming context exposure makes identity and access review a high-priority follow-on."
- "SNMP read access can expose topology; validate ACLs and community configuration."

Avoid:
- step-by-step exploitation
- credential capture or cracking steps
- lateral movement instructions
- persistence, evasion, or bypass guidance

---

## Recommended Follow-On Phases

Recommend only phases that fit the evidence:

- `recon-fingerprinting` for missing infrastructure context
- `web-app-mapping` for HTTP route and API inventory
- network scanning skill for missing service discovery, if one exists and scope permits
- vulnerability analysis for version and configuration risk review
- AD security review for authorized directory and identity testing
- SMB/NFS permissions review for authorized file-share validation
- mail security review for SMTP, SPF, DKIM, and DMARC validation
- reporting/remediation planning after evidence is sufficient

---

## Stop or Escalate Conditions

Stop and ask for authorization when the next step would:
- use credentials not already approved
- query a protocol not in scope
- enumerate accounts at scale
- mount filesystems
- retrieve file contents
- capture hashes, tickets, or challenge material
- test relay, spoofing, or open relay behavior
- change service state
- increase request rate materially
