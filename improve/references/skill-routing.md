# Skill Routing — Audit & Plan Handoffs

When the advisor audits or writes plans, **point executors at specialist skills** already installed in the user's skill library. Do not paste entire skill files into plans — cite the skill **`name`** from frontmatter and one line on when to load it.

Discover installed skills from recon:

- **Cursor:** `~/.cursor/skills/`, `.cursor/skills/`
- **OpenCode:** `~/.config/opencode/skills/`, `~/.agents/skills/`, `.opencode/skills/`

If a skill is missing, omit it; the plan must still be self-contained.

## Audit phase — deepen a category

When running a category yourself (no subagent) or briefing an explore agent, optionally read the matching skill for checklist depth:

| Audit category | Skill `name` | Use when |
|----------------|--------------|----------|
| Security | `auditing-security` | OWASP-style repo scan, secrets, injection |
| Security (Express API) | `express-api-review` | Routes, middleware, Mongoose, API auth |
| Performance (app) | `auditing-performance` | Bundles, rendering, DB queries, CWV |
| Performance (React/Next) | `vercel-react-best-practices` | Components, data fetching, bundle rules |
| Test coverage | `writing-tests` | Missing tests, coverage strategy |
| Test coverage (Express) | `express-testing` | Supertest, middleware, API integration tests |
| Tech debt / hygiene | `cleaning-code` | Debug logs, obsolete comments, non-English comments |
| UI / accessibility | `web-design-guidelines` | UI review against interface guidelines |
| Responsive layout | `responsive-testing` | Breakpoint verification after UI plans |
| Code review (full PR) | `reviewing-code` | Cross-stack review conventions |
| Dependencies | `updating-npm-package` | Major bumps, migration reports |
| Database schema | `database-design` | New collections, indexes, relationships |
| SEO | `seo-auditing` or `seo-analysis` | Technical or full SEO audits |

## Plan phase — executor toolkit

In each plan's **Suggested executor toolkit** section, list only skills that apply to that plan's steps:

| Plan type | Recommend executors load |
|-----------|--------------------------|
| Fix Express N+1 / auth / validation | `express-api-review` (review after), `express-testing` (tests) |
| Add API integration tests | `express-testing` |
| React perf refactor | `vercel-react-best-practices` |
| Remove console.log / translate comments | `cleaning-code` |
| Security hardening PR | `auditing-security` |
| Dependency major upgrade | `updating-npm-package` |
| UI component work | `frontend-design`, `web-design-guidelines`; `responsive-testing` before merge |
| Post-execute review | `reviewing-code` on the executor's diff |

## Advisor skill

| Role | Skill `name` | Note |
|------|--------------|------|
| This workflow | `improve` | Advisor only — never implement |

## Project conventions

If `.cursorrules` or `AGENTS.md` exists, every plan must tell the executor:

> Read `.cursorrules` and `AGENTS.md` before editing. Match stack conventions (package manager, folder layout, English-only comments).

Stack hints from this collection's default `.cursorrules`: Express + TypeScript + MongoDB/Mongoose + pnpm on backend; Next.js + React Query hooks in `/hooks` on frontend.
