---
description: Extract non-obvious learnings from session to AGENTS.md files and self-improve project overlay
---

Analyze this session and extract non-obvious learnings. You will work in two phases: Learn, then Meta-Learn.

## Instruction File Detection

First, check which instruction files exist in the project tree. Check in this order:

1. AGENTS.md (root and nested)
2. CLAUDE.md
3. GEMINI.md
4. .cursorrules
5. .github/copilot-instructions.md

If multiple exist, use the first found as the primary file. If a project uses a non-AGENTS.md file (e.g., CLAUDE.md only), note this for the overlay.

## Phase 1: Learn

1. **Read overlay** — if `.opencode/learn.md` exists, read it for project-specific scoping hints, directory routing rules, and known conventions.

2. **Review session** — scan this conversation for non-obvious discoveries:
   - Hidden relationships between files or modules
   - Execution paths that differ from how code appears
   - Non-obvious configuration, env vars, or flags
   - Debugging breakthroughs when error messages were misleading
   - API/tool quirks and workarounds
   - Build/test commands not in README
   - Architectural decisions and constraints
   - Files that must change together

   Do NOT include: obvious facts from docs, standard framework behavior, things already in an AGENTS.md, verbose explanations, or session-specific details.

3. **For each learning:**
   - Determine scope: which directory level does it apply to?
     - Project-wide → root instruction file
     - Package/module-specific → nearest package AGENTS.md (e.g., `packages/foo/AGENTS.md`)
     - Feature-specific → nearest feature AGENTS.md (e.g., `src/auth/AGENTS.md`)
   - Use overlay's `directory-scoping` hints if available
   - Read the existing instruction file at that level
   - Skip if already documented (deduplicate — check for same concept, not exact wording)
   - Write 1-3 line entry at the end of the appropriate section, or create a new section if needed

4. **Add overlay reference** — if not already present in the root instruction file, append:
   ```
   When using /learn, read `.opencode/learn.md` first
   ```

5. **Summarize** — present the changes to the user. List each file modified, how many entries added, and brief descriptions. Ask for approval before proceeding to Phase 2.

## Phase 2: Meta-Learn

After the user approves the Phase 1 changes:

1. **Evaluate learn performance** — ask yourself:
   - Were any learnings misplaced at the wrong directory level?
   - Did the overlay lack a useful scoping rule that would have helped?
   - Were new conventions or directory patterns discovered?
   - Did the instruction file detection need correcting?

2. **Update `.opencode/learn.md`** — if improvements are needed, update or create the overlay. Format:

   ```markdown
   # learn overlay

   ## instruction-files
   - primary: AGENTS.md
   - notes: (any relevant notes about the project's instruction file setup)

   ## directory-scoping
   - root: (what belongs at root level)
   - packages/foo/: (what belongs in this package)
   - src/feature/: (what belongs in this feature)

   ## known-conventions
   - (project-specific conventions that affect how learnings are placed)
   ```

   If the overlay already exists and is accurate, leave it unchanged.

3. **Report** — tell the user what was updated in the overlay, or that it was already accurate.

$ARGUMENTS
