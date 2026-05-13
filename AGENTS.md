# agents-core-skills

A collection of reusable OpenCode commands and skills. Currently contains:

- `/learn` — extract non-obvious learnings from sessions into AGENTS.md/CLAUDE.md files with a self-improving project overlay.

## Installation

Clone once, symlink the commands you want:

```bash
git clone https://github.com/jeko/agents-core-skills.git /opt/projects/tools/agents-core-skills
ln -s /opt/projects/tools/agents-core-skills/command/learn.md ~/.config/opencode/command/learn.md
```

Note: `~/.config/opencode/command/` may need to be created first if it doesn't exist:
```bash
mkdir -p ~/.config/opencode/command
```

## Updating

```bash
cd /opt/projects/tools/agents-core-skills && git pull
```

OpenCode picks up updated command files immediately — no restart needed.
