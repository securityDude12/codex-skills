# Tool Recipes

Use only tools that are installed and in scope. Prefer the smallest command that answers the current question.

Verify tools with:

```bash
command -v curl ffuf feroxbuster katana gau waybackurls httpx nuclei
```

---

## curl

Baseline headers:

```bash
curl -sS -D - -o /dev/null --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url>
```

Fetch a small body for route classification:

```bash
curl -sS --location --max-redirs 1 --connect-timeout 5 --max-time 15 <url>
```

Check robots or sitemap:

```bash
curl -sS --connect-timeout 5 --max-time 15 <base>/robots.txt
curl -sS --connect-timeout 5 --max-time 15 <base>/sitemap.xml
```

Use `-I` only when the target handles HEAD normally. Some apps route HEAD differently from GET.

---

## ffuf

Use for bounded content discovery.

Conservative defaults:

```bash
ffuf -u <base>/FUZZ -w <small-wordlist> -rate 10 -t 1 -timeout 5 -mc all
```

After observing baseline noise, filter carefully:

```bash
ffuf -u <base>/FUZZ -w <small-wordlist> -rate 10 -t 1 -timeout 5 -fs <soft404-size>
```

Avoid:
- recursion unless explicitly bounded
- vhost fuzzing unless scope includes host discovery
- parameter fuzzing during mapping
- large wordlists without approval

---

## feroxbuster

Use only when ffuf is insufficient and scope permits crawling-style content discovery.

Conservative defaults:

```bash
feroxbuster -u <base> -w <small-wordlist> --rate-limit 10 --threads 1 --depth 1 --timeout 5 --dont-extract-links
```

Enable link extraction only when the user authorizes broader crawling and the target is stable.

---

## katana

Use for safe crawling and link extraction.

Conservative defaults:

```bash
katana -u <base> -d 2 -rate-limit 5 -timeout 10 -silent
```

Stay within the scoped host and path. Do not submit forms unless explicitly authorized.

---

## gau and waybackurls

Use only when archived URLs are allowed by scope.

```bash
gau <host>
waybackurls <host>
```

Treat results as archived, not confirmed live endpoints. Re-check only promising in-scope paths at low rate.

---

## httpx

Use ProjectDiscovery `httpx`, not unrelated tools with the same name.

```bash
httpx -u <url> -status-code -title -tech-detect -follow-redirects -max-redirects 1 -timeout 10 -rate-limit 10
```

If `httpx` is missing or is the Python HTTP client CLI, skip it.

---

## nuclei

Use only safe, non-intrusive templates when explicitly useful for discovery, such as exposed panels or tech detection.

Rules:
- no exploit templates
- no brute force templates
- no intrusive checks
- no template download without approval
- review selected templates before running when possible

Example pattern:

```bash
nuclei -u <url> -tags panel,tech,exposure -severity info,low -rate-limit 5
```

Label nuclei results as tool findings that require manual confirmation.

---

## Burp Suite

Use Burp as a manual proxy workflow when available:
- define target scope first
- browse only allowed paths
- disable active scanning for this phase
- use Target, HTTP history, Site map, and Logger to export endpoints
- preserve methods, parameters, cookies, and auth redirects

Do not run active scan or intruder attacks during mapping.

---

## Browser Automation

Use a browser only for scoped, low-volume observation:
- load pages as a normal user would
- do not submit forms unless authorized
- record network requests and redirects
- capture frontend route changes
- avoid destructive UI actions

Prefer browser evidence for SPA routes and auth redirects that command-line tools cannot see cleanly.
