# Invocation Variants

Modifiers compose unless noted. Host-specific dispatch details: [host-adapters.md](./host-adapters.md).

| Invocation | Behavior |
|------------|----------|
| *(bare)* | Full workflow: Recon → Audit → Vet → Confirm → Plans |
| `quick` | Lighter audit — see effort table in SKILL.md Phase 2 |
| `deep` | Full-repo audit — more subagents, include LOW-confidence investigate items |
| `security`, `perf`, `tests`, … | Recon + single category from audit-playbook + plan |
| `branch` | Audit diff vs default branch only; tag `introduced` / `pre-existing` |
| `next`, `features`, `roadmap` | Recon + direction category; 4–6 suggestions; spike/design plans |
| `plan <description>` | Skip audit; recon + one self-contained plan |
| `review-plan <file>` | Tighten existing plan against plan-template; cold subagent review if possible |
| `execute <plan>` | Dispatch executor + advisor review — [closing-the-loop.md](./closing-the-loop.md) |
| `reconcile` | Refresh `plans/` status, drift, BLOCKED — closing-the-loop |
| `--issues` | Also `gh issue create` per plan (explicit flag only) |

## Examples

- `improve quick security` — fast security pass + plans for top items
- `improve branch` — PR-scoped audit
- `improve plan add rate limiting to auth routes` — single plan, no audit
- `improve execute 002` — run plan 002 in isolated worktree
- `improve reconcile` — sync plan index with repo reality
- `improve deep --issues` — full audit, top plans, GitHub issues

## `branch` scope

Files changed since merge-base with default branch, plus direct importers/callers. Default branch resolution: host-adapters.md.

If on default branch or zero commits ahead: say so; offer full audit.

## Non-interactive

If the user cannot select findings: write plans for top 3–5 by leverage; note default in `plans/README.md`.
