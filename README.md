# Codex Skills

AI-assisted security assessment and automation skills built with Codex CLI.

## Skills

- `port-service-discovery` - Authorized bounded TCP port discovery and light service identification for handoff to recon, mapping, or service enumeration.
- `recon-fingerprinting` - Authorized web reconnaissance for infrastructure, headers, redirects, stack indicators, DNS signals, and light endpoint discovery.
- `web-surface-discovery` - Authorized production-aware web content, artifact, subdomain, and virtual-host discovery using bounded enumeration.
- `web-app-mapping` - Authorized application-layer mapping for endpoints, APIs, parameters, cookies, auth surfaces, artifacts, and manual-testing candidates.
- `service-enumeration` - Authorized post-scanning enumeration for known services, protocol metadata, exposed resource signals, and prioritized follow-on actions.

## Layout

```text
skills/
  port-service-discovery/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  recon-fingerprinting/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  web-surface-discovery/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  web-app-mapping/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  service-enumeration/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
```

## Install Locally

Copy a skill directory into your Codex skills directory:

```bash
mkdir -p ~/.agents/skills
rsync -a skills/port-service-discovery/ ~/.agents/skills/port-service-discovery/
rsync -a skills/recon-fingerprinting/ ~/.agents/skills/recon-fingerprinting/
rsync -a skills/web-surface-discovery/ ~/.agents/skills/web-surface-discovery/
rsync -a skills/web-app-mapping/ ~/.agents/skills/web-app-mapping/
rsync -a skills/service-enumeration/ ~/.agents/skills/service-enumeration/
```

## Step-Up Sessions

Each skill starts with a bounded initial report. After that report is complete, the skill can offer a step-up session for deeper authorized review, but only after the user explicitly confirms scope, rate limits, exclusions, and allowed methods.

## Safety

These skills are written for explicitly authorized work only. Do not commit target output, scan logs, cookies, credentials, local filesystem paths, usernames, hostnames, private IPs, or personal identifiers.
