# Service Recipes

Use only the recipe that matches an observed, in-scope service. Prefer the smallest command that answers the current question.

Verify tools with:

```bash
command -v dig host nslookup nmap nc ncat openssl smbclient rpcclient smbmap enum4linux-ng snmpwalk snmpget ldapsearch rpcinfo showmount ntpq ntpdate ike-scan svmap swaks ldns-walk
```

General rules:
- run one host and one protocol at a time
- avoid broad script categories
- avoid unauthenticated enumeration unless explicitly permitted
- do not use wordlists for users, passwords, shares, or communities
- do not print secrets in commands, logs, or reports
- stop after denial, instability, or rate limiting

---

## DNS

Use when a domain is explicitly in scope.

Baseline records:

```bash
dig +short <domain> NS
dig +short <domain> MX
dig +short <domain> TXT
dig +short <domain> SOA
dig +short <domain> CAA
```

Authoritative query:

```bash
dig @<nameserver> <domain> SOA
```

Zone transfer check only when DNS enumeration and AXFR attempts are explicitly allowed:

```bash
dig @<nameserver> <domain> AXFR
```

DNSSEC/NSEC walking only when explicitly allowed and tooling is installed:

```bash
ldns-walk <domain>
```

Avoid DNS cache snooping against third-party resolvers unless the resolver is in scope and the user explicitly authorizes that check.

---

## SMB and NetBIOS

Use for known Windows/SMB services such as 139/tcp or 445/tcp.

Protocol and signing metadata:

```bash
nmap -Pn -p445 --script smb-protocols,smb-security-mode,smb2-security-mode,smb2-time <host>
```

Anonymous share listing only when anonymous SMB enumeration is explicitly allowed:

```bash
smbclient -L //<host>/ -N -m SMB3
```

Credentialed share listing only with user-supplied read-only credentials:

```bash
smbclient -L //<host>/ -U '<domain>/<user>' -m SMB3
```

Anonymous/null-session RPC metadata only when anonymous SMB/RPC enumeration is explicitly allowed:

```bash
rpcclient -U "" -N <host> -c srvinfo
```

Use `enum4linux-ng` or `smbmap` only when the user explicitly authorizes broader SMB enumeration. Do not download files, traverse shares, enumerate large user lists, or test passwords.

---

## SNMP

Use for known SNMP services such as 161/udp. A community string is credential material.

Do not guess community strings or try lists. Use only a user-supplied community string, or a single default-community check if the user explicitly authorizes that exact check.

System metadata:

```bash
snmpwalk -v2c -c '<community>' -t 3 -r 1 <host> 1.3.6.1.2.1.1
```

Interface metadata:

```bash
snmpwalk -v2c -c '<community>' -t 3 -r 1 <host> 1.3.6.1.2.1.2.2.1
```

Single OID:

```bash
snmpget -v2c -c '<community>' -t 3 -r 1 <host> <oid>
```

Avoid dumping process tables, routing tables, installed software, or user data unless explicitly required and approved.

---

## LDAP and Active Directory

Use for known LDAP/LDAPS services such as 389/tcp, 636/tcp, or 3268/tcp.

Anonymous RootDSE query:

```bash
ldapsearch -x -LLL -H ldap://<host> -s base -b "" namingContexts defaultNamingContext dnsHostName supportedLDAPVersion supportedSASLMechanisms
```

LDAPS RootDSE query:

```bash
ldapsearch -x -LLL -H ldaps://<host> -s base -b "" namingContexts defaultNamingContext dnsHostName supportedLDAPVersion supportedSASLMechanisms
```

Credentialed base query with user-supplied read-only bind credentials:

```bash
ldapsearch -LLL -H ldap://<host> -D '<binddn>' -W -b '<baseDN>' -s one '(objectClass=*)' dn -z 100
```

Targeted AD policy query after base DN is known:

```bash
ldapsearch -LLL -H ldap://<host> -D '<binddn>' -W -b '<baseDN>' '(objectClass=domainDNS)' minPwdLength lockoutThreshold maxPwdAge -z 20
```

Do not perform Kerberoasting, AS-REP roasting, password spraying, BloodHound collection, DCSync, LDAP writes, or large directory dumps from this skill.

---

## NFS and RPC

Use for known RPC/NFS services such as 111/tcp, 111/udp, or 2049/tcp.

RPC program list:

```bash
rpcinfo -p <host>
```

NFS exports:

```bash
showmount -e <host>
```

Do not mount remote exports unless the user explicitly authorizes mounting, the mount is read-only, and the test environment can safely handle it. Do not traverse or retrieve file contents beyond minimal metadata confirmation.

---

## NTP

Use for known NTP services such as 123/udp.

Basic query:

```bash
ntpq -c rv <host>
```

Peer summary:

```bash
ntpq -p <host>
```

Time query:

```bash
ntpdate -q <host>
```

Avoid `monlist` or amplification-prone checks. Stop if the service rejects mode 6 queries or shows instability.

---

## SMTP

Use for known mail services such as 25/tcp, 465/tcp, or 587/tcp.

Capabilities with `swaks` when installed:

```bash
swaks --server <host> --port <port> --quit-after EHLO
```

STARTTLS service metadata for ports such as 25 or 587:

```bash
openssl s_client -connect <host>:<port> -starttls smtp -crlf
```

Implicit TLS service metadata for port 465:

```bash
openssl s_client -connect <host>:465 -crlf
```

Nmap SMTP capabilities for a known host and port:

```bash
nmap -Pn -p<port> --script smtp-commands <host>
```

Do not run automated SMTP user enumeration, VRFY/EXPN lists, RCPT probing, or mailbox harvesting unless the user gives explicit written authorization and a tiny supplied list of approved test identifiers.

---

## IPsec and IKE

Use for known VPN/IKE services such as 500/udp or 4500/udp.

Transform and vendor hints:

```bash
ike-scan -M <host>
```

Do not capture hashes, attempt aggressive-mode PSK cracking, enumerate usernames, or test credentials from this skill.

---

## VoIP and SIP

Use for known SIP services such as 5060/tcp, 5060/udp, or 5061/tcp.

Known SIP endpoint mapping for a specific approved port:

```bash
svmap -p <port> <host>
```

SIP method metadata with Nmap for a known UDP SIP port:

```bash
nmap -Pn -sU -p<port> --script sip-methods <host>
```

Do not enumerate extensions, brute force registrations, place calls, test voicemail, or attempt authentication.

---

## HTTP Services

For web services, prefer `web-app-mapping`.

Use this skill only when HTTP is acting as a management or protocol-adjacent service and the check is read-only:

```bash
curl -sS -D - -o /dev/null --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url>
```

Do not fuzz paths or parameters here. Hand off to `web-app-mapping` when route inventory is needed.

---

## Other Exposed Services

For services such as FTP, SSH, RDP, database listeners, Redis, Memcached, Elasticsearch, or message queues:
- collect banners or capability metadata only when explicitly in scope
- do not attempt login unless credentials and permission are supplied
- do not run default-password checks
- do not read, write, dump, or modify data

Use named, read-only checks and document exactly what was observed.
