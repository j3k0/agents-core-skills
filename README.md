# agents-core-skills

Reusable OpenCode commands and skills.

## Commands

| Command | Description | Symlink |
|---------|-------------|---------|
| `/learn` | Extract non-obvious learnings from a session into AGENTS.md/CLAUDE.md files, with a self-improving project overlay | `command/learn.md` |

## Install

Clone once, then symlink the commands you want:

```bash
git clone https://github.com/j3k0/agents-core-skills.git /opt/projects/tools/agents-core-skills
mkdir -p ~/.config/opencode/command
ln -s /opt/projects/tools/agents-core-skills/command/learn.md ~/.config/opencode/command/learn.md
```

OpenCode picks up new commands immediately — no restart needed.

## Update

```bash
cd /opt/projects/tools/agents-core-skills && git pull
```

## Adding commands

Add new `.md` files under `command/` with a YAML front matter `description` field. Symlink them into `~/.config/opencode/command/` to make them available.