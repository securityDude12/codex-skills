# Tool Recipes

Use these recipes as conservative starting points. Adjust only inside the approved scope.

## Tool Check

Check only tools needed for the approved discovery type:

```bash
command -v curl ffuf gobuster feroxbuster dig
```

Skip missing tools unless the user approves installation.

---

## Baseline Requests

Collect headers and response size:

```bash
curl -sS -D - -o /dev/null --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url>
```

Fetch a small body only when needed for classification:

```bash
curl -sS --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url> | head -c 4096
```

Soft-404 calibration:

```bash
curl -sS -D - -o /dev/null --connect-timeout 5 --max-time 15 <base>/not-found-<random-token>
```

Use `HEAD` only when the target handles it normally. Some apps route `HEAD` differently from `GET`.

---

## Declared Files

Request standard declared files one at a time:

```bash
curl -sS -D - --connect-timeout 5 --max-time 15 <base>/robots.txt
curl -sS -D - --connect-timeout 5 --max-time 15 <base>/sitemap.xml
curl -sS -D - --connect-timeout 5 --max-time 15 <base>/.well-known/security.txt
```

Do not follow declared paths outside approved scope.

---

## ffuf Route Discovery

Use for bounded content discovery.

Baseline first:

```bash
ffuf -u <base>/FUZZ -w <small-wordlist> -rate 10 -t 1 -timeout 5 -mc all
```

If a soft-404 size is confirmed:

```bash
ffuf -u <base>/FUZZ -w <small-wordlist> -rate 10 -t 1 -timeout 5 -mc all -fs <soft404-size>
```

Avoid:
- recursion
- auto-calibration without reviewing what it filters
- vhost fuzzing unless host discovery is explicitly in scope
- extension fuzzing unless the user approves a small extension set
- large wordlists without approval
- parameter fuzzing

---

## Gobuster Route Discovery

Use when Gobuster is preferred or ffuf is unavailable:

```bash
gobuster dir -u <base> -w <small-wordlist> -t 1 --delay 100ms --timeout 5s --no-error
```

With a small approved extension set:

```bash
gobuster dir -u <base> -w <small-wordlist> -x txt,json,xml -t 1 --delay 100ms --timeout 5s --no-error
```

Use extensions only when they are relevant to the assessment. Do not use broad extension lists.

---

## Feroxbuster Route Discovery

Use only when recursive-style discovery is explicitly approved and bounded:

```bash
feroxbuster -u <base> -w <small-wordlist> --rate-limit 10 --threads 1 --depth 1 --timeout 5 --dont-extract-links
```

Do not increase depth or enable link extraction unless explicitly approved.

---

## Artifact Checks

Use a small artifact list or individual checks for obvious public files:

```bash
ffuf -u <base>/FUZZ -w <artifact-wordlist> -rate 10 -t 1 -timeout 5 -mc all
```

Typical artifact categories:
- build manifests
- source maps
- OpenAPI or Swagger files
- public docs
- status or health pages
- backup-like filenames
- framework metadata files

If a candidate appears sensitive, stop after confirming minimal metadata. Do not retrieve full contents.

---

## DNS and Subdomain Discovery

Run only when subdomain discovery is explicitly in scope.

Check wildcard DNS first:

```bash
dig +short <random-token>.<domain> A
dig +short <random-token>.<domain> AAAA
```

Small active DNS pass with Gobuster:

```bash
gobuster dns -d <domain> -w <small-wordlist> -t 1 --delay 200ms --timeout 5s --no-error
```

Validate a discovered name minimally:

```bash
dig +short <name> A
dig +short <name> AAAA
dig +short <name> CNAME
```

Do not actively probe newly discovered hosts unless the user confirms they are in scope.

---

## Virtual-Host Discovery

Run only when virtual-host discovery is explicitly in scope.

Check default behavior first with one random host header:

```bash
curl -sS -D - -o /dev/null --connect-timeout 5 --max-time 15 -H "Host: <random-token>.<domain>" <scheme>://<host-or-ip>/
```

Small vhost pass with ffuf:

```bash
ffuf -u <scheme>://<host-or-ip>/ -H "Host: FUZZ.<domain>" -w <small-wordlist> -rate 5 -t 1 -timeout 5 -mc all
```

Small vhost pass with Gobuster:

```bash
gobuster vhost -u <scheme>://<host-or-ip> -w <small-wordlist> -t 1 --delay 200ms --timeout 5s --no-error
```

Treat differences in status, size, title, and redirects as signals that need validation.

---

## Validation

Validate candidates one at a time:

```bash
curl -sS -D - -o /dev/null --location --max-redirs 1 --connect-timeout 5 --max-time 15 <candidate-url>
```

If minimal body inspection is needed:

```bash
curl -sS --location --max-redirs 1 --connect-timeout 5 --max-time 15 <candidate-url> | head -c 2048
```

Do not retrieve large files, secrets, exports, archives, backups, or user data.

---

## Stop Conditions

Stop active discovery when:
- rate limiting or blocking appears
- response behavior becomes unstable
- results are dominated by soft-404s, wildcard DNS, or default-vhost noise
- sensitive data appears
- the next useful action requires a larger wordlist, more concurrency, recursion, or scope expansion
- the target falls outside approved scope
