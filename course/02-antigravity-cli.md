<!-- course-title: Google Antigravity -->
<!-- course-theme: roi-alt-theme -->

<!-- layout: title -->
![ROI Logo](images/roi-logo-with-name.png)

Google Antigravity

# Chapter 2: Working with the Antigravity CLI

---

# Chapter 2: Objectives

- Execute common command patterns and automate code generation tasks with the Antigravity CLI
- Master core CLI commands, syntax conventions, and option flags
- Generate project code and remove boilerplate using Gemini prompts
- Automate repetitive developer tasks across the development lifecycle
- Observe end-to-end CLI workflows in a live demonstration

---

<!-- layout: navigation -->
# Chapter 2

- **Core CLI Structure and Command Patterns**
- Generating Code and Eliminating Boilerplate
- Automating Routine Development Tasks
- Demo: Accelerating Development Workflows with the Antigravity CLI

---

# Anatomy of an Antigravity CLI Command

- Antigravity provides both an interactive terminal interface (`agy`) and a headless scriptable execution mode
- Use `agy -p` with `--mode plan` or `--mode default` to preview proposed diffs in the terminal before modifying your working tree

```bash
# Headless scriptable command grammar
agy -p "<prompt>" [flags]

# Generate a new module with explicit context and preview the diff
agy -p "Create REST endpoints for order creation and status lookup in src/routes/orders.ts using src/models/order.ts" \
  --model gemini-2.5-flash \
  --mode plan

# Interactive session attaching file context with @ references
agy
> Create REST endpoints in @src/routes/orders.ts based on @src/models/order.ts
```

> [!TIP]
> In an interactive `agy` session, press `Shift + Tab` to cycle through execution modes (`default`, `accept-edits`, `plan`) without restarting your session.

---

<!-- layout: card-layout -->
# Core CLI Execution Patterns

### Interactive Agent Session (`agy`)
- Launches an interactive TUI in your terminal for multi-turn conversations and iterative code editing.
- Type prompts naturally and reference files with `@path/file.ts` to attach AST context.

### Headless Execution (`agy -p`)
- Executes prompts non-interactively from shell scripts, terminal pipes, or CI pipelines.
- Supports `--mode accept-edits` for automated file modifications or `--mode plan` for read-only analysis.

### In-Session Slash Commands
- Type `/plan` to outline complex tasks, `/diff` to inspect uncommitted changes, and `/model` to switch models.
- Type `/permissions` to adjust auto-approval rules and `/agents` to orchestrate background subagents.

---

# Essential Option Flags and Context Controls

- Control model selection, execution modes, and output formatting directly from the command line
- Explicit CLI flags override both environment variables and workspace settings in `antigravity.yaml`

| Flag | Purpose | Example Usage |
| :--- | :--- | :--- |
| `--model`, `-m` | Override the default Gemini model for the current run | `--model gemini-2.5-pro` |
| `--mode` | Set execution mode (`default`, `accept-edits`, `plan`, `auto`) | `--mode accept-edits` |
| `-p`, `--print` | Headless execution: run prompt and print output to stdout | `agy -p "Explain auth flow"` |
| `--sandbox` | Run shell and tool executions in an isolated local container | `agy --sandbox` |
| `--output-format` | Emit structured output (`text`, `json`) for scripts | `--output-format json` |

> [!NOTE]
> Pass `--continue` (or `-c`) to resume the most recent interactive session, or `--conversation <id>` to resume a specific historical task.

---

<!-- layout: stacked -->
# How the CLI Assembles Context and Applies Diffs

- **AST-aware context pruning**: instead of sending your entire repository, the CLI parses imports from target files and strips paths matching `context.exclude`
- **Unified diff generation**: Gemini returns structured file patches that can be previewed, piped, or validated automatically
- **Verification gate**: when paired with shell test commands, Antigravity verifies patched code and rolls back automatically on failure

![Target file AST imports are extracted and pruned against excluded paths to assemble a compact context payload, producing structured diff patches that pass verification before writing to disk](images/ch02-cli-context-diff-pipeline.svg)

---

<!-- layout: 2-column -->
# Piping Standard Input and Shell Composition

### Piping Logs and Diffs into `agy`
- Pipe compiler errors, failing test output, or Git diffs directly via `stdin`
- Pass the target prompt so Gemini sees both the failure trace and the task requirements

```bash
# Pipe failing test output into agy headless mode
npm test 2>&1 | agy -p \
  "Fix the token expiration bug in src/auth/jwt.ts" \
  --mode accept-edits

# Summarize staged changes into a commit message
git diff --staged | agy -p \
  "Write a conventional commit message for this diff"
```

