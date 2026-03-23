---
name: skill-scanner-scans
description: Inspect the current environment, reuse or install the PyPI package `skill-scanner` with `pip`, and scan AI skills, prompts, instructions, agents, and rules for malicious behavior. Use this skill whenever the user mentions `skill-scanner`, `skillscan`, `pip install skill-scanner`, malicious AI skills, suspicious prompt or instruction files, or scanning Codex, Claude, Cursor, Copilot, Gemini, Windsurf, Cline, or OpenCode skill artifacts.
---

# Skill Scanner Scans

## Overview

Use this skill to set up or reuse the published `skill-scanner` CLI, discover AI skill and instruction artifacts first, and then scan selected targets for malicious behavior. Prefer direct CLI execution after install, and keep the default output human-readable.

## Workflow

1. Inspect the operating system, Python launchers, Python version, current directory, and whether `skill-scanner` or `skillscan` already works.
2. Reuse an existing working CLI when present.
3. Select the best Python launcher for the platform and confirm it meets the package requirement of Python 3.11 or newer.
4. Install with user-level `pip` only when the CLI is missing or clearly unusable.
5. Verify the CLI with `skill-scanner --help` first and `skillscan --help` as fallback.
6. Check analyzer readiness with `doctor`.
7. Run discovery before any live scan.
8. If the user already pointed to a specific skill file or folder, scan that target after discovery confirms it.
9. If the user did not specify a target, present discovered skills and let the user choose what to scan before running a live scan.
10. If no analyzer is configured, still show what is discoverable without keys and stop with the exact blocker.

## Default Behavior

Act instead of only describing:

1. Detect the platform and available Python launchers before choosing commands.
2. Prefer these launcher choices:
   - Windows: `py -3.11`, then `py`, then `python`
   - macOS and Linux: `python3`, then `python`
3. Check whether `skill-scanner` or `skillscan` is already on `PATH`.
4. If the CLI is missing, install with the selected launcher using `-m pip install --user skill-scanner`.
5. If the install succeeds but the CLI is not on `PATH`, derive the user scripts directory from the selected interpreter, run the binary by absolute path, and explain the PATH fix.
6. Run `skill-scanner doctor` after setup.
7. Run discovery first with `skill-scanner discover --format table` or `skill-scanner scan --list-targets`.
8. If the user provided a specific file or folder, confirm it appears in discovery or is the intended direct target, then run `skill-scanner scan --path <target> --format summary`.
9. If the user did not provide a target, summarize the discovered skills and ask which one to scan before running the live scan.
10. If analyzer configuration does not exist, stop after discovery and report that live scanning is blocked until an analyzer is configured.
11. After any live scan, provide clear and actionable remediation for each meaningful finding.

Do not assume `uv`. This skill intentionally uses `pip`.

## Environment Checks

Inspect these first:

- operating system and shell context
- current directory and whether it is inside a git repository
- `skill-scanner --help`
- `skillscan --help`
- Python launcher availability and version
- whether `SKILLSCAN_MODEL`, `SKILLSCAN_API_KEY`, `SKILLSCAN_BASE_URL`, or `VT_API_KEY` are set

If the user provided a specific file or folder, prefer `--path <target>` after discovery. Otherwise, rely on the tool's default broad discovery across repo, user, system, and extension scopes, then let the user choose which discovered skill to scan.

## Analyzer Decision

- Treat LLM analysis as available only when `SKILLSCAN_MODEL` is set and either `SKILLSCAN_API_KEY` or `SKILLSCAN_BASE_URL` is present.
- Treat VirusTotal analysis as available when `VT_API_KEY` is present.
- Treat live scanning as blocked when neither analyzer is configured.
- When live scanning is blocked, continue with `discover` or `scan --list-targets` so the user can still see the candidate artifacts.
- Do not skip target selection when multiple discovered skills are in scope and the user did not name one.

## Default Scan Flow

Use this order:

1. Reuse or install the CLI.
2. Verify the entrypoint works.
3. Run `doctor`.
4. If keys may be valid and the user wants connectivity checked, run `doctor --check`.
5. Run `discover --format table` or `scan --list-targets` before any live scan.
6. If the user explicitly named a skill file or folder and at least one analyzer is configured, run `scan --path <target> --format summary`.
7. If discovery returns multiple candidate skills and the user did not specify one, present the list and ask the user which target to scan.
8. If discovery returns one obvious target and the user's intent is clearly to scan it, proceed and state that inference.
9. If no analyzer is configured, stop after discovery and explain the configuration blocker.

Prefer `skill-scanner` as the main command name. Use `skillscan` only when the primary entrypoint is unavailable.

## Output Pattern

Report progress in this order:

1. What was found in the environment.
2. Whether the CLI was reused or installed.
3. Which Python launcher was selected.
4. Whether the CLI worked directly or required an absolute scripts-directory path.
5. Whether analyzers were configured.
6. Which skills or artifacts were discovered.
7. Which target was selected and why.
8. What scan or discovery command was run.
9. The findings or the exact blocker.
10. Clear remediation for each real finding, including what to remove, restrict, verify, or rewrite.

## Reference Use

Load [references/install-and-usage.md](references/install-and-usage.md) when you need:

- exact Windows, macOS, or Linux install commands
- user-level scripts-directory and PATH troubleshooting
- exact `doctor`, `discover`, `scan`, `--list-targets`, `--fail-on`, or `--output` command patterns
- target-selection and path-scoped scan patterns
- analyzer environment variables and config file locations
