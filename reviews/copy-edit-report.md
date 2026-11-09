# Copy Edit Report: Google Antigravity

## Pass: 2026-11-09

| Field | Value |
| :--- | :--- |
| Editor model | Claude 3.7 Sonnet / Copy Editor |
| Session | Fresh (yes) |
| Scope | Slides (`course/00-introduction.md`, `course/01-getting-started.md`, `course/02-antigravity-cli.md`, `course/03-antigravity-app.md`); no external lab manuals (demonstrations embedded in slides) |

### Glossary

| Term | Form used | Notes |
| :--- | :--- | :--- |
| Google Antigravity | `Google Antigravity` | Official framework suite name |
| Antigravity CLI | `Antigravity CLI` | Official CLI tool name |
| Antigravity App | `Antigravity App` | Official desktop companion application name |
| `agy` | `agy` | Official CLI binary command name |
| `antigravity .` | `antigravity .` | Official desktop app launch command |
| Gemini | `Gemini` | Google model family |
| `gemini-2.5-flash` | `gemini-2.5-flash` | Fast default model identifier |
| `gemini-2.5-pro` | `gemini-2.5-pro` | Reasoning model identifier |
| `antigravity.yaml` | `antigravity.yaml` | Workspace configuration manifest |
| `AGENTS.md` | `AGENTS.md` | Repository guidelines file |
| `stdin` / `stdout` | `stdin` / `stdout` | Standard Unix streams |
| Questions and Answers | `Questions and Answers` | Standardized title (no `Q&A` or ampersand) |

### Fix counts

| Type | Count |
| :--- | :--- |
| Spelling | 0 |
| Grammar | 3 |
| Smart quotes / code formatting | 1 |
| Em-dashes / ampersands | 0 |
| Consistency | 5 |

### Representative fixes

- `course/01-getting-started.md` / Supported Authentication Methods: enclosed `"modelProvider": "gemini"` in inline code backticks to protect literal JSON syntax.
- `course/01-getting-started.md` / Demo: corrected title number agreement from `Demo Step 1 and 2` to `Demo Steps 1 and 2`.
- `course/02-antigravity-cli.md` / Ad-Hoc Prompts vs. Project Templates: corrected verb agreement from “Encapsulates” to “Encapsulate” under plural heading “Reusable Templates and Rules”.
- `course/02-antigravity-cli.md` / Guardrails for Safe Code Generation: aligned bullet parallelism from “Keeps token consumption low...” to imperative “Keep token consumption low...”.
- `course/02-antigravity-cli.md` / High-Leverage Tasks to Automate with the CLI: aligned bullet parallelism from “Automatically aligns...” to imperative “Automatically align...”.
- `course/02-antigravity-cli.md` / Demo: corrected title number agreement from `Demo Step 2 and 3` to `Demo Steps 2 and 3`.
- `course/03-antigravity-app.md` / Granular Multi-File Diff and Hunk Review: aligned bullet parallelism from “Prevents broken...” to imperative “Prevent broken...”.
- `course/03-antigravity-app.md` / Managing Workspaces and Session History: aligned bullet parallelism from “Saves a parameterized...” to imperative “Save a parameterized...”.
- `course/03-antigravity-app.md` / Demo: corrected title number agreement from `Demo Step 2 and 3` to `Demo Steps 2 and 3`.

### Left unchanged

- `agy` and `antigravity .` kept as CLI and desktop binary commands
- `gemini-2.5-flash` and `gemini-2.5-pro` kept as official model identifiers
- Inline code and fenced code blocks untouched
- Stock intro images reused as specified (`roi-logo-with-name.png`, `welcome.png`, `agenda.png`, `who-should-attend.png`, `prerequisites.png`)
- Hyphenated contrast breaks (` - `) kept in place per typography rules
- All `---` slide separators, metadata directives, and layout comments intact
