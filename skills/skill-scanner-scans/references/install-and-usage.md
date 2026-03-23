# `skill-scanner` Install and Usage Reference

## Scope

Use this reference when the task needs exact `pip`-based install commands, CLI verification commands, analyzer configuration details, or troubleshooting for user-level installs.

## Sections

- Requirements
- Launcher Selection
- Windows Install
- macOS and Linux Install
- Verify the CLI
- User Scripts Directory and PATH Fixes
- Analyzer Configuration
- Discovery and Scan Commands
- Target Selection
- Output and Exit Controls

## Requirements

- Install from PyPI: `skill-scanner`
- Python requirement: 3.11 or newer
- Live scans require at least one analyzer:
  - LLM: `SKILLSCAN_MODEL` plus `SKILLSCAN_API_KEY` or `SKILLSCAN_BASE_URL`
  - VirusTotal: `VT_API_KEY`

## Launcher Selection

Choose the first launcher that exists and meets the Python requirement.

Windows preference order:

```powershell
py -3.11 --version
py --version
python --version
```

macOS and Linux preference order:

```bash
python3 --version
python --version
```

## Windows Install

Preferred install:

```powershell
py -3.11 -m pip install --user skill-scanner
```

Fallback when `py -3.11` is unavailable but `py` already resolves to Python 3.11+:

```powershell
py -m pip install --user skill-scanner
```

Fallback when only `python` is usable and it resolves to Python 3.11+:

```powershell
python -m pip install --user skill-scanner
```

## macOS and Linux Install

Preferred install:

```bash
python3 -m pip install --user skill-scanner
```

Fallback when only `python` is usable and it resolves to Python 3.11+:

```bash
python -m pip install --user skill-scanner
```

## Verify the CLI

Prefer the main entrypoint:

```bash
skill-scanner --help
```

Fallback alias:

```bash
skillscan --help
```

Check analyzer readiness:

```bash
skill-scanner doctor
```

Run live connectivity checks only when configuration exists and the user wants validation:

```bash
skill-scanner doctor --check
```

## User Scripts Directory and PATH Fixes

If `pip --user` installation succeeds but the command is not found, derive the scripts directory from the selected interpreter.

Windows:

```powershell
$userBase = py -3.11 -m site --user-base
$scriptsDir = Join-Path $userBase 'Scripts'
Get-ChildItem $scriptsDir
& (Join-Path $scriptsDir 'skill-scanner.exe') --help
```

Fallback for a different launcher:

```powershell
$userBase = py -m site --user-base
$scriptsDir = Join-Path $userBase 'Scripts'
```

macOS and Linux:

```bash
user_base="$(python3 -m site --user-base)"
scripts_dir="${user_base}/bin"
ls "$scripts_dir"
"$scripts_dir/skill-scanner" --help
```

Tell the user to add the derived scripts directory to `PATH` for future direct use.

## Analyzer Configuration

Environment variables:

```bash
SKILLSCAN_MODEL=openai/gpt-5.4
SKILLSCAN_API_KEY=your-llm-key
VT_API_KEY=your-vt-key
```

Local-model example:

```bash
SKILLSCAN_MODEL=ollama/llama3.1
SKILLSCAN_BASE_URL=http://localhost:11434
```

Config files:

- project-local: `./skill-scanner.toml`
- user-level: `~/.config/skill-scanner/config.toml`

Live scan rules:

- LLM analysis requires `SKILLSCAN_MODEL` and either `SKILLSCAN_API_KEY` or `SKILLSCAN_BASE_URL`.
- VT analysis requires `VT_API_KEY`.
- `scan` is blocked when neither analyzer is configured.

## Discovery and Scan Commands

Discover likely targets across default scopes:

```bash
skill-scanner discover --format table
```

List targets without running analyzers:

```bash
skill-scanner scan --list-targets
```

Run against an explicit path:

```bash
skill-scanner scan --path /absolute/path/to/target --format summary
```

Show detailed diagnostics:

```bash
skill-scanner discover --verbose --format table
skill-scanner scan --verbose --format summary
```

## Target Selection

Always discover first, then choose the scan target.

Recommended flow:

1. Run discovery across the default scopes.
2. If the user already provided a skill file or folder, confirm that target and scan it with `--path`.
3. If multiple skills are discovered and the user did not specify a target, present the discovered list and ask which skill to scan.
4. If only one obvious skill is discovered and the user's intent is to scan it, proceed and state that inference.
5. Do not run a broad live scan across every discovered skill by default unless the user explicitly asks for that.

Example commands:

```bash
skill-scanner discover --format table
skill-scanner scan --list-targets
skill-scanner scan --path /absolute/path/to/target --format summary
```

## Output and Exit Controls

Write results to a file:

```bash
skill-scanner scan --format json --output skill-scanner-results.json
skill-scanner scan --format sarif --output skill-scanner-results.sarif
```

Fail on higher-severity findings:

```bash
skill-scanner scan --fail-on high --format summary
skill-scanner scan --min-severity medium --format summary
```

Useful output formats:

- `table`
- `summary`
- `json`
- `sarif`

When reporting results, include actionable remediation such as:

- remove or rewrite suspicious shell commands that download remote payloads
- delete hidden exfiltration instructions or secret-harvesting steps
- restrict overly broad file access, network access, or credential use
- pin and verify external downloads instead of executing mutable remote content
- re-scan the updated skill after remediation to confirm the risk is gone
