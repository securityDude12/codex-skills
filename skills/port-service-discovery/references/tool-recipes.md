# Tool Recipes

Use only the smallest command that answers the approved discovery question.

Verify tools:

```bash
command -v nmap nc ncat curl
```

---

## TCP Discovery

Minimal web ports:

```bash
nmap -Pn -sT -p80,443,8080,8443 --max-retries 2 --host-timeout 60s --scan-delay 100ms <host>
```

Common web and dev ports:

```bash
nmap -Pn -sT -p80,443,3000,5000,5173,8000,8080,8081,8443,8888,9000 --max-retries 2 --host-timeout 90s --scan-delay 100ms <host>
```

Custom approved ports:

```bash
nmap -Pn -sT -p<ports> --max-retries 2 --host-timeout 120s --scan-delay 100ms <host>
```

Top 100 TCP ports only when explicitly authorized:

```bash
nmap -Pn -sT --top-ports 100 --max-retries 2 --host-timeout 180s --scan-delay 100ms <host>
```

Full TCP port scans require explicit authorization and clear timing limits. Prefer a smaller profile first.

---

## Light Service Detection

Use only against confirmed open ports, and only when service/version detection is approved:

```bash
nmap -Pn -sT -sV --version-light -p<open_ports> --max-retries 2 --host-timeout 120s <host>
```

Do not add `-A` or broad script categories.

---

## Single-Port Confirmation

If `nmap` output is ambiguous and a single port needs confirmation:

```bash
nc -vz -w 5 <host> <port>
```

or:

```bash
ncat -vz -w 5 <host> <port>
```

Use this sparingly. Do not loop over large port lists with `nc` or `ncat`.

---

## HTTP-Like Confirmation

After HTTP-like ports are discovered and confirmation is authorized:

```bash
curl -sS -D - -o /dev/null --connect-timeout 5 --max-time 15 http://<host>:<port>/
```

For likely HTTPS ports:

```bash
curl -sS -D - -o /dev/null --connect-timeout 5 --max-time 15 https://<host>:<port>/
```

Do not use TLS verification bypass flags unless the user explicitly asks for TLS troubleshooting. Do not crawl or fuzz from this skill.

---

## Banned Patterns

Do not run:

```bash
nmap -A <host>
nmap --script vuln <host>
nmap --script exploit <host>
nmap --script brute <host>
nmap --script auth <host>
nmap --script dos <host>
nmap --script intrusive <host>
nmap -sU <host>
nmap -D <decoys> <host>
nmap -f <host>
nmap --source-port <port> <host>
```

UDP scans, full-range scans, and multi-host scans require explicit approval and should remain outside the default path.
