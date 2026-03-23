# sec-skills

This repo stores security-focused skills for AI coding agents such as Claude, Cursor, and Codex.

It is structured as a single Claude plugin marketplace repo and a conventional `skills/` repo so it can be used with both Claude Code plugins and the `skills` CLI.

## Included skill

### skill-scanner-scans

Use this skill to:
- check whether `skill-scanner` is already installed
- install or reuse the PyPI package with `pip`
- discover AI skill and instruction artifacts across supported scopes
- scan those artifacts for malicious patterns and suspicious behavior

Start with [`skills/skill-scanner-scans/SKILL.md`](skills/skill-scanner-scans/SKILL.md).

## Claude marketplace

Add the marketplace:

```text
/plugin marketplace add thedevappsecguy/sec-skills
```

Install the plugin:

```text
/plugin install sec-skills@sec-skills-marketplace
```

For private validation, use a local path instead:

```text
/plugin marketplace add .
```

## skills CLI

List available skills:

```bash
npx skills add thedevappsecguy/sec-skills --list
```

Install a specific skill:

```bash
npx skills add thedevappsecguy/sec-skills --skill skill-scanner-scans
```

For private validation, use the local repo path instead:

```bash
npx skills add . --list
```

Public GitHub visibility is required for unauthenticated public installs. While the repo is private, use a local path or authenticated GitHub access.

## Structure

```text
.claude-plugin/
  marketplace.json
  plugin.json
skills/
  skill-scanner-scans/
```
