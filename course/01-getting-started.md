<!-- course-title: Google Antigravity -->
<!-- course-theme: roi-alt-theme -->

<!-- layout: title -->
![ROI Logo](images/roi-logo-with-name.png)

Google Antigravity

# Chapter 1: Getting Started with Google Antigravity

---

# Chapter 1: Objectives

- Install, authenticate, and configure the Google Antigravity framework in a local development environment
- Understand Google Antigravity architecture and core capabilities for AI development
- Verify prerequisite tooling and install Google Antigravity packages
- Configure authentication and required environment settings for Gemini models
- Walk through live setup and validation of the Antigravity environment

---

<!-- layout: navigation -->
# Chapter 1

- **Google Antigravity Overview and Architecture**
- System Requirements and Installation
- Authentication and Environment Configuration
- Demo: Installing and Configuring Google Antigravity

---

<!-- layout: 2-column -->
# Why AI Coding Workflows Create Friction

### Manual Prompt-and-Paste
- Developers manually copy files and stack traces into browser chat windows
- Models lack awareness of repository structure, dependencies, and types
- Generated snippets require tedious manual merging and formatting
- Team members use inconsistent prompts with no shared configuration

### Google Antigravity Framework
- Connects Gemini models directly to your local repository and toolchain
- Automatically indexes project structure and builds targeted context
- Produces deterministic, reviewable file patches from the CLI or App
- Standardizes model selection and guardrails in versioned configuration

<!-- below-columns -->

> [!NOTE]
> Google Antigravity does not replace your editor or Git workflow - it removes the repetitive wiring between your code and Gemini models.

---

<!-- layout: stacked -->
# Google Antigravity Runtime Architecture

- **Dual entry surfaces**: run fast terminal commands with the Antigravity CLI or inspect workflows visually in the Antigravity App
- **Shared local engine**: a single runtime reads `antigravity.yaml`, indexes the abstract syntax tree (AST), and manages `.antigravity/` session state
- **Bidirectional Gemini loop**: sends pruned project context to Gemini models and applies structured file patches back to your working tree

![CLI commands and App workspace events flow through the shared Antigravity engine and antigravity.yaml into Gemini models and return structured diffs to local files](images/ch01-antigravity-architecture.svg)

---

<!-- layout: card-layout -->
# One Framework, Two Complementary Interfaces

### Antigravity CLI
- Built for terminal-first execution, shell pipelines, and automated scripts.
- Generates boilerplate, scaffolds tests, and applies targeted refactors with minimal keystrokes.

### Antigravity App
- Provides a graphical workspace for interactive prototyping and multi-file diff inspection.
- Visualizes context graphs, token budgets, and prompt execution traces side by side.

### Shared Workspace State
- Both interfaces read the same `antigravity.yaml` manifest and local `.antigravity/` state directory.
- Start a task in the terminal and open the exact same session in the App without losing context.

---

# Selecting Gemini Models for Antigravity Tasks

- Antigravity lets you route tasks to different Gemini models based on latency, cost, and reasoning depth
- Configure a fast default model for routine edits and override with a reasoning model for complex architecture tasks

| Model Identifier | Primary Strength | Ideal Antigravity Use Cases |
| :--- | :--- | :--- |
| `gemini-2.5-flash` | Low latency and high throughput | Boilerplate generation, unit test scaffolding, inline docstrings, CLI one-liners |
| `gemini-2.5-pro` | Deep reasoning and multi-file planning | Cross-module refactoring, architecture prototyping, debugging subtle concurrency bugs |

> [!TIP]
> Set `gemini-2.5-flash` as your default CLI model for sub-second feedback, and pass `--model gemini-2.5-pro` when planning multi-file changes.

---

<!-- layout: navigation -->
# Chapter 1

- Google Antigravity Overview and Architecture
- **System Requirements and Installation**
- Authentication and Environment Configuration
- Demo: Installing and Configuring Google Antigravity

