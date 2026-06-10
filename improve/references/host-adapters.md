# Host Adapters — Cursor & OpenCode

The improve workflow is **host-agnostic**. Behavior (advisor read-only, plans in `plans/`, self-contained handoffs) is identical. Only **how you spawn parallel auditors and executors** changes.

Detect the host at session start from available tools and config paths. If uncertain, say which host you assume and proceed with the matching row below.

## Host detection

| Signal | Cursor | OpenCode |
|--------|--------|----------|
| Parallel delegation | `Task` tool with `subagent_type` | `@explore`, `@general`, or agent `task` delegation |
| Skill loading | Skills in agent context / skill list | Native `skill` tool + paths below |
| Project rules | `.cursorrules`, `AGENTS.md` | `AGENTS.md`, `opencode.json` |
| Read-only audit agent | `Task` → `explore` | `@explore` or configured explore subagent |
| Implementer agent | `Task` → `generalPurpose` or `best-of-n-runner` | `@general` / **build** primary agent |
| Isolated execution | `best-of-n-runner` worktree (if available) | Manual `git worktree add` + executor session, or general agent with strict plan preamble |

## Skill install paths

Same skill folders; different discovery roots:

| Scope | Cursor | OpenCode |
|-------|--------|----------|
| Global | `~/.cursor/skills/<name>/SKILL.md` | `~/.config/opencode/skills/<name>/SKILL.md` or `~/.agents/skills/<name>/SKILL.md` |
| Project | `.cursor/skills/<name>/SKILL.md` | `.opencode/skills/<name>/SKILL.md` or `.agents/skills/<name>/SKILL.md` |

Claude-compatible paths (`.claude/skills/`) also work in OpenCode. Point executors at the skill **name** from frontmatter, not the host folder.

## Phase 2 — Parallel audit (read-only)

**Goal:** one pass per audit category (or cluster), findings only, no fixes.

### Subagent prompt (all hosts)

Every delegated auditor gets the same payload:

1. Absolute path to `references/audit-playbook.md` + section headings to read (**include `## Finding format`**).
2. Recon facts: languages, frameworks, key dirs, effort level, what to skip.
3. Domain risk hints from recon.
4. Instruction: **read-only** — no edits, no installs, no commits; return findings in playbook format only.
5. Confirm the playbook file was readable (paste sections only if the path failed).

### Cursor

```
Task(subagent_type: "explore", readonly: true, prompt: <payload above>)
```

- `standard`: up to 4 concurrent Tasks (group related categories).
- `deep`: up to 8, ideally one category each.
- `quick`: 0–1 Task; advisor sweeps hotspots directly.

### OpenCode

- **Preferred:** `@explore` per category (built-in read-only subagent).
- **Alternative:** delegate via primary agent task tool to a configured **explore** / **review** subagent with edit/bash denied in `opencode.json`.
- Same concurrency targets as Cursor; if the host caps parallel tasks, run sequential explore passes.

### No subagent support

Audit in category-priority order yourself: correctness → security → tests → perf → tech-debt → deps → DX → docs → direction.

## Phase 4 / execute — Implementation (mutates code)

**Advisor never edits source.** Only an executor does, in an **isolated** workspace when possible.

### Executor prompt (all hosts)

Always **inline the full plan file** in the dispatch message. Uncommitted `plans/` may not exist in the executor's tree.

Include the preamble from [closing-the-loop.md](./closing-the-loop.md) (executor instructions + report format).

Tell the executor to load specialist skills by name when the plan's "Suggested executor toolkit" section lists them (OpenCode: `skill` tool; Cursor: invoke named skill if available).

### Cursor

| Isolation | How |
|-----------|-----|
| Worktree available | `Task(subagent_type: "best-of-n-runner", prompt: <full plan + preamble>)` |
| No worktree | `Task(subagent_type: "generalPurpose", prompt: <plan + "edit only in-scope files; do not touch user's other branches">)` — warn user isolation is weaker |

Advisor reviews diff in the worktree path returned by the subagent.

### OpenCode

| Isolation | How |
|-----------|-----|
| Worktree (recommended) | Advisor runs read-only: `git worktree add ../<repo>-plan-NNN -b advisor/NNN-slug` then user or `@general` / **build** agent works in that directory |
| No worktree | `@general` with the same strict preamble; scope limited to in-plan paths |

OpenCode has no single built-in "best-of-n-runner" — document the worktree path in the review verdict.

### Executor model tier

- **Advisor:** use the strongest model available (plan quality matters).
- **Executor:** mid-tier / cost-efficient model is fine if the plan is explicit.
- Do not hardcode vendor model names (`sonnet`, `haiku`); use "strong advisor / standard executor" or the model the user named.

## Phase 3 — Report

Use [report-template.md](./report-template.md) for the user-facing audit output (both hosts).

## Commands — cross-platform

During recon, record verification commands that work on the user's OS:

| Intent | Unix / Git Bash | PowerShell fallback |
|--------|-----------------|---------------------|
| Search pattern | `rg -n "pattern" src/` | `rg -n "pattern" src/` (if ripgrep installed) or `git grep -n "pattern" -- src/` |
| Drift stat | `git diff --stat <sha>..HEAD -- <paths>` | same |
| Short SHA | `git rev-parse --short HEAD` | same |

Prefer `rg` and `git` over shell-specific `grep` in plans.

## `branch` variant — default branch resolution

Try in order until one works:

1. `git symbolic-ref refs/remotes/origin/HEAD` → `origin/main` or `origin/master`
2. `git rev-parse --verify origin/main` or `origin/master`
3. Ask the user, or diff against `HEAD~20` with a clear caveat

If no remote: use `git merge-base main HEAD` / `master HEAD` for local branches, or offer a full audit.

## Config files to read in Recon (both hosts)

- `README`, `CONTRIBUTING`, `AGENTS.md`, `CLAUDE.md`
- `.cursorrules` (Cursor-heavy repos)
- `opencode.json` (OpenCode permissions and custom agents)
- Root manifests (`package.json`, `pyproject.toml`, `go.mod`, …)
- CI workflows

## OpenCode permissions note

If `improve` runs as the **plan** primary agent, tool permissions should allow read/bash (read-only) and writing under `plans/` only. Executors need `edit` + `bash` in their isolated worktree. Configure in `opencode.json` — do not bypass user deny rules.
