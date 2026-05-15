---
name: log-analysis
description: Capture command output to dated log files and analyze it with standard
  text tools. Use this skill whenever the user wants to run a command and inspect its
  output later, debug something by re-running it, capture logs from a long-running
  process, or analyze any output stream — even if they don't explicitly say "log".
  Triggers on phrases like "run this and save the output", "capture the logs",
  "investigate what happened when X ran", "look at the output of Y", "analyze these
  logs", "what errors did we get", or any debugging task where command output is
  the evidence. Enforces a consistent location and naming convention so logs from
  different runs and sessions stay organized and queryable.
---

# log-analysis

A lightweight convention for capturing command output and querying it later with
standard text tools (`rg`, `tail`, `awk`, `wc`). No database, no daemon — just
disciplined file naming and the right shell idioms.

## The convention

Every captured stream goes here:

```
~/logs/<source>-YYYY-MM-DD-HHMMSS.log
```

- `~/logs/` is the only location. Never put logs anywhere else.
- `<source>` is a short tag identifying what was captured (e.g. `docker-api`,
  `bun-test`, `rsync-backup`, `iaptic-validator`). Kebab-case, no spaces.
- The timestamp is the start time of the capture, local time. One file per run —
  if the same command is run again, it gets a new file.

This convention exists so that:
- Logs are time-ordered by filename, so `ls -t ~/logs/` shows newest first.
- Multiple runs of the same command don't clobber each other.
- You can grep across runs (`rg pattern ~/logs/docker-api-*.log`).
- The user always knows where to look.

## Capturing output

Use this idiom — it shows output live AND saves it:

```bash
mkdir -p ~/logs
SOURCE=docker-api
LOG="$HOME/logs/${SOURCE}-$(date +%Y-%m-%d-%H%M%S).log"
docker logs -f api 2>&1 | tee "$LOG"
```

Important details:

- **Always `2>&1`** before the pipe, or stderr won't be captured. Most useful
  log content is on stderr.
- **Always `tee`**, never `>` alone. The user wants to see output live; silently
  swallowing it into a file makes the command feel hung.
- **Set `$LOG` first** in a separate line, then echo it before running the
  command so the user knows the file path:
  ```bash
  echo "Logging to: $LOG"
  ```
- For commands that exit non-zero on purpose (tests, linters), use
  `set -o pipefail` or capture the exit code with `${PIPESTATUS[0]}` if it
  matters.

When the user asks to "run X and capture", do the full pattern above. Don't just
run the command and assume stdout-to-terminal is enough — the point of the skill
is that the output is retrievable later.

## Querying logs

Use standard tools. `rg` (ripgrep) is preferred over `grep` — it's faster, has
better defaults, and is already on most systems.

### Find the most recent log for a source

```bash
ls -t ~/logs/<source>-*.log | head -1
```

### Search across all runs of a source

```bash
rg -i 'error|exception' ~/logs/docker-api-*.log
```

### Search just the latest run

```bash
rg -i error "$(ls -t ~/logs/docker-api-*.log | head -1)"
```

### Context around a match

```bash
rg -B3 -A10 'request_id=abc123' ~/logs/*.log
```

`-B3 -A10` shows 3 lines before and 10 after — useful for seeing what led up to
an error and what happened after.

### Counts and frequencies

```bash
# How many errors per log file?
for f in ~/logs/*.log; do
  printf "%6d  %s\n" "$(rg -c -i error "$f" 2>/dev/null || echo 0)" "$f"
done | sort -rn | head

# Top error messages
rg -i error ~/logs/*.log | sort | uniq -c | sort -rn | head -20
```

### Time-bounded search

The filename already encodes the start time, so often the right approach is
"pick the right file" rather than filtering by timestamp inside the file. But
if log lines have their own timestamps, `awk` works:

```bash
awk '$1 >= "14:23:00" && $1 <= "14:27:00"' ~/logs/some-source-2026-05-15-142000.log
```

### Live tail with filter

```bash
tail -f ~/logs/docker-api-*.log | rg -i --line-buffered error
```

`--line-buffered` is needed so `rg` flushes per line instead of buffering.

## Listing what's available

When the user asks "what logs do I have" or you need to figure out what to
look at:

```bash
ls -lht ~/logs/ | head -30
```

To group by source:

```bash
ls ~/logs/ | sed 's/-[0-9].*//' | sort | uniq -c | sort -rn
```

## Cleanup

Logs accumulate. When the user wants to clean up:

```bash
# Show what would be deleted (older than 30 days)
find ~/logs/ -name '*.log' -mtime +30

# Actually delete (only after user confirms)
find ~/logs/ -name '*.log' -mtime +30 -delete
```

Don't delete logs unprompted, even old ones — they're cheap and the user may
need them.

## When NOT to capture

This skill is for command output. Don't use it for:

- Application logs that already write to their own files (just `rg` those
  directly).
- Output the user explicitly wants discarded (`> /dev/null`).
- Interactive sessions where `tee` would break TTY behavior (most REPLs,
  `vim`, etc.).

## Picking a good source tag

The tag is what the user (and you) will search by later. Make it specific
enough to be useful:

- ✅ `docker-api`, `rsync-devbox3`, `bun-test-parcolab`, `iaptic-validator-restart`
- ❌ `output`, `log`, `run`, `test` (too generic — will collide)

If the user doesn't suggest one, derive it from the command: `docker logs api`
→ `docker-api`, `bun test` in the parcolab repo → `bun-test-parcolab`. Confirm
the tag with the user on the first capture in a session; reuse it without
asking for subsequent captures of the same thing.

## Summary checklist

When asked to capture: `mkdir -p ~/logs`, derive `$SOURCE`, build
`$LOG=~/logs/$SOURCE-$(date +%Y-%m-%d-%H%M%S).log`, echo the path, run with
`2>&1 | tee "$LOG"`.

When asked to analyze: `ls -t ~/logs/` to orient, then `rg` with context flags.
Prefer the most recent matching file unless the user wants cross-run analysis.
