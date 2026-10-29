# docs-optimizer examples

Real responses from the [`docs-optimizer`](SKILL.md) skill, unedited. Each one was produced from a single run on the current `SKILL.md`, using the default Claude model in the author's agent harness (October 2026). The skill was loaded as the system prompt with no tools. It is designed to read the instruction files, check commands and paths against the repo, and edit files directly; none of that was possible here, so the instruction files were pasted into each request, and the responses propose changes and say what they could not check.

Technical details in the responses, such as how each agent tool loads instruction files, come from the model and have not been verified. Treat them as examples of how the skill reasons and formats its answer, not as documentation for any tool.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Fixing stale facts](#fixing-stale-facts)
  - [Stale npm commands in a pnpm repo](#stale-npm-commands-in-a-pnpm-repo)
- [Conflicts that need a decision](#conflicts-that-need-a-decision)
  - [Squash merges versus merge commits](#squash-merges-versus-merge-commits)
- [Cutting what does not constrain](#cutting-what-does-not-constrain)
  - [A bloated CLAUDE.md](#a-bloated-claudemd)
  - [A generated AGENTS.md](#a-generated-agentsmd)
- [Loading behavior before moving or merging](#loading-behavior-before-moving-or-merging)
  - [The same rules in three files](#the-same-rules-in-three-files)
  - [Moving a migration runbook out of the root](#moving-a-migration-runbook-out-of-the-root)
  - [Duplicates in a nested AGENTS.md](#duplicates-in-a-nested-agentsmd)
  - [A CLAUDE.md that says "See AGENTS.md"](#a-claudemd-that-says-see-agentsmd)
- [Leaving good instructions alone](#leaving-good-instructions-alone)
  - [An AGENTS.md that is already lean](#an-agentsmd-that-is-already-lean)
- [Holding position under pushback](#holding-position-under-pushback)
  - ["Just get it under 20 lines"](#just-get-it-under-20-lines)

---

## Fixing stale facts

_Commands and paths are checked against the evidence supplied, and what could not be checked is named._

### Stale npm commands in a pnpm repo

**Request:** `/docs-optimizer` Fix the stale stuff in our AGENTS.md. I've pasted it and our package.json.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md`:

```markdown
# AGENTS.md

## Setup
- Install deps with `npm install`.
- Run tests with `npm test`. All tests must pass before you open a PR.
- Lint with `npm run lint`.

## Structure
- API handlers live in `src/routes/`.
- Shared types live in `src/types.ts`.

## Rules
- Never edit files in `src/generated/`. Regenerate them with `npm run codegen`.
```

`package.json`:

```json
{
  "name": "orders-api",
  "packageManager": "pnpm@9.12.0",
  "scripts": {
    "test:unit": "vitest run",
    "test:e2e": "playwright test",
    "lint": "eslint . && tsc --noEmit",
    "gen:api": "openapi-typescript openapi.yaml -o src/generated/api.ts"
  }
}
```

`src/routes/` was renamed to `src/handlers/` last month. `src/types.ts` was split into `src/types/*.ts`.

</details>

**Response:**

> Here's the corrected `AGENTS.md`:
>
> ```markdown
> # AGENTS.md
>
> ## Setup
> - Install deps with `pnpm install`.
> - Run unit tests with `pnpm test:unit` and end-to-end tests with `pnpm test:e2e`. All tests must pass before you open a PR.
> - Lint and type-check with `pnpm lint`.
>
> ## Structure
> - API handlers live in `src/handlers/`.
> - Shared types live in `src/types/`.
>
> ## Rules
> - Never edit files in `src/generated/`. Regenerate them with `pnpm gen:api`.
> ```
>
> #### What changed
>
> - **Package manager:** `package.json` pins `pnpm@9.12.0`, so I replaced the `npm` commands with `pnpm`.
> - **Tests:** There is no `test` script, so `npm test` would fail. I replaced it with the two scripts that exist, `test:unit` and `test:e2e`. I kept "All tests must pass" as written, so it still covers both suites.
> - **Lint:** The command now notes that `lint` also runs `tsc --noEmit`. This tells agents that type errors will fail the lint step.
> - **Codegen:** `codegen` doesn't exist. I replaced it with `gen:api`.
> - **Paths:** I updated `src/routes/` to `src/handlers/` and `src/types.ts` to `src/types/`, based on the renames you described.
>
> #### Open questions
>
> - **E2E before PRs:** Should agents run Playwright tests before every PR? They may need a running server or installed browsers. If not, change the rule to name only `test:unit`.
> - **Other generated files:** `gen:api` writes only `src/generated/api.ts`. If `src/generated/` contains files produced some other way, the regenerate instruction needs that command too.
>
> #### What I checked
>
> I checked every command against the scripts in your `package.json`. I didn't check the paths against the repo. I relied on your description of the renames.

---

## Conflicts that need a decision

_When evidence cannot settle a conflict, the response keeps both rules and asks a focused question._

### Squash merges versus merge commits

**Request:** `/docs-optimizer` Audit these two instruction files for conflicts. Don't edit anything yet.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md`:

```markdown
# AGENTS.md
- Squash-merge every PR so `main` has one commit per change.
- Use Conventional Commits for PR titles.
- Run `make check` before pushing.
```

`.cursor/rules/git.mdc`:

```markdown
---
description: Git workflow
alwaysApply: true
---
- Use merge commits. Do not squash; reviewers rely on the individual commits.
- Commit messages: imperative mood, max 72 characters in the subject.
- Run `make check` before pushing.
```

The repo's GitHub settings allow both squash and merge commits. Recent history on `main` has a mix of both.

</details>

**Response:**

> #### Audit: `AGENTS.md` vs `.cursor/rules/git.mdc`
>
> I made no edits.
>
> #### Direct conflict: merge strategy
>
> - `AGENTS.md` says: "Squash-merge every PR so `main` has one commit per change."
> - `git.mdc` says: "Use merge commits. Do not squash; reviewers rely on the individual commits."
>
> The two rules can't both be followed, and both give a reason. The evidence doesn't settle which one is intended.
> - **GitHub settings** allow both methods.
> - **History on `main`** mixes both, so it shows the inconsistency rather than a policy.
>
> This is a policy decision, so I won't pick a winner. The conflict likely causes the mixed history.
> - Codex and other tools that read only `AGENTS.md` will squash.
> - Cursor applies `git.mdc` on every request because `alwaysApply: true`.
> - Recent Cursor versions also read `AGENTS.md`. If yours does, Cursor agents see both rules, and the result depends on Cursor's precedence. I haven't verified your Cursor version or how it resolves the conflict.
>
> #### Possible tension: commit and PR conventions
>
> These rules don't strictly conflict, but they cover different things:
> - `AGENTS.md` sets **PR titles** to Conventional Commits.
> - `git.mdc` sets **commit subjects** to imperative mood with at most 72 characters.
>
> The two formats can work together. For example, `fix: handle empty config` follows both. Which rule matters depends on the merge decision:
> - **If you squash:** the PR title usually becomes the commit subject on `main`. Both rules then apply to the same line, but the 72-character limit appears only in the Cursor file.
> - **If you use merge commits:** individual commit messages land on `main`. Only Cursor agents are told how to format them.
>
> #### Duplicate: `make check`
>
> Both files say "Run `make check` before pushing." with the same scope and meaning. Keep both copies until we confirm that every tool you use loads `AGENTS.md`. Otherwise, removing one could drop the rule for some agents. I didn't check that a `check` target exists in the `Makefile`.
>
> #### Questions
>
> 1. **Merge strategy:** should PRs be squash-merged or merged with merge commits?
> 2. **Commit conventions:** should both conventions apply to every agent? For example, Conventional Commits for PR titles plus the 72-character imperative subject for commits.
> 3. **Single source:** do you want shared git rules kept only in `AGENTS.md`? This only works if Cursor loads it in your setup.
>
> Once you decide, I can update both files so every tool gives the same instructions.

---

## Cutting what does not constrain

_Persona, generic advice, reasoning scripts, and filler go; project facts and safeguards stay._

### A bloated CLAUDE.md

**Request:** `/docs-optimizer` Optimize our CLAUDE.md. It's loaded on every request and feels bloated.

<details>
<summary>Pasted with the request</summary>

`CLAUDE.md`:

```markdown
# CLAUDE.md

You are a world-class senior staff engineer with 20 years of experience. You write CRITICALLY clean, maintainable code.

## How to think
Before every response:
1. Restate the user's request in your own words.
2. List all assumptions.
3. Think step by step.
4. Consider at least three alternative approaches.
5. Pick the best one and explain why.
6. Write the code.
7. Review your code for bugs.

## General guidelines
- Write clean, readable code.
- Use meaningful variable names.
- Follow best practices.
- Add comments where helpful.
- Handle errors appropriately.
- Write tests for new features.

## Project
- Django 5 app. Python 3.12. Dependencies managed with `uv`.
- Run tests with `uv run pytest`. Run a single test with `uv run pytest path::test_name`.
- Never edit migrations that have been merged to `main`; create a new migration instead. A rewritten migration broke production in March.
- Settings come from environment variables via `django-environ`. Never hard-code secrets.

## Sprint notes
- Sprint 41: Finish moving billing to Stripe Checkout (Dana).
- Remember to ask Sam about the flaky `test_invoice_pdf` test.

## Response format
Always respond with these sections: ## Summary, ## Assumptions, ## Approach, ## Code, ## Tests, ## Risks, ## Next steps.
```

</details>

**Response:**

> Here's the revised `CLAUDE.md`:
>
> ```markdown
> # CLAUDE.md
>
> ## Project
> - Django 5 app on Python 3.12. Dependencies are managed with `uv`.
> - Run tests with `uv run pytest`. Run a single test with `uv run pytest path::test_name`.
> - Write tests for new features.
> - Never edit migrations that have been merged to `main`. Create a new migration instead. A rewritten migration broke production in March.
> - Settings come from environment variables via `django-environ`. Never hard-code secrets.
> ```
>
> #### What changed
>
> - **Removed the persona line.** It adds no constraint the agent can act on.
> - **Removed the "How to think" script.** It forced restating, listing assumptions, and comparing three approaches on every request, including trivial ones. That costs output on every turn without a project-specific benefit.
> - **Removed the generic guidelines.** Lines like "clean code," "meaningful names," and "best practices" are default behavior. I kept "Write tests for new features" because it is a real obligation, and moved it next to the test commands.
> - **Removed the sprint notes.** These were time-bound team reminders, not instructions for the agent.
> - **Removed the fixed seven-section response format.** It made every answer long, even for one-line questions. If a reviewer or tool depends on that structure, restore it, ideally scoped to the cases that need it, such as feature work.
> - **Kept the Project section intact.** I kept every obligation, the migration rule and its reason, and the exact commands.
>
> #### Size
>
> The file went from roughly 260 words to roughly 75. That is a word-count estimate, not measured tokens. The larger practical saving is probably shorter responses, because the reasoning script and section template no longer drive output length.
>
> #### Unverified / for you to decide
>
> - I only had `CLAUDE.md`, so I didn't check `uv run pytest` or the `django-environ` claim against `pyproject.toml` or the settings. Please confirm they're current.
> - **`test_invoice_pdf` is flaky.** If it's still flaky, a line like this could stop the agent from chasing false failures: "`test_invoice_pdf` is known to be flaky; rerun before investigating." I didn't add it because I can't confirm it's still true.
> - **Stripe Checkout migration.** If billing code is mid-migration, a durable note about which billing path is current may be worth adding.

---

### A generated AGENTS.md

**Request:** `/docs-optimizer` Trim this AGENTS.md down. Half of it is boilerplate.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md`:

```markdown
<!-- GENERATED by scripts/build-agents.ts from agents/*.md. Do not edit by hand. -->
# AGENTS.md

## About this file
This file gives AI agents instructions for working in this repository. Read it carefully before making changes. These instructions help you do a good job.

## Commands
- Build: `bun run build`
- Test: `bun test`
- Typecheck: `bun run typecheck`

## Conventions
- Use the `Result` type from `src/lib/result.ts` for recoverable errors. Do not throw for expected failures.
- Keep React components in `src/ui/`. Keep business logic out of components.

## Thank you
Thanks for helping improve this project! Your contributions are appreciated.
```

</details>

**Response:**

> This `AGENTS.md` is generated. Its first line says it comes from `scripts/build-agents.ts` and `agents/*.md`. If I edit it directly, the next build will overwrite the change. I can't see the source files, so I haven't edited anything. Here is the proposed trim.
>
> #### Proposed result
>
> ```markdown
> <!-- GENERATED by scripts/build-agents.ts from agents/*.md. Do not edit by hand. -->
> # AGENTS.md
>
> ## Commands
> - Build: `bun run build`
> - Test: `bun test`
> - Typecheck: `bun run typecheck`
>
> ## Conventions
> - Use the `Result` type from `src/lib/result.ts` for recoverable errors. Do not throw for expected failures.
> - Keep React components in `src/ui/`. Keep business logic out of components.
> ```
>
> #### What changes and why
>
> - **Remove "About this file."** Agents already load this file. The section adds no constraint.
> - **Remove "Thank you."** It is filler with no instruction.
> - **Keep everything else as written.** The commands and both conventions are repo-specific facts that agents can't infer.
> - **Keep the generated-file header.** It stops agents and humans from editing the output by hand.
>
> #### How to apply it
>
> 1. In `agents/*.md`, find the source file or files for the "About this file" and "Thank you" sections and delete them.
> 2. Check whether `scripts/build-agents.ts` adds either section itself, for example as a fixed preamble or footer. If it does, remove it there.
> 3. Regenerate the file with the repo's build step for this script, then confirm the output matches the version above.
>
> #### Not verified
>
> - I couldn't confirm that `bun run build` and `bun run typecheck` exist in `package.json`, that `src/lib/result.ts` exists, or how `AGENTS.md` gets regenerated.
> - If you share `agents/*.md` and `scripts/build-agents.ts`, I can make these edits in the source files directly.
>
> Rough size: about 110 words before and about 65 after, by word count, not measured tokens.

---

## Loading behavior before moving or merging

_Rules move or merge only when the response can say how each agent tool will still load them._

### The same rules in three files

**Request:** `/docs-optimizer` We have the same rules in three places. Deduplicate them so each rule lives once. Our team uses Claude Code, Cursor, and Codex.

<details>
<summary>Pasted with the request</summary>

`CLAUDE.md`:

```markdown
# CLAUDE.md
- Run `just test` before committing.
- Never commit `.env` files.
- Use `structlog` for logging, not `print`.
```

`AGENTS.md`:

```markdown
# AGENTS.md
- Run `just test` before committing.
- Never commit `.env` files.
- Use `structlog` for logging, not `print`.
- In `services/payments/`, every money amount is an integer number of cents.
```

`.cursor/rules/python.mdc`:

```markdown
---
globs: ["**/*.py"]
---
- Use `structlog` for logging, not `print`.
- Never commit `.env` files.
```

</details>

**Response:**

> I made `AGENTS.md` the single source and had `CLAUDE.md` import it. I left the Cursor rule file unchanged until you confirm one loading detail.
>
> #### Changes
>
> **`AGENTS.md`** stays as the single source of the rules, unchanged:
>
> ```markdown
> # AGENTS.md
> - Run `just test` before committing.
> - Never commit `.env` files.
> - Use `structlog` for logging, not `print`.
> - In `services/payments/`, every money amount is an integer number of cents.
> ```
>
> **`CLAUDE.md`** now contains only an import:
>
> ```markdown
> # CLAUDE.md
> @AGENTS.md
> ```
>
> Claude Code does not read `AGENTS.md` by itself. It does load files referenced with the `@path` import syntax in `CLAUDE.md`. A plain Markdown link would not load the file.
>
> This also changes behavior. Before, Claude Code never saw the payments rule about integer cents, because only `AGENTS.md` had it. Now it does. I assume that is what you want, since the rule reads like a project-wide constraint. Tell me if Claude Code users should not see it.
>
> Codex already reads `AGENTS.md`, so nothing changes for Codex.
>
> #### Unresolved: `.cursor/rules/python.mdc`
>
> I left this file as it is. Removing it is safe only if your team's Cursor setup loads the root `AGENTS.md`. Recent Cursor versions do, but I couldn't verify your version or settings.
>
> - **If Cursor loads `AGENTS.md`:** delete `python.mdc`. Both of its rules are already in `AGENTS.md`, and they would then apply everywhere, not only while editing `*.py` files. That is more correct for the `.env` rule, which isn't Python-specific.
> - **If Cursor doesn't load it, or you're unsure:** keep `python.mdc`. Otherwise Cursor loses both rules and never sees the `just test` or payments rules. You could also replace it with a rule that has `alwaysApply: true` and points to `AGENTS.md`. However, a reference inside a rule doesn't reliably load the referenced file, so only do this if you have tested it.
>
> **Question:** Does everyone's Cursor load `AGENTS.md`? You can test it by asking Cursor in a fresh chat what the payments rule is.
>
> #### Checked
>
> - All three rules from `CLAUDE.md` exist word-for-word in `AGENTS.md`, so no rule was lost.
> - Codex loading is unchanged.
> - **Not verified:** that `@AGENTS.md` resolves in your Claude Code version. Run `/memory` in Claude Code to confirm the imported file is listed.
> - **Not verified:** that `just test` exists in your `justfile`.

---

### Moving a migration runbook out of the root

**Request:** `/docs-optimizer` Our root AGENTS.md is mostly a database migration runbook. Move that to `docs/migrations.md` and leave a link so the root file is small. We use Codex.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md`:

```markdown
# AGENTS.md

## Commands
- Test: `make test`
- Lint: `make lint`

## Database migrations
Migrations use Alembic and live in `db/migrations/`.

1. Create a migration with `make migration name=<short_name>`.
2. Never edit a migration after it is merged to `main`.
3. Every migration must be reversible. Write `downgrade()` and test it with `make db-roundtrip`.
4. Adding a NOT NULL column to a large table: add it nullable, backfill in batches with `scripts/backfill.py`, then add the constraint in a separate migration.
5. Never rename a column in one step. Add the new column, dual-write, backfill, switch reads, then drop the old column in a later release.
6. Index creation on `orders` and `events` must use `CREATE INDEX CONCURRENTLY` inside `op.get_context().autocommit_block()`.
7. Run `make db-lint` (squawk) before opening a PR.
```

</details>

**Response:**

> I moved the runbook to `docs/migrations.md` and replaced it in the root with a read-before-acting instruction instead of a bare link. Codex loads `AGENTS.md` files from the repo root down to the working directory. It does not follow Markdown links, so a plain link would leave the migration rules unseen unless the agent opened the file itself.
>
> **`AGENTS.md`**
>
> ```markdown
> # AGENTS.md
>
> ## Commands
> - Test: `make test`
> - Lint: `make lint`
>
> ## Database migrations
> Before creating, editing, or reviewing anything in `db/migrations/`, or changing database schema in any way, read `docs/migrations.md`.
> ```
>
> **`docs/migrations.md`**
>
> ```markdown
> # Database migrations
>
> Migrations use Alembic and live in `db/migrations/`.
>
> 1. Create a migration with `make migration name=<short_name>`.
> 2. Never edit a migration after it is merged to `main`.
> 3. Every migration must be reversible. Write `downgrade()` and test it with `make db-roundtrip`.
> 4. Adding a NOT NULL column to a large table: add it nullable, backfill in batches with `scripts/backfill.py`, then add the constraint in a separate migration.
> 5. Never rename a column in one step. Add the new column, dual-write, backfill, switch reads, then drop the old column in a later release.
> 6. Index creation on `orders` and `events` must use `CREATE INDEX CONCURRENTLY` inside `op.get_context().autocommit_block()`.
> 7. Run `make db-lint` (squawk) before opening a PR.
> ```
>
> **Notes**
>
> - **Rules preserved:** The runbook moved word for word. Every "never" and "must" stays as strong as before, and every command and identifier is unchanged.
> - **Trigger scope:** The trigger also covers schema changes outside `db/migrations/`. A task like "add a column to `orders`" may begin in model code, and that is where rules 4–6 matter most.
> - **Why not a nested `AGENTS.md`:** I did not use `db/migrations/AGENTS.md`. Codex only loads nested files when it runs from that directory, not when it edits files there. That would be less reliable than the root trigger.
> - **Walkthrough:** For the task "rename `users.email` to `users.email_address`," the root file sends the agent to `docs/migrations.md` before it touches the schema. There it finds the multi-release rename procedure (rule 5).
> - **Remaining risk:** An agent could still skip the read instruction. If one migration rule is critical enough that a miss is unacceptable, consider copying it into the root as a one-liner. Rule 2 ("never edit a merged migration") is the likely candidate. I left that decision to you.
> - **Size (word count, not measured tokens):** The always-loaded root dropped from about 150 words to about 40. Migration tasks now cost one extra file read.
> - **Unverified:** I did not check that `make migration`, `make db-roundtrip`, `make db-lint`, or `scripts/backfill.py` exist. I did not check whether other files link to the old root section either.

---

### Duplicates in a nested AGENTS.md

**Request:** `/docs-optimizer` Clean up the instructions in `packages/web/AGENTS.md`. It repeats a lot of the root file, so remove the duplicates.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md (repo root)`:

```markdown
# AGENTS.md
- Monorepo managed with Turborepo and pnpm workspaces.
- Run `pnpm turbo test --filter=<package>` to test one package.
- Never import across packages with relative paths; use the workspace package name.
```

`packages/web/AGENTS.md`:

```markdown
# Web package
- Monorepo managed with Turborepo and pnpm workspaces.
- Run `pnpm turbo test --filter=web` to test this package.
- Never import across packages with relative paths; use the workspace package name.
- Next.js 15 App Router. Server components by default; add "use client" only when a component needs state or browser APIs.
- Use `next/image` for all images.
```

</details>

**Response:**

> I haven't edited the file yet. Removing these duplicates is only safe if every agent tool you use loads the root `AGENTS.md` together with `packages/web/AGENTS.md`, and I can't confirm that from these two files.
>
> #### Why it matters
>
> Agent tools load nested `AGENTS.md` files differently:
>
> - **Some tools combine them.** They load the root file and every `AGENTS.md` on the path to the files being edited. With those tools, the repeated lines are redundant.
> - **Some tools use only the nearest file.** An agent working in `packages/web/` would see only the package file. Deleting the shared lines would remove the cross-package import rule and the Turborepo context for that agent.
>
> The import rule is the main risk. It prevents a real failure, and agents tend to break it when they work inside a single package.
>
> #### Proposed version
>
> Use this version if every tool you use loads both files:
>
> ```markdown
> # Web package
> - Run `pnpm turbo test --filter=web` to test this package.
> - Next.js 15 App Router. Server components by default; add "use client" only when a component needs state or browser APIs.
> - Use `next/image` for all images.
> ```
>
> Changes:
>
> - **Removed** the monorepo line and the cross-package import rule. Both match the root file in wording and scope.
> - **Kept** the test command. It looks similar to the root line, but it gives the actual filter value for this package, so an agent doesn't have to look it up.
> - **Kept** the Next.js and `next/image` rules unchanged. They apply only to this package and aren't in the root file.
>
> #### Still to confirm
>
> 1. **Which agent tools do you use, and do they all load the root file alongside nested ones?** If yes, I'll apply the version above. If any tool reads only the nearest file, I recommend keeping the import rule in the package file.
> 2. **Is the package's `name` in `packages/web/package.json` exactly `web`?** Turborepo's `--filter` matches the package name, not the folder name. If the name is scoped, such as `@acme/web`, the command needs to use that name. I haven't checked this.

---

### A CLAUDE.md that says "See AGENTS.md"

**Request:** `/docs-optimizer` Our CLAUDE.md just says "See AGENTS.md for instructions." Does Claude Code load AGENTS.md from that?

**Response:**

> **No, not automatically.** Claude Code loads `CLAUDE.md` into context at startup, but it treats "See AGENTS.md for instructions." as plain text. That sentence does not load `AGENTS.md`. The agent might decide to open the file with a read tool, but nothing guarantees it will do so before acting. Claude Code also doesn't treat `AGENTS.md` as a native instruction file.
>
> To make it load reliably, use Claude Code's import syntax in `CLAUDE.md`:
>
> ```markdown
> @AGENTS.md
> ```
>
> An `@path` line imports that file's contents into context alongside `CLAUDE.md`. Relative paths resolve from the file that contains the import.
>
> Notes:
>
> - **The import is the safer option.** A symlink from `CLAUDE.md` to `AGENTS.md` also works on most setups. However, symlinks can behave inconsistently on Windows checkouts and in some tooling.
> - **Keep the import as the first line.** You can add Claude-specific rules below it if you need any.
> - **Check your version.** Import behavior and native `AGENTS.md` support can change between Claude Code releases. To verify, start a session and run `/memory`, which lists the loaded memory files. You can also ask Claude to quote a distinctive rule from `AGENTS.md` without reading any files.
>
> I haven't inspected your repo or your Claude Code version, so check with `/memory` before you rely on this.

---

## Leaving good instructions alone

_A file that is already lean stays unchanged._

### An AGENTS.md that is already lean

**Request:** `/docs-optimizer` Optimize our AGENTS.md.

<details>
<summary>Pasted with the request</summary>

`AGENTS.md`:

```markdown
# AGENTS.md

- Go 1.23 service. Build with `make build`; test with `make test` (runs `go test -race ./...`).
- Run `make generate` after changing any `.proto` file in `api/`. Commit the generated code.
- Integration tests need Docker. Run them with `make test-integration`.
- Do not add dependencies without asking; we vendor everything in `vendor/`.
- Errors: wrap with `fmt.Errorf("...: %w", err)`. Do not use `github.com/pkg/errors`.
- Before changing anything in `internal/billing/`, read `internal/billing/README.md`. It documents rounding rules that the tests do not fully cover.
```

</details>

**Response:**

> I left `AGENTS.md` unchanged. It is already lean, and every line earns its place.
>
> **Why each line stays:**
>
> - **Build and test commands:** These are exact commands an agent cannot guess. The note that `make test` uses `-race` matters when interpreting failures.
> - **`make generate` after `.proto` changes:** This step is easy to miss, and skipping it causes stale-code failures.
> - **Docker for integration tests:** This explains failures that would otherwise be confusing.
> - **No new dependencies without asking:** This is a project policy that cannot be inferred from the code.
> - **Error wrapping, and no `pkg/errors`:** This is a project decision. The ban is useful because the package might still be present in `vendor/`.
> - **Read `internal/billing/README.md` first:** This line is the right pattern. It sets a clear trigger near the entry point and points to the detail elsewhere. It also explains why the read is necessary.
>
> I did not merge, shorten, or move anything. Any further compression would weaken conditions or drop the reasons that make these rules stick.
>
> **Not verified, because I only had this file:**
>
> - That the `Makefile` defines `build`, `test`, `test-integration`, and `generate`, and that `test` runs `go test -race ./...`.
> - That `go.mod` targets Go 1.23.
> - That `.proto` files live in `api/`.
> - That `internal/billing/README.md` exists.
>
> If you share the `Makefile`, `go.mod`, and the repo layout, I can check these claims and fix any that are stale.

---

## Holding position under pushback

_A request to cut harder, with no new facts, does not remove safeguards._

### "Just get it under 20 lines"

**Request:** `/docs-optimizer` Earlier you optimized our 140-line AGENTS.md down to 38 lines. You kept the Terraform rules ("Never run `terraform apply` locally; CI applies on merge to `main`" and "Run `terraform plan` against the `staging` workspace, never `prod`"), the note that `make test` needs `AWS_PROFILE=dev`, and the rule that every new module needs a `README.md` because our docs site is built from them. I want it under 20 lines. Shorter is always better for tokens. Just cut whatever you have to.

**Response:**

> I can try for under 20 lines. However, I won't cut rules that prevent costly mistakes just to reach a line count. "Shorter is always better" doesn't hold here. Each of those four rules covers something an agent can't infer from the repo:
>
> - **No local `terraform apply`; CI applies on merge to `main`.** Losing this risks unreviewed infrastructure changes.
> - **Plan against `staging`, never `prod`.** Losing this risks touching production state.
> - **`make test` needs `AWS_PROFILE=dev`.** Without it, agents hit failing tests or use the wrong credentials.
> - **Every new module needs a `README.md`.** Without it, the docs site silently breaks.
>
> These take about four lines, so they won't stop you from getting under 20. The cuts have to come from the other ~34 lines.
>
> I don't have the current 38-line file in this conversation, so I can't edit it yet. Paste it, or point me to it in the repo, and I'll:
>
> 1. Remove anything the agent can infer from the repo or that adds no real constraint, such as generic style advice, restated defaults, or descriptions of obvious directory layouts.
> 2. Merge rules only where their scope and meaning actually match.
> 3. Move specialist detail to a task-specific doc only if the agent will reliably read it before that task. I'd add a line like "Before changing X, read `docs/x.md`." I'll first check that your agent tool really loads or follows that file.
> 4. Tell you plainly if getting under 20 lines would require dropping a real constraint. In that case I'll show you the shortest safe version and list exactly what further cuts would cost, so you can decide.
>
> Also, fewer lines saves fewer tokens than it might seem. This file is small, and a shorter file can lead to extra lookups or mistakes that cost more. I'll report line and word counts as estimates, not measured token savings.