---

<!-- layout: 3-column -->
# System Requirements and Prerequisites

### Host Runtime
- 64-bit macOS, Linux, or Windows (WSL2 recommended on Windows)
- Node.js 20 LTS or newer, or Python 3.11+ for SDK extensions
- Minimum 4 GB available RAM for local AST indexing and App rendering

### Developer Tooling
- Git 2.35+ initialized in the target workspace
- Modern code editor such as Visual Studio Code
- Terminal emulator with UTF-8 output support

### API and Network
- Outbound HTTPS (port 443) to Google Generative Language or Vertex AI endpoints
- Valid Gemini API key or Google Cloud project with Vertex AI enabled

---

# Installing the Antigravity CLI and App

- Install the CLI globally via your package manager and launch or install the companion desktop App
- Run `ag doctor` immediately after installation to verify binary paths, Git integration, and OS keychain access

```bash
# Install the Antigravity CLI globally
npm install -g @google/antigravity-cli

# Verify CLI version and binary path
ag --version

# Install or link the graphical Antigravity App companion
ag app install

# Run preflight diagnostics on local environment
ag doctor
```

---

<!-- layout: 2-column -->
# Global vs. Project-Pinned Installations

### Global Installation
- Installed once per developer workstation (`npm install -g`)
- Immediately available in any directory for quick prototyping
- Risk: team members may run different CLI versions with mismatched template schemas

### Project-Pinned Installation
- Declared in `package.json` (`devDependencies`) or `.tool-versions`
- Guarantees every developer and CI runner uses the exact same Antigravity release
- Recommended whenever `antigravity.yaml` workflows are committed to Git

<!-- below-columns -->

> [!IMPORTANT]
> When running in a team repository, invoke Antigravity via `npx ag` or enable `engineStrict` in `antigravity.yaml` so version drift fails fast.

---

<!-- layout: navigation -->
# Chapter 1

- Google Antigravity Overview and Architecture
- System Requirements and Installation
- **Authentication and Environment Configuration**
- Demo: Installing and Configuring Google Antigravity

---

<!-- layout: card-layout -->
# Supported Authentication Methods

### Gemini API Key
- Fastest setup for individual developers and local prototyping via Google AI Studio.
- Stored in OS credential storage or supplied through the `GEMINI_API_KEY` environment variable.

### Google Cloud ADC
- Uses Application Default Credentials (`gcloud auth application-default login`) with Vertex AI.
- Ideal for enterprise teams requiring IAM role governance, audit logs, and regional data residency.

### Interactive Browser OAuth
- Initiated directly via `ag auth login` or the Antigravity App sign-in screen.
- Automatically refreshes short-lived access tokens and binds them to your local developer profile.

---

# Authenticating Your Local Environment

- Use `ag auth login` to store credentials securely in your operating system keychain, or export `GEMINI_API_KEY` for headless sessions
- Verify active identity, auth provider, and model quota access with `ag auth status`

```bash
# Option 1: Interactive authentication saved to OS keychain
ag auth login

# Option 2: Environment variable for shell or CI sessions
export GEMINI_API_KEY="AIzaSyYourApiKeyHere"

# Verify active credential source and test token validity
ag auth status
```

> [!WARNING]
> Never hardcode `GEMINI_API_KEY` inside `antigravity.yaml` or commit `.env` files to version control. Always add `.env` and `.antigravity/credentials.json` to `.gitignore`.

---

# Do Now: Spot the Context Bloater

Before we look at `antigravity.yaml`, inspect a typical repository directory listing in your head or on your machine:

- `.git/`, `node_modules/`, `dist/`, `.env.local`, `src/`, `tests/`
- Write down which paths **must never** be sent to Gemini for security or token reasons
- Write down which paths Gemini **needs** to generate accurate code and unit tests

---

# Configuring Project Defaults in antigravity.yaml

