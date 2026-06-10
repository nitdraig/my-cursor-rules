# Audit Report Template

Use this structure for the **user-facing report** after Phase 3 vetting (before or after plan selection). Works the same in Cursor and OpenCode.

```markdown
# Improve audit: <repo name or path>

**Date:** <YYYY-MM-DD>
**Host:** Cursor | OpenCode | unknown
**Effort:** quick | standard | deep
**Focus:** full | <category> | branch | direction
**Commit:** `<short SHA>`
**Scope note:** <what was audited; what was excluded>

## Executive summary

2–4 sentences: overall health, top risk, recommended next action (e.g. "write plans 001–003" or "execute plan 001").

## Verification baseline

| Check | Command | Result |
|-------|---------|--------|
| Typecheck | `...` | pass / fail / missing |
| Tests | `...` | pass / fail / missing |
| Lint | `...` | pass / fail / missing |

If missing: establishing a baseline is finding #1 (link to plan if exists).

## Findings (problems)

Ordered by leverage (impact ÷ effort × confidence). Vet confirmed — no by-design false positives.

| # | ID | Finding | Category | Impact | Effort | Risk | Conf. | Evidence |
|---|-----|---------|----------|--------|--------|------|-------|----------|
| 1 | SEC-01 | ... | security | ... | M | LOW | HIGH | `path:line` |

### Branch-only table (when `branch` variant)

| # | Finding | Origin | Evidence |
|---|---------|--------|----------|
| 1 | ... | introduced / pre-existing | `path:line` |

## Direction (opportunities)

Not ranked against bugs — options for the maintainer.

1. **<title>** — Evidence: `...` Trade-off: ... Effort: S/M/L (coarse).
2. ...

## Considered and rejected

- **<finding>:** <one line why not reported or downgraded>

## Recommended plans

| Priority | Finding IDs | Suggested slug | Depends on |
|----------|-------------|----------------|------------|
| 1 | SEC-01, COR-02 | `001-fix-...` | verification baseline |

Ask user which to plan (default: top 3–5). Non-interactive: document default in `plans/README.md`.

## Executor routing

Specialist skills to load during execution (see [skill-routing.md](./skill-routing.md)):

- ...

## Out of scope / not audited

- <packages, dirs, categories skipped and why>
```

## Quality bar

- Every row in findings table was **opened and verified** by the advisor.
- No secret values — `file:line` and credential type only.
- Direction items cite repo evidence, not generic product advice.
- Host and effort level stated so a later session can reproduce context.
