# Codex Skills

Personal Codex skill library.

## Skills

- `recon-fingerprinting` - Authorized web reconnaissance for infrastructure, headers, redirects, stack indicators, DNS signals, and light endpoint discovery.

## Layout

```text
skills/
  recon-fingerprinting/
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
```

## Safety

These skills are written for explicitly authorized work only. Do not commit target output, scan logs, cookies, credentials, local filesystem paths, usernames, hostnames, private IPs, or personal identifiers.