- Running `ag init` creates an `antigravity.yaml` manifest at the root of your repository
- This file controls model selection, context inclusion rules, and safety boundaries for both the CLI and the App

```yaml
version: 1
project:
  name: order-service
models:
  default: gemini-2.5-flash
  reasoning: gemini-2.5-pro
  temperature: 0.2
context:
  include: ["src/**/*.ts", "tests/**/*.ts"]
  exclude: ["node_modules/**", "dist/**", "**/*.env*"]
  max_tokens: 32000
safety:
  require_diff_approval: true
```

---

<!-- layout: 2-column -->
# Configuration Precedence and Troubleshooting

### Precedence Order (Highest First)
1. **Explicit CLI flags** (`--model`, `--temperature`)
2. **Environment variables** (`ANTIGRAVITY_MODEL`, `GEMINI_API_KEY`)
3. **Workspace manifest** (`./antigravity.yaml`)
4. **User global config** (`~/.config/antigravity/config.yaml`)

### Common Setup Pitfalls
- **403 Permission Denied**: API key lacks Generative Language API access or Vertex AI region is mismatched
- **Context overflow**: Missing `exclude` rules pull `node_modules/` or build artifacts into prompts
- **Stale env var**: A forgotten `export ANTIGRAVITY_MODEL` overrides changes made in `antigravity.yaml`

<!-- below-columns -->

> [!TIP]
> Run `ag config --effective` at any time to print the merged configuration and see which layer (flag, env var, workspace, or global) supplied each active setting.

---

<!-- layout: navigation -->
# Chapter 1

- Google Antigravity Overview and Architecture
- System Requirements and Installation
- Authentication and Environment Configuration
- **Demo: Installing and Configuring Google Antigravity**

---

<!-- layout: card-layout -->
# Demo: Installing and Configuring Antigravity

### Step 1: Verify and Diagnose
- Check the installed CLI version and run `ag doctor` to confirm Node.js, Git, and OS keychain readiness.
- Inspect any warnings reported by the diagnostic check before authenticating.

### Step 2: Authenticate with Gemini
- Configure authentication using `ag auth login` or `GEMINI_API_KEY` and verify identity with `ag auth status`.
- Confirm both `gemini-2.5-flash` and `gemini-2.5-pro` endpoints are reachable.

### Step 3: Initialize and Validate Workspace
- Run `ag init` in a sample repository to generate `antigravity.yaml` and `.gitignore` entries.
- Inspect effective configuration layers, execute a live connectivity check, and launch the Antigravity App.

---

# Demo Step 1 and 2: Diagnostics and Authentication

- First, we verify the installation health and inspect the diagnostic output before authenticating

```bash
# 1. Check installed version and environment health
ag --version
ag doctor

# Expected output from ag doctor:
# [OK] Node.js v20.14.0 (>= 20.0.0)
# [OK] Git v2.43.0 repository detected (.git)
# [OK] OS Keychain unlocked and writable

# 2. Authenticate and verify active credential source
ag auth login --provider ai-studio
ag auth status
```

---

<!-- layout: 2-column -->
# Demo Step 3: Workspace Initialization and Ping

### Terminal Commands
```bash
# 3. Initialize workspace config
ag init --default-model gemini-2.5-flash

# 4. Inspect effective config sources
ag config --effective

# 5. Send a live validation prompt
ag ping --model gemini-2.5-flash

# 6. Open workspace in the App
ag app open .
```

### Expected Validation Results
- `ag init` writes `antigravity.yaml` and appends `.antigravity/cache` and `.antigravity/credentials.json` to `.gitignore`
- `ag config --effective` shows `models.default: gemini-2.5-flash (source: ./antigravity.yaml)`
- `ag ping` returns round-trip latency and token usage from `gemini-2.5-flash`
- `ag app open .` launches the graphical interface preloaded with the same workspace settings

---

# What You Learned