### Emitting Structured Output to `stdout`
- Use `--output-format json` to feed downstream CLI tools like `jq`
- Helpful for headless CI checks and automated code review summaries

```bash
# Export structured review findings as JSON
git diff origin/main | agy -p \
  "Review changes for security risks" \
  --output-format json > review-report.json
```

<!-- below-columns -->

> [!NOTE]
> When piping via `stdin`, `agy` automatically merges the standard input stream with project rules defined in `AGENTS.md` and `antigravity.yaml`.

---

<!-- layout: navigation -->
# Chapter 2

- Core CLI Structure and Command Patterns
- **Generating Code and Eliminating Boilerplate**
- Automating Routine Development Tasks
- Demo: Accelerating Development Workflows with the Antigravity CLI

---

# Do Now: Diagnose the Unanchored Prompt

Look at this command before we run it:

```bash
agy -p "Create CRUD handlers for invoices in src/controllers/invoice.controller.ts"
```

- The file `src/controllers/invoice.controller.ts` does not exist yet, so it has no imports for the AST parser to follow
- Write down two things Gemini will have to guess (and likely get wrong) without explicit schema context
- Write down which files and references you would add to anchor the prompt

---

# Scaffolding Code from Existing Types and Schemas

- Boilerplate generation is most reliable when anchored to existing type definitions, database schemas, or OpenAPI specs
- Point `agy` at your domain types and middleware references so generated handlers, validators, and DTOs match your exact interfaces

```bash
# Generate a typed Express controller and Zod validator anchored to an existing schema
agy -p "Generate CRUD controller handlers in src/controllers/invoice.controller.ts using schema in src/schemas/invoice.schema.ts and standard error middleware in src/middleware/error.ts" \
  --mode accept-edits
```

- **Why anchoring works**:
  - Eliminates hallucinated method names and mismatched import paths
  - Adheres to existing error-handling conventions and logging helpers in your repo

---

<!-- layout: 2-column -->
# Ad-Hoc Prompts vs. Project Templates

### Ad-Hoc Prompt Flag (`-p`)
- Ideal for one-off transformations or unique business logic
- Written inline on the command line during interactive work
- Risk: developers phrase instructions differently, creating subtle style drift across services

### Reusable Templates and Rules
- Stored in `.antigravity/templates/` and codified in `AGENTS.md`
- Encapsulates architectural rules, required imports, and naming conventions
- Invoked consistently across team members to maintain uniform coding standards

<!-- below-columns -->

> [!IMPORTANT]
> Convert any prompt you run more than three times into a versioned template in `.antigravity/templates/` so the whole team shares the same architectural pattern.

---

# Defining a Reusable Boilerplate Template

- Templates in `.antigravity/templates/` combine parameterized variables, pinned context globs, and system instructions

```yaml
# .antigravity/templates/service-module.yaml
name: service-module
description: Scaffold a domain service with repository injection and error handling
model: gemini-2.5-flash
variables:
  - name: domain
    required: true
context:
  include:
    - "src/core/base-service.ts"
    - "src/core/errors.ts"
prompt: |
  Create a TypeScript service class for the {{domain}} domain extending BaseService.
  Include typed create, getById, and list methods using AppError for not-found cases.
```

```bash
# Apply template instructions from the CLI
agy -p "Apply template service-module with domain=shipping to src/services/shipping.service.ts" \
  --mode accept-edits
```

---

<!-- layout: 3-column -->
# Guardrails for Safe Code Generation

### Scope Target Context
- Pass explicit file paths or use `@file` references instead of broad directory wildcards
- Keeps token consumption low and prevents unintended edits to unrelated modules

### Inspect Diffs First
- Run `--mode plan` or interactive mode when modifying core shared libraries
- Verify imports and edge-case handling before writing changes to disk

### Validate Immediately
- Pair generation commands with your test runner or linter to verify changes
- Example: running `npx tsc --noEmit` immediately catches type mismatches

---

<!-- layout: navigation -->
# Chapter 2

- Core CLI Structure and Command Patterns
- Generating Code and Eliminating Boilerplate
- **Automating Routine Development Tasks**
- Demo: Accelerating Development Workflows with the Antigravity CLI

---

<!-- layout: card-layout -->
# High-Leverage Tasks to Automate with the CLI

### Unit Test Generation
- Inspect untested branches and edge cases in existing modules using `agy`.
- Automatically aligns assertions and mocks with Jest, Vitest, or Pytest conventions in your repo.

### Multi-File Refactoring
- Migrate deprecated APIs, rename domain concepts, or convert callbacks to `async/await` across modules.
- Route complex cross-file changes to `gemini-2.5-pro` with plan review to guarantee passing tests.

