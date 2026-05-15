# agents-core-skills

Reusable OpenCode commands (`/learn`). See README.md for install and usage.

## Adding commands

- New commands are `.md` files under `command/` with a YAML front matter `description` field (see `command/learn.md` for the format)
- Symlink each command into `~/.config/opencode/command/` to make it available to OpenCode — no restart needed
- No build, test, or lint steps exist in this repo

## Install / Update

```bash
git clone https://github.com/j3k0/agents-core-skills.git /opt/projects/tools/agents-core-skills
mkdir -p ~/.config/opencode/command
ln -s /opt/projects/tools/agents-core-skills/command/learn.md ~/.config/opencode/command/learn.md
```

Update: `cd /opt/projects/tools/agents-core-skills && git pull`
