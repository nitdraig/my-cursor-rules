# Closing the Loop — execute, reconcile, issues

Advisor follow-through after plans exist. **Advisor never edits source code.**

Host-specific dispatch: [host-adapters.md](./host-adapters.md) (Cursor `Task` / `best-of-n-runner`; OpenCode `@general` + `git worktree`).

---

## `execute <plan>` — dispatch and review

### Preconditions

- Git repository (for isolation).
- Plan file exists; dependencies **DONE** in `plans/README.md`.
- Advisor runs drift check from the plan. If in-scope files changed since **Planned at**, refresh plan or `reconcile` first.

### Dispatch

Spawn **one executor** with the **full plan inlined** (uncommitted `plans/` may not exist in executor context).

**Executor preamble:**

> You are the executor for the implementation plan below. Follow it step by step. Run every verification command and confirm the expected result before moving on. Touch only in-scope files. If any STOP condition occurs, stop and report — do not improvise. Read `.cursorrules` and `AGENTS.md` if present. Load specialist skills listed in "Suggested executor toolkit" (OpenCode: `skill` tool; Cursor: invoke by skill name). Commit in your workspace per the plan's git workflow. **Do not** update `plans/README.md` — the reviewer maintains the index. Before reporting, every claim must match a tool result from this session.

**Report format:**

```
STATUS: COMPLETE | STOPPED
STEPS: per step — done/skipped + verification result
STOPPED BECAUSE: (if STOPPED) which condition, what was observed
FILES CHANGED: list
WORKSPACE: absolute path (worktree or cwd)
NOTES: deviations, surprises, judgment calls
SKILLS USED: skill names loaded, if any
```

### Cursor

- Isolated: `Task(subagent_type: "best-of-n-runner", ...)` when available.
- Fallback: `Task(subagent_type: "generalPurpose", ...)` with strict scope — warn user isolation is weaker.

### OpenCode

- Isolated: advisor creates worktree (`git worktree add <path> -b advisor/NNN-slug`), user or `@general` / **build** agent works there.
- Fallback: `@general` in main tree with strict in-scope list — warn user.

Fresh worktrees lack `node_modules` — executor installs deps before verification; not a deviation.

### Review (advisor)

1. Re-run **every done criterion** in the executor workspace — do not trust the report alone.
2. **Scope:** `git diff --stat` vs in-scope list; out-of-scope file = fail.
3. **Read full diff** — solves "Why this matters"? Matches repo conventions?
4. **Read new tests** — meaningful assertions, not placeholders.

**Documented deviations** in NOTES are judged on merit. Undocumented scope creep = fail.

| Verdict | When | Action |
|---------|------|--------|
| **APPROVE** | Criteria pass, scope clean | Index → DONE. Summarize diff, workspace path, NOTES. **User merges** — never merge/push for them. |
| **REVISE** | Fixable gaps | Same executor, specific feedback. **Max 2 rounds**, then BLOCK. |
| **BLOCK** | STOP, scope break, revisions exhausted | Index → BLOCKED + reason. Refine plan; tell user. |

Verification in the executor workspace is allowed — it is isolated from the user's tree.

---

## `reconcile` — keep `plans/` alive

Read `plans/README.md` and each plan:

| Status | Action |
|--------|--------|
| **DONE** | Spot-check cheap done criteria on current HEAD; mark verified |
| **BLOCKED** | Investigate; rewrite plan or REJECTED with rationale |
| **IN PROGRESS** (stale) | Flag user; check worktree if any |
| **TODO** | Drift check; refresh excerpts + Planned at SHA, or REJECTED if finding fixed |

Report: verified done, refreshed, rejected, ready to execute.

---

## `--issues` — GitHub issues

Only with explicit `--issues` flag.

1. `gh auth status` + GitHub remote — else skip with reason.
2. Confirm titles if interactive.
3. `gh issue create --title "..." --body-file <plan>`; labels `improve` + category if they exist.
4. Record URL in plan Status and index.

Plan file remains source of truth; issue is distribution.