### Pre-Commit Review and Docs
- Run `agy` on staged Git diffs before committing to flag leaked secrets, unhandled promises, or missing docstrings.
- Generate changelog entries, OpenAPI specs, and pull request descriptions directly from Git history.

---

# Automating Unit Test Generation and Verification

- Use `agy` to analyze control flow in a source file, scaffold a matching test file, and execute your test runner in one loop
- Pairing automated generation with test runner execution ensures changes are verified before committing

```bash
# Generate unit tests for pricing logic and verify they pass in Vitest
agy -p "Generate unit tests in tests/billing/discount-calculator.test.ts for src/billing/discount-calculator.ts based on src/billing/types.ts covering boundary conditions and negative totals" \
  --mode accept-edits

# Run test runner to verify green status
npx vitest run tests/billing/discount-calculator.test.ts
```

> [!TIP]
> Reference an existing test file in the prompt (for example, `@tests/billing/tax.test.ts`) so Gemini mirrors your team’s exact mocking and fixture setup.

---

# Declarative Workflows in antigravity.yaml

- Define repeatable, multi-step workflows under the `workflows:` key in `antigravity.yaml` and execute them via automation scripts
- Workflows can chain code generation, verification commands, or stdin pipes into a single command

```yaml
# Excerpt from antigravity.yaml
workflows:
  scaffold-endpoint:
    description: Generate route, controller, and verified unit tests
    steps:
      - name: generate-handler
        run: agy -p "Scaffold route for {{name}}" --mode accept-edits
      - name: generate-tests
        run: agy -p "Scaffold tests for {{name}}" --mode accept-edits
      - name: verify-types-and-tests
        shell: npm run typecheck && npx vitest run tests/routes/{{name}}.test.ts
  commit-summary:
    description: Generate a conventional commit message from piped git diff
    steps:
      - name: summarize-diff
        run: agy -p "Write a conventional commit message for this diff" --model gemini-2.5-flash
```

---

<!-- layout: 2-column -->
# Integrating CLI Workflows into Git Hooks and CI

### Local Pre-Commit Hook (`.husky/pre-commit`)
- Keep pre-commit checks under a few seconds by routing staged diffs to `gemini-2.5-flash`
- Block commits only on high-severity findings

```bash
#!/usr/bin/env sh
# Run fast AI review on staged changes
git diff --staged | agy -p \
  "Review staged changes for regressions and security bugs" \
  --model gemini-2.5-flash
```

### Headless CI Pipeline Execution
- Always pass `--dangerously-skip-permissions` in sandboxed CI so the runner never hangs waiting for terminal input
- Supply credentials via masked CI secrets (`GEMINI_API_KEY` or Workload Identity)

```bash
# Run automated PR review in CI
git diff origin/main | agy -p \
  "Audit pull request changes" \
  --dangerously-skip-permissions \
  --output-format json > pr-audit.json
```

<!-- below-columns -->

> [!CAUTION]
> Always verify test suites before committing AI-generated changes in an automated pipeline. Unverified automated commits can introduce build failures directly into your branch.

---

<!-- layout: navigation -->
# Chapter 2

- Core CLI Structure and Command Patterns
- Generating Code and Eliminating Boilerplate
- Automating Routine Development Tasks
- **Demo: Accelerating Development Workflows with the Antigravity CLI**

---

<!-- layout: card-layout -->
# Demo: Accelerating Workflows with the CLI

### Step 1: Scaffold a Typed Endpoint
- Inspect an existing TypeScript domain model (`src/models/subscription.ts`).
- Run `agy -p` with `--mode plan` to preview the REST handler, then apply the patch.

### Step 2: Generate and Verify Unit Tests
- Run `agy -p` against the newly created subscription handler to generate unit tests.
- Watch the CLI generate edge-case tests and immediately execute them with Vitest to confirm green status.

### Step 3: Pipe a Diff into Automated Review
- Stage the generated files with Git and pipe `git diff --staged` into `agy -p`.
- Generate a conventional commit message directly from the staged diff.

---

# Demo Step 1: Scaffolding Code with Context and Plan Mode

- First, we preview the generated handler using `--mode plan` to inspect how Antigravity uses our domain types, then write the file

```bash
# 1. Inspect the existing domain model
cat src/models/subscription.ts

# 2. Preview generated route handler in plan mode without modifying disk
agy -p "Create Express routes in src/routes/subscription.routes.ts for subscriptions using src/models/subscription.ts and src/middleware/auth.ts" \
  --mode plan

# 3. Apply the generation and typecheck immediately
agy -p "Create Express routes in src/routes/subscription.routes.ts for subscriptions using src/models/subscription.ts and src/middleware/auth.ts" \
  --mode accept-edits
npm run typecheck
```

---