- Installed, authenticated, and configured the Google Antigravity framework in a local development environment
- Understood Google Antigravity architecture and core capabilities for AI development
- Verified prerequisite tooling and installed Google Antigravity packages
- Configured authentication and required environment settings for Gemini models
- Walked through live setup and validation of the Antigravity environment

---

# Quiz 1 of 3

**How do the Antigravity CLI and the graphical Antigravity App stay synchronized when working on the same project?**

- A. They require manual export and import of prompt history files between sessions
- B. They share the local Antigravity Core Engine, reading the same `antigravity.yaml` manifest and `.antigravity/` workspace state
- C. The CLI only runs in the cloud while the App only runs against local files
- D. The App overwrites CLI settings every time it starts up

---

# Quiz 1: Answer

**How do the Antigravity CLI and the graphical Antigravity App stay synchronized when working on the same project?**

**Correct: B.** They share the local Antigravity Core Engine, reading the same `antigravity.yaml` manifest and `.antigravity/` workspace state

- Both surfaces connect to the same local runtime, AST index, and workspace configuration
- Changes to model defaults or context globs in `antigravity.yaml` apply to both interfaces immediately
- Session state in `.antigravity/` allows you to start a task in the CLI and inspect it visually in the App

---

# Quiz 2 of 3

**A developer updates `default: gemini-2.5-pro` inside the project’s `antigravity.yaml`, but running `ag generate` in their terminal still uses `gemini-2.5-flash`. No CLI flags were passed. What is the most likely cause?**

- A. `antigravity.yaml` is only read by the graphical Antigravity App, not the CLI
- B. `gemini-2.5-pro` cannot be set as a default model in configuration files
- C. An environment variable such as `ANTIGRAVITY_MODEL=gemini-2.5-flash` is exported in their shell and takes higher precedence
- D. The developer did not commit `antigravity.yaml` to Git before running the command

---

# Quiz 2: Answer

**A developer updates `default: gemini-2.5-pro` inside the project’s `antigravity.yaml`, but running `ag generate` in their terminal still uses `gemini-2.5-flash`. No CLI flags were passed. What is the most likely cause?**

**Correct: C.** An environment variable such as `ANTIGRAVITY_MODEL=gemini-2.5-flash` is exported in their shell and takes higher precedence

- Antigravity resolves configuration in order: CLI flags, then environment variables, then `antigravity.yaml`, and finally global user config
- An exported `ANTIGRAVITY_MODEL` variable in the shell profile overrides the workspace `antigravity.yaml` setting
- Running `ag doctor` or `ag config --effective` reveals which layer supplied each active setting

---

<!-- layout: 2-column -->
# Quiz 3 of 3: Discussion

### Prompt
Your engineering team of 15 developers is adopting Google Antigravity on a large monorepo that contains proprietary backend services, frontend apps, and large build directories.

### Discuss
- Which authentication method should the team standardize on, and why?
- What rules belong in `antigravity.yaml` before committing it to the repository?
- How will you prevent secret leakage and excessive token usage?

---

<!-- layout: 2-column -->
# Quiz 3: Discussion Points

**Your engineering team of 15 developers is adopting Google Antigravity on a large monorepo that contains proprietary backend services, frontend apps, and large build directories.**

### Strong Answers Mention
- Using Google Cloud ADC with Vertex AI for centralized IAM governance and audit logging
- Committing `antigravity.yaml` with strict `context.exclude` patterns for build artifacts, `node_modules/`, and `.env*` files
- Pinning the Antigravity CLI version in the repo so all 15 developers run identical tooling
- Setting `gemini-2.5-flash` as the default model and reserving `gemini-2.5-pro` for complex tasks

### Watch For
- Sharing a single personal `GEMINI_API_KEY` across the team via chat or committed files
- Indexing the entire monorepo without exclusion globs, causing slow prompts and context overflow
- Allowing unreviewed auto-apply of multi-file diffs without `require_diff_approval: true`

---

# Questions and Answers

Questions?
