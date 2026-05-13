# Codex Skills

Personal Codex skill library.

## Skills

- `recon-fingerprinting` - Authorized web reconnaissance for infrastructure, headers, redirects, stack indicators, DNS signals, and light endpoint discovery.
- `service-enumeration` - Authorized post-scanning enumeration for known services, protocol metadata, exposed resource signals, and prioritized follow-on actions.
- `web-app-mapping` - Authorized application-layer mapping for endpoints, APIs, parameters, cookies, auth surfaces, artifacts, and manual-testing candidates.

## Layout

```text
skills/
  recon-fingerprinting/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  service-enumeration/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
  web-app-mapping/
    SKILL.md
    agents/openai.yaml
    codex-limitations.md
    references/
```

## Install Locally

Copy a skill directory into your Codex skills directory:

```bash
mkdir -p ~/.agents/skills
rsync -a skills/recon-fingerprinting/ ~/.agents/skills/recon-fingerprinting/
rsync -a skills/service-enumeration/ ~/.agents/skills/service-enumeration/
rsync -a skills/web-app-mapping/ ~/.agents/skills/web-app-mapping/
```

## Safety

These skills are written for explicitly authorized work only. Do not commit target output, scan logs, cookies, credentials, local filesystem paths, usernames, hostnames, private IPs, or personal identifiers.
