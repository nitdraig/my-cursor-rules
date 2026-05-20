# Cursor Agent Skills

Personal [Cursor Agent Skills](https://cursor.com/docs) that extend the AI agent with specialized workflows, conventions, and domain knowledge. Each skill is a folder with a `SKILL.md` file; Cursor loads them from this directory and applies them when the task matches the skill’s description.

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

Skills marked `user-invocable: true` in their `SKILL.md` frontmatter can be triggered directly from the agent UI when supported:

| Category | Skills |
|----------|--------|
| Frontend & UI | **react-native-patterns**, **responsive-testing**, **web-design-guidelines** |
| SEO | **seo-auditing** |
| Data | **database-design** |
| Content & AI | **writing-copy**, **prompt-engineering** |
| Code quality & testing | **cleaning-code** |

## Installation

Clone this repository into your personal skills directory:

```bash
git clone https://github.com/nitdraig/my-cursor-rules.git ~/.cursor/skills
```

On Windows (PowerShell):

```powershell
git clone https://github.com/nitdraig/my-cursor-rules.git $env:USERPROFILE\.cursor\skills
```

If you already use this folder for other skills, clone elsewhere and copy only the skill subfolders you need, or add this repo as a remote and pull updates.

**Project-scoped skills:** To share skills with a team inside a repo, place them under `.cursor/skills/` in that project instead of `~/.cursor/skills/`.

> **Note:** Do not put custom skills in `~/.cursor/skills-cursor/` — that path is reserved for Cursor’s built-in skills.

## Usage

Skills are picked up automatically when their `description` in the YAML frontmatter matches what you are doing. You can also invoke them explicitly, for example:

- “Run responsive testing on the dashboard at `/settings`.” (Frontend & UI)
- “Review the changes in this PR using our code review skill.” (Code quality & testing)
- “Clean debug logs and translate comments to English in `server/src`.” (Code quality & testing)
- “Review this Express API for security and performance.” (Backend — Express)
- “Add supertest coverage for the new `/api/users` routes.” (Backend — Express)
- “Audit technical SEO for this Next.js site.” (SEO)

## Repository layout

```
.
├── LICENSE
├── README.md
├── .cursorrules          # Shared project conventions for the agent
└── <skill-name>/
    └── SKILL.md          # Required; optional reference.md, examples.md, scripts/
```

Some skills include extra assets (for example `react-best-practices/AGENTS.md`, `express-api-review/references/patterns.md`, or `anthropic-frontend-design/LICENSE.txt`).

## Adding a skill

1. Create a directory named after the skill (lowercase, hyphens).
2. Add `SKILL.md` with YAML frontmatter (`name`, `description`) and markdown instructions.
3. Keep the description specific so the agent knows **when** to apply the skill.
4. Commit and push; pull on other machines to sync.

For authoring guidance, use Cursor’s built-in **create-skill** skill or see the [Cursor docs on Agent Skills](https://cursor.com/docs).

## License

This project is licensed under the [MIT License](./LICENSE). You may use, copy, modify, and distribute these skills freely, provided the copyright notice and license text are included in any substantial copy.
