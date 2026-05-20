---
name: cleaning-code
description: Removes debug logs, unnecessary and obsolete comments, and translates remaining comments to English. Use when cleaning up code hygiene, removing console.log, deleting stale TODOs, translating Spanish or mixed-language comments, or preparing a PR for English-only documentation per project conventions.
user-invocable: true
---

# Cleaning Code (Logs & Comments)

Clean source files by removing noisy logging, redundant or outdated comments, and translating useful comments to **English**. Align with `.cursorrules` when present: all comments, descriptions, and documentation should be in English.

Use **`deslop`** (Cursor Team Kit) when the goal is removing AI-generated slop from a **branch diff**; use this skill for **logs, comment hygiene, and i18n** across specified files or folders.

## When to use

- “Remove debug logs”, “clean up console.log”, “delete unnecessary comments”
- “Translate comments to English”, “fix Spanish comments in this module”
- “Remove obsolete TODOs”, “delete commented-out code”
- Pre-merge cleanup before review or release
- After a feature is done and temporary debugging remains

## Scope

1. Confirm target paths (files, directories, or “changed files only”).
2. If the user gives no scope, prefer **files they mentioned** or the **current diff** — do not rewrite unrelated modules.
3. **Do not change behavior** unless removing dead code that is provably unused (commented-out blocks only).

## Workflow

### 1. Inventory

Search the scope for:

| Pattern | Examples |
|---------|----------|
| Debug logs | `console.log`, `console.debug`, `console.dir`, `print()`, `dbg!()` |
| Temporary logs | `// TODO remove`, `// debug`, `// temp` |
| Noise comments | Restates the next line (`// increment i`, `// return user`) |
| Obsolete comments | Wrong description, completed TODO, outdated API notes |
| Commented-out code | Old implementations left as comments |
| Non-English comments | Spanish, mixed language, legacy copy-paste |

### 2. Logs — remove vs keep

**Remove**

- `console.log` / `console.debug` used for development tracing
- Logs that dump full objects, tokens, passwords, or PII
- Duplicate logs (same message in try and catch without value)
- Logs left after debugging a specific bug (no longer needed)

**Keep (or replace with proper logging)**

- Structured production logging (`logger.info`, `pino`, `winston`) at appropriate levels
- `console.error` in CLI scripts or intentional stderr paths when that is the project pattern
- Logs behind explicit debug flags or `NODE_ENV === 'development'` if the team uses that pattern
- Test output and test utilities (do not strip assertions or test `console` unless clearly debug noise)

When removing `console.log`, do **not** delete the surrounding error handling or business logic.

### 3. Comments — remove vs keep

**Remove**

- Comments that duplicate obvious code
- Outdated comments describing old behavior
- `// TODO` / `// FIXME` that are already done (verify in git or code)
- Large blocks of commented-out code (delete; history lives in git)
- Section banners with no information (`// ----------`, `// utils`)
- AI-style filler (“Here we handle the request”, “This function does X” when the name already says it)

**Keep (translate to English if needed)**

- Non-obvious **why** (business rules, edge cases, known bugs, workarounds)
- Security or compliance notes
- Performance caveats (“avoid N+1”, “must stay sync”)
- Public API **JSDoc** / TSDoc (translate prose to English; keep tags)
- License headers and third-party attribution
- `eslint-disable` / `biome-ignore` with a **short English reason**

**Translate, do not delete**

- Useful comments in Spanish or other languages → clear, concise English
- Preserve technical accuracy; do not expand into essays

### 4. Apply edits

- One logical concern per file when possible (logs first, then comments)
- Match existing comment style (`//` vs `/* */`, JSDoc on exports)
- Do not reformat unrelated code or rename symbols unless required for the cleanup
- Run `npm run lint` / `pnpm run lint` when the project defines it

### 5. Report

```markdown
# Code cleanup: [scope]

## Summary
[1–2 sentences: files touched, main actions]

## Removed
- [N] debug `console.log` calls in `path/file.ts`
- [N] obsolete / redundant comments
- [N] commented-out code blocks

## Translated to English
- `path/file.ts` — [brief note, e.g. “service layer comments”]

## Kept intentionally
- [e.g. “error logger in middleware”, “eslint-disable for dynamic import”]

## Not changed
- [files skipped and why, if any]
```

## Translation guidelines

- Use **American or neutral English** consistent with the repo
- Prefer short phrases over long sentences
- Keep domain terms the codebase already uses (e.g. existing enum names)
- User-facing strings in UI copy are **out of scope** unless the user asks to translate those too
- Do not translate string literals used as API keys, i18n keys, or database values

## Examples

| Before | After |
|--------|--------|
| `console.log('user', user)` | *(removed)* |
| `// obtiene el usuario por id` | `// Load user by id` |
| `// TODO: add validation (done in PR #42)` | *(removed)* |
| `// const oldHandler = () => { ... }` | *(removed block)* |
| `// Increment retry count` above `retries++` | *(removed)* |

## Guardrails

- **Behavior unchanged** — no logic edits except deleting dead commented-out code
- **Minimal diff** — only lines affected by cleanup
- **No new noise** — do not add “cleaned up” meta-comments
- **Secrets** — if a log exposes a token or key, remove the log and flag it in the report (do not paste secrets in chat)
- Ask before deleting **large** comment blocks that might be legal docs or feature flags

## Related skills

| Skill | Use when |
|-------|----------|
| `deslop` | Branch diff cleanup of AI slop (Team Kit) |
| `reviewing-code` | Review after cleanup |
| `express-api-review` | Backend-specific review (not general comment cleanup) |

## Checklist

```
- [ ] Scope confirmed with user or from context
- [ ] Debug logs removed; production logging preserved
- [ ] Redundant and obsolete comments removed
- [ ] Commented-out code removed
- [ ] Remaining useful comments in English
- [ ] Lint run if available
- [ ] Summary report delivered
```
