# Agent Operating Manual — path-b-test

**Purpose:** Test repo for validating Path B workflow (GitButler + Entire + Orca + multi-LLM review).

---

## 1. Git and PRs

Use GitButler (`but`) for all writes. Never `git add`, `git commit`, `git push`, or `gh pr create` directly.

- One logical change per branch/PR.
- Branch names: `feat/<name>` for features, `fix/<name>` for fixes, `chore/<name>` for maintenance.
- PR body: what changed, and anything the user must confirm.
- Never merge a PR yourself. The user reviews and merges.
- Rebase with `but pull` when needed.

---

## 2. Repo Workflow (Path B: GitButler + Entire + Orca)

Load and follow the **`entire-orca-workflow`** skill for the 5-phase execution checklist, troubleshooting table, and `--gate in-review` completion check. Key rules:

1. **GitButler (`but`) for all VCS writes** on unstacked branches (`but commit -b <branch> -m "..." <paths>` → `but push <branch>`). Never stop at `but push`.
2. **Entire Checkpoints on commits:** Ensure `.opencode/plugin/entire.ts` + `~/.config/opencode/plugins/entire-but-bridge.js` (OpenCode/OpenChamber) and `~/.pi/agent/extensions/entire-but-bridge.ts` (Pi) are installed so commits carry `Entire-Checkpoint:` trailers and checkpoints are pushed to `origin`.
3. **Entire Trail + Shadow PR sync:** Create a Trail (`entire trail create --branch <branch> --base main ...`) and update the shadow PR via `gh pr edit` to add `Fixes #<N>` while **preserving `<!-- entire-shadow-pr -->`**.
4. **Trail-posted `entire review`:** Always run `entire review` with `output: "trail"` (never `local`). On zero findings (`approve`), record explicit approval via `entire trail approve <trail> --branch <branch> -m "..."`.
5. **Completion gate:** Before ending a task, verify:
   ```bash
   node ~/.agents/skills/entire-orca-workflow/scripts/preflight.mjs --gate in-review --branch <branch> --issue <N>
   ```
