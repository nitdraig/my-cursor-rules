# Agent Skills (Cursor & OpenCode)

Personal agent skills that extend AI coding assistants with specialized workflows, conventions, and domain knowledge. Each skill is a folder with a `SKILL.md` file.

Compatible with:

- **[Cursor](https://cursor.com/docs)** — `~/.cursor/skills/` or `.cursor/skills/`
- **[OpenCode](https://opencode.ai/docs/skills)** — `~/.config/opencode/skills/`, `~/.agents/skills/`, or `.opencode/skills/` (also `.claude/skills/`)

## Skills

### Frontend & UI

| Skill | Description |
|-------|-------------|
| [anthropic-frontend-design](./anthropic-frontend-design/) | **frontend-design** — Build distinctive, production-grade web UIs (landing pages, dashboards, components) with strong aesthetics and without generic “AI slop” styling. |
| [react-best-practices](./react-best-practices/) | **vercel-react-best-practices** — Vercel’s React and Next.js performance guide for components, data fetching, bundles, and refactors (70 rules). |
| [react-native-patterns](./react-native-patterns/) | **react-native-patterns** — React Native and Expo patterns: navigation, platform-specific code, performance, and native modules. |
| [responsive-testing](./responsive-testing/) | **responsive-testing** — Test the app in Cursor’s browser at multiple viewport sizes, capture screenshots, and report layout breakage. |
| [web-design-guidelines](./web-design-guidelines/) | **web-design-guidelines** — Review UI against Web Interface Guidelines for accessibility, UX, and design best practices. |

### SEO

| Skill | Description |
|-------|-------------|
| [seo-analysis](./seo-analysis/) | **seo-analysis** — Full SEO audit with Search Console, PageSpeed, technical crawl, keywords, schema, Core Web Vitals, and a 30-day action plan. |
| [seo-auditing](./seo-auditing/) | **seo-auditing** — Technical SEO audit: meta tags, structured data, Open Graph, sitemaps, `robots.txt`, and accessibility signals. |

### Code quality & testing

| Skill | Description |
|-------|-------------|
| [reviewing-code](./reviewing-code/) | **reviewing-code** — Structured code review for correctness, architecture, TypeScript, React Query hooks, tests, and team conventions. |
| [writing-tests](./writing-tests/) | **writing-tests** — Analyze code and generate unit and integration tests with proper mocking, edge cases, and assertions. |
| [cleaning-code](./cleaning-code/) | **cleaning-code** — Remove debug logs, redundant and obsolete comments, commented-out code; translate remaining comments to English. |

### Backend (Express)

Aligned with `.cursorrules` (Express, TypeScript, MongoDB/Mongoose, pnpm).

| Skill | Description |
|-------|-------------|
| [express-api-review](./express-api-review/) | **express-api-review** — Structured review of Express APIs: REST layout, thin controllers, Mongoose performance, OWASP-style security, and report tables. Includes [reference patterns](./express-api-review/references/patterns.md). |
| [express-testing](./express-testing/) | **express-testing** — Supertest integration tests for routes, Zod validation, JWT auth, error mapping, and mocked services; Jest or Vitest. |

### Audits (performance & security)

| Skill | Description |
|-------|-------------|
| [auditing-performance](./auditing-performance/) | **auditing-performance** — Audit and optimize application performance: bundle size, rendering, database queries, and Core Web Vitals. |
| [auditing-security](./auditing-security/) | **auditing-security** — Systematic security review for OWASP Top 10 risks, exposed secrets, and insecure coding patterns. |

### Data

| Skill | Description |
|-------|-------------|
| [database-design](./database-design/) | **database-design** — Design schemas with tables, relationships, indexes, constraints, normalization, and ORM setup. |

### Content & AI

| Skill | Description |
|-------|-------------|
| [writing-copy](./writing-copy/) | **writing-copy** — Marketing and product copy for landing pages, CTAs, emails, and in-app UI text. |
| [prompt-engineering](./prompt-engineering/) | **prompt-engineering** — Write effective LLM prompts: structure, few-shot examples, chain-of-thought, system prompts, and output parsing. |

### Tooling

| Skill | Description |
|-------|-------------|
| [updating-npm-package](./updating-npm-package/) | **updating-npm-package** — Safely upgrade npm dependencies: check versions, read release notes, and handle minor vs major migrations. |

### Advisory & workflow

| Skill | Description |
|-------|-------------|
| [improve](./improve/) | **improve** — Senior advisor (read-only): audit codebase, prioritize findings, write self-contained `plans/` for other agents to execute. Cursor + OpenCode; includes audit playbook, plan template, and host adapters. |

Skills marked `user-invocable: true` in their `SKILL.md` frontmatter can be triggered directly from the agent UI when supported:

| Category | Skills |
|----------|--------|
| Frontend & UI | **react-native-patterns**, **responsive-testing**, **web-design-guidelines** |
| SEO | **seo-auditing** |
| Data | **database-design** |
| Content & AI | **writing-copy**, **prompt-engineering** |
| Code quality & testing | **cleaning-code** |

## Installation

### Cursor

```bash
git clone https://github.com/nitdraig/my-cursor-skills.git ~/.cursor/skills
```

Windows (PowerShell):

```powershell
git clone https://github.com/nitdraig/my-cursor-skills.git $env:USERPROFILE\.cursor\skills
```

Project-scoped: `.cursor/skills/<skill-name>/`

> Do not put custom skills in `~/.cursor/skills-cursor/` — reserved for Cursor built-ins.

### OpenCode

```bash
git clone https://github.com/nitdraig/my-cursor-skills.git ~/.config/opencode/skills
```

Or copy/symlink skill folders into `~/.agents/skills/` or `.opencode/skills/` per project. Allow skills in `opencode.json` if you use permission patterns.

### Both hosts

Clone once and symlink, or copy only the subfolders you need. Pull to sync updates.

## Usage

Skills are picked up automatically when their `description` in the YAML frontmatter matches what you are doing. You can also invoke them explicitly, for example:

- “Run responsive testing on the dashboard at `/settings`.” (Frontend & UI)
- “Review the changes in this PR using our code review skill.” (Code quality & testing)
- “Clean debug logs and translate comments to English in `server/src`.” (Code quality & testing)
- “Review this Express API for security and performance.” (Backend — Express)
- “Add supertest coverage for the new `/api/users` routes.” (Backend — Express)
- “Audit technical SEO for this Next.js site.” (SEO)
- “Run improve on this repo — audit and write plans for the top findings.” (Advisory)

## Repository layout

```
.
├── LICENSE
├── README.md
├── .cursorrules          # Shared project conventions for the agent
└── <skill-name>/
    └── SKILL.md          # Required; optional reference.md, examples.md, scripts/
```

Some skills include extra assets (for example `improve/references/`, `react-best-practices/AGENTS.md`, `express-api-review/references/patterns.md`).

## Adding a skill

1. Create a directory named after the skill (lowercase, hyphens).
2. Add `SKILL.md` with YAML frontmatter (`name`, `description`) and markdown instructions.
3. Keep the description specific so the agent knows **when** to apply the skill.
4. Commit and push; pull on other machines to sync.

For authoring guidance, use **create-skill** (Cursor) or see [Cursor docs](https://cursor.com/docs) / [OpenCode skills](https://opencode.ai/docs/skills).

## License

This project is licensed under the [MIT License](./LICENSE). You may use, copy, modify, and distribute these skills freely, provided the copyright notice and license text are included in any substantial copy.
