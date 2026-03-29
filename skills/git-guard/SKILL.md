---
name: git-guard
description: Set up Claude Code hooks to block dangerous git commands before they execute, including `git push`, `git reset --hard`, `git clean`, `git branch -D`, and checkout or restore of the working tree. Use this skill when the user wants to prevent destructive git operations, protect a repo from accidental pushes or resets, add git safety hooks, or make git commands safer.
---

# Git Guard

Set up a `PreToolUse` hook that intercepts and blocks dangerous git commands before Claude executes them.

## What Gets Blocked

- `git push`, including force variants
- `git reset --hard`
- `git clean -f` and `git clean -fd`
- `git branch -D`
- `git checkout .` and `git restore .`

When blocked, Claude sees a message explaining that it does not have authority to run the command.

## Steps

### 1. Ask scope

Ask whether to install for this project only in `.claude/settings.json` or for all projects in `~/.claude/settings.json`.

### 2. Copy the hook script

The bundled script is at [scripts/git-guard.sh](scripts/git-guard.sh).

Prerequisite: `jq` must be installed.

Copy it to the target location based on scope:

- Project: `.claude/hooks/git-guard.sh`
- Global: `~/.claude/hooks/git-guard.sh`

Make it executable with `chmod +x`.

### 3. Add the hook to settings

Add this to the appropriate settings file.

Project scope:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/git-guard.sh"
          }
        ]
      }
    ]
  }
}
```

Global scope:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/git-guard.sh"
          }
        ]
      }
    ]
  }
}
```

If the settings file already exists, merge the hook into the existing `hooks.PreToolUse` array instead of overwriting other settings.

### 4. Ask about customization

Ask whether to add or remove any blocked patterns, then edit the copied script accordingly.

### 5. Verify

Run a quick test:

```bash
echo '{"tool_input":{"command":"git push origin main"}}' | <path-to-script>
```

The script should exit with code `2` and print a `BLOCKED` message to stderr.