<!-- layout: 2-column -->
# Demo Step 2 and 3: Automated Testing and Staged Review

### Terminal Commands
```bash
# 4. Generate and verify unit tests
agy -p "Generate unit tests in tests/subscription.routes.test.ts for src/routes/subscription.routes.ts" \
  --mode accept-edits
npx vitest run tests/subscription.routes.test.ts

# 5. Stage files and run pre-commit AI review
git add src/routes/subscription.routes.ts \
        tests/subscription.routes.test.ts
git diff --staged | agy -p \
  "Review staged changes for security and missing error handling" \
  --model gemini-2.5-flash

# 6. Generate conventional commit message
git diff --staged | agy -p \
  "Write a conventional commit message for this diff"
```

### What to Watch For
- `agy -p --mode accept-edits` writes the test suite, and `vitest` confirms all assertions pass
- `agy -p` reads the piped Git diff and checks for missing authentication middleware or unhandled errors
- The commit message prompt prints `feat(subscriptions): add create, pause, and cancel routes with unit tests`

---

# What You Learned

- Executed common command patterns and automated code generation tasks with the Antigravity CLI
- Mastered core CLI commands, syntax conventions, and option flags
- Generated project code and removed boilerplate using Gemini prompts
- Automated repetitive developer tasks across the development lifecycle
- Observed end-to-end CLI workflows in a live demonstration

---

# Quiz 1 of 3

**You want to generate a refactoring plan for an authentication middleware file without allowing the agent to modify any files on disk yet. Which CLI execution mode should you specify?**

- A. `--mode accept-edits`
- B. `--mode plan`
- C. `--output-format json`
- D. `--sandbox`

---

# Quiz 1: Answer

**You want to generate a refactoring plan for an authentication middleware file without allowing the agent to modify any files on disk yet. Which CLI execution mode should you specify?**

**Correct: B.** `--mode plan`

- `--mode plan` runs in read-only planning mode, inspecting files and drafting a detailed step-by-step implementation plan before touching code
- `--mode accept-edits` automatically approves and applies file modifications without prompting
- `--output-format json` controls output serialization, and `--sandbox` restricts bash execution within an isolated container

---

# Quiz 2 of 3

**Your team notices that when different developers run natural language prompts to create new microservice endpoints, the generated controllers use inconsistent error handling and logging imports. What is the best way to standardize the output?**

- A. Require everyone to use `gemini-2.5-pro` with `--temperature 1.0`
- B. Disable the Antigravity CLI and write all boilerplate manually
- C. Codify repository rules in `AGENTS.md` and create parameterized templates in `.antigravity/templates/`
- D. Pass `--dangerously-skip-permissions` on every workstation

---

# Quiz 2: Answer

**Your team notices that when different developers run natural language prompts to create new microservice endpoints, the generated controllers use inconsistent error handling and logging imports. What is the best way to standardize the output?**

**Correct: C.** Codify repository rules in `AGENTS.md` and create parameterized templates in `.antigravity/templates/`

- Guidelines in `AGENTS.md` and templates in `.antigravity/templates/` lock in standard system instructions, required context files (such as shared error and logger modules), and variable placeholders
- Committing templates and rules to version control ensures every developer invokes the exact same pattern
- Raising temperature to `1.0` increases randomness rather than consistency

---

<!-- layout: 2-column -->
# Quiz 3 of 3: Discussion

### Prompt
Your team wants to add Antigravity CLI automation to both your local Git pre-commit hook and your GitHub Actions pull request pipeline.

### Discuss
- Which tasks and Gemini models belong in the local pre-commit hook versus the CI pipeline?
- Which CLI flags are mandatory when running `agy` inside a headless CI job?
- How should you handle code generation versus read-only review in CI?

---

<!-- layout: 2-column -->
# Quiz 3: Discussion Points

**Your team wants to add Antigravity CLI automation to both your local Git pre-commit hook and your GitHub Actions pull request pipeline.**

### Strong Answers Mention
- Using `gemini-2.5-flash` on `git diff --staged` in pre-commit hooks so checks finish in a few seconds
- Reserving deeper multi-file analysis with `gemini-2.5-pro` for asynchronous CI pull request checks
- Passing `--dangerously-skip-permissions` and `--output-format json` in CI jobs so pipelines never hang on user prompts
- Keeping CI pipelines read-only (auditing diffs) or strictly gating any automated edits behind test validation

### Watch For
- Running slow, full-repository `gemini-2.5-pro` prompts on every local `git commit`, frustrating developers
- Forgetting non-interactive flags in CI, causing builds to time out waiting for approval
- Auto-committing unverified AI code edits directly on the main branch in CI

---

# Questions and Answers

Questions?
