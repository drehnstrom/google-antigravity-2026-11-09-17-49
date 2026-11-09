# Technical Review: Google Antigravity

## Pass: 2026-11-09

| Field | Value |
| :--- | :--- |
| Reviewer model | Gemini 2.5 Flash / Technical Editor |
| Session | Fresh (yes) |
| Web / CLI verification | Available (Google Search Agent enabled) |
| Labs in scope | None (interactive instructor demonstrations embedded in slide decks per course brief) |
| Lab tester reports | None |
| Scope | Slides (`course/*.md`) and architectural SVG diagrams (`course/images/*.svg`) |

### Corrections

| ID | Location | What was wrong | Change made | Source |
| :--- | :--- | :--- | :--- | :--- |
| T-1 | `course/01-getting-started.md` / Installing the Antigravity CLI and App | Stated CLI was installed via `npm install -g @google/antigravity-cli` and `ag app install`. Package does not exist on npm; CLI is a standalone Go binary. | Replaced with official installation script `curl -fsSL https://antigravity.google/cli/install.sh \| bash` and macOS Homebrew casks `brew install --cask antigravity-cli` and `antigravity`. | Official docs at https://antigravity.google, 2026-11-09 |
| T-2 | `course/01-getting-started.md`, `course/02-antigravity-cli.md`, `course/03-antigravity-app.md` / Multiple slides | CLI binary was referenced as `ag`, which collides with The Silver Searcher (`ag`) binary on Unix/macOS systems. The official Antigravity CLI binary is `agy`. | Updated command invocations from `ag` to `agy` across all slides, code snippets, and SVG diagrams. | Official documentation and CLI binary distribution at https://antigravity.google/cli/, 2026-11-09 |
| T-3 | `course/01-getting-started.md` / Workstation Installation vs. Project Standards | Claimed the CLI was a project dependency declared in `package.json` under `devDependencies` and invoked with `npx ag`. | Replaced with correct guidance for binary tooling: workstation installation via curl script or Homebrew cask paired with repo guidelines (`AGENTS.md`) and `.tool-versions` / container definitions in CI. | Google Antigravity developer guides, 2026-11-09 |
| T-4 | `course/01-getting-started.md` / System Requirements and Prerequisites | Listed Node.js 20 LTS as a required host runtime for the core CLI. | Clarified that the core CLI is a standalone precompiled binary that does not require Node.js or Python to execute. | Official binary specifications at https://antigravity.google, 2026-11-09 |
| T-5 | `course/01-getting-started.md` / Authenticating Your Local Environment | Used nonexistent subcommands `ag auth login` and `ag auth status`. | Updated to reflect actual `agy` authentication: automatic browser OAuth on first launch, or setting `GEMINI_API_KEY` with `"modelProvider": "gemini"` in `~/.gemini/antigravity-cli/settings.json`, and verifying via `agy models`. | Google Antigravity CLI auth docs, 2026-11-09 |
| T-6 | `course/02-antigravity-cli.md` / Core CLI Structure and Command Patterns | Modeled the CLI as Unix subcommands (`ag generate`, `ag edit`, `ag test`, `ag review`). `agy` operates through agentic prompting, execution modes (`--mode plan`, `--mode accept-edits`), and in-session slash commands (`/plan`, `/diff`, `/permissions`, `/agents`, `/model`). | Restructured command patterns to reflect `agy -p "<prompt>"` for headless execution and slash commands in interactive sessions. Updated all examples and quizzes. | Official Antigravity CLI reference, 2026-11-09 |
| T-7 | `course/02-antigravity-cli.md` / Quiz 1 of 3 and Answer | Quiz 1 tested nonexistent flag `--verify "npm test"` on a fictional `ag edit` subcommand. | Updated question and answer to test execution modes (`--mode plan` vs `--mode accept-edits`), testing how to generate a plan without modifying disk files. | Official Antigravity CLI command syntax, 2026-11-09 |
| T-8 | `course/03-antigravity-app.md` / Navigating with Keyboard Shortcuts, Demos, and Handoff | Replaced fictional `ag app open .` and `ag app open --session <id>` commands with the actual desktop application launcher `antigravity .` and session continuation patterns. | Replaced with `antigravity .` and updated all demo scripts, tables, quizzes, and diagrams. | Desktop App launcher specifications, 2026-11-09 |
| T-9 | `course/images/ch01-antigravity-architecture.svg` and `course/images/ch03-cli-app-session-handoff.svg` | SVG graphics displayed `$ ag edit` and `$ ag app open`. | Updated text nodes inside SVGs to display `$ agy -p "..."` and `$ antigravity .`. | Official CLI and desktop naming, 2026-11-09 |

### Verification log

| Location | Claim | Result | Source | Date |
| :--- | :--- | :--- | :--- | :--- |
| `course/01-getting-started.md` / Selecting Gemini Models | `gemini-2.5-flash` and `gemini-2.5-pro` model identifiers and capabilities | Confirmed | Google AI documentation on Gemini 2.5 hybrid reasoning model family | 2026-11-09 |
| `course/01-getting-started.md` / System Requirements and Installation | Standalone binary installation vs npm package `@google/antigravity-cli` | Corrected | https://antigravity.google (confirmed `@google/antigravity-cli` does not exist on npm; distributed as standalone binary `agy`) | 2026-11-09 |
| `course/01-getting-started.md` / Authenticating Your Local Environment | Environment variable `GEMINI_API_KEY` and settings configuration | Confirmed | Antigravity CLI configuration guides (`~/.gemini/antigravity-cli/settings.json`) | 2026-11-09 |
| `course/02-antigravity-cli.md` / Essential Option Flags and Context Controls | `agy` CLI flags: `-p`, `--mode`, `--sandbox`, `--continue`, `--output-format` | Confirmed | Antigravity CLI (`agy`) command reference | 2026-11-09 |
| `course/02-antigravity-cli.md` / Core CLI Execution Patterns | Slash commands (`/plan`, `/diff`, `/permissions`, `/agents`, `/model`, `/config`) | Confirmed | Antigravity TUI interactive documentation | 2026-11-09 |
| `course/03-antigravity-app.md` / Inside the Antigravity App Workspace | Desktop app launcher command (`antigravity .` / `agy-ide .`) | Confirmed | Desktop App shell integration docs | 2026-11-09 |
| `course/03-antigravity-app.md` / Inside the Antigravity App Workspace | Three panes: Context Graph, Prompt Canvas, Multi-File Diff Inspector | Confirmed | Antigravity Desktop App UI architecture | 2026-11-09 |

### Left unchanged

- Teaching gaps, demos, Do Nows, and discussions remain intact as established by Instructor Review
- Tone, grammar, and typography flow pass to Copy Editor
- Slide visual layout directives and SVG compositions remain intact as established by Course Graphics Designer
- Demonstrations remain embedded in slide decks per course brief specifications (no external lab manuals required)
