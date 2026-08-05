---
tags:
  - CCA-F
  - domain-3
  - ci-cd
  - structured-output
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "3 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 13"
---

# 🃏 EP13 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP13 — Claude Code CI/CD Pipelines** (Domain 3, task statement 3.6). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry a correction or expansion verified against official Anthropic documentation. The lecture passes `--json-schema` a file path (it takes inline JSON), and stops at `-p` without covering tool permissions, turn limits, or `--bare` — those cards give the verified behaviour. Its fabricated-flag list and its batch-API rule are both **correct**.

**Related:** [[EP13 - Claude Code CI-CD Pipelines]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]] · [[EP12 - Flashcards]] · [[EP10 - CLAUDE.md Hierarchy & Config Rules]]

---

## Domain 3 — The Hang and the -p Flag

**Q: A `claude` command reviews tests fine locally and times out in GitHub Actions. What is the cause?**
A: Claude Code is conversational by default — on a complex task it asks a clarifying question, and in CI nobody answers, so the job sits until its timeout fires. The integration is fine; the `-p` flag is missing.

**Q: Which flag makes a Claude Code run non-interactive, and do the short and long forms differ?**
A: `-p` or `--print` — identical in behaviour. Claude takes the prompt, completes, prints, and exits without waiting for input.

**Q: Name two properties of `claude -p` that let it compose like an ordinary CLI tool. (docs)**
A: It **reads stdin** (`git diff main | claude -p "..."`) and it sets a **normal exit code** — 0 on success, non-zero on failure — so a script can pipe into it and branch on the status.

**Q: Why is piping data into `claude -p` often safer than letting Claude fetch it? (docs)**
A: It shrinks the permission surface. Data arriving on stdin needs no tool grant, whereas having Claude run `git diff` itself requires a Bash permission that could be exercised more broadly.

**Q: Which of `--headless`, `--ci`, `--batch`, `-p`, `--non-interactive` actually exist? (docs)**
A: **Only `-p`** (and `--print`). The other four are fabricated distractors — they borrow credibility from flags common in other CLIs.

**Q: An exam option says "enable headless mode with `--headless`." What exactly is wrong with it? (docs)**
A: The flag name. "Headless" is legitimate vocabulary for running Claude Code programmatically, but there is no `--headless` flag — the option is wrong on the mechanism, not the concept.

---

## Domain 3 / Domain 4 — Machine-Readable Output

**Q: Why can a CI pipeline feed Claude raw prose but not consume it?**
A: Claude is an LLM and parses a raw log fine on the way in. The next CI step is a script and cannot branch on a sentence. Input is forgiving; output must be structured.

**Q: Name the two flags that produce schema-conforming output and what each contributes.**
A: `--output-format json` returns a JSON envelope with the result and session metadata; `--json-schema` constrains the payload to a specific JSON Schema. Together they give strictly-shaped, parseable output.

**Q: What are the three accepted values of `--output-format`? (docs)**
A: `text` (the default), `json`, and `stream-json`.

**Q: Your schema is saved in `review-schema.json`. Is `--json-schema review-schema.json` correct? (docs)**
A: **No** — the flag takes an **inline JSON string**, not a file path. Interpolate the file yourself: `--json-schema "$(cat review-schema.json)"`.

**Q: You pass `--json-schema` a bare filename. What happens, and why did it used to be worse? (docs)**
A: From Claude Code v2.1.205 the command errors with `--json-schema is not a valid JSON Schema`. Before that it **silently returned unstructured prose** — a green pipeline whose gate step had nothing parseable to test.

**Q: You run with `--json-schema` and `jq -r '.result'`, but get prose instead of your schema. Why? (docs)**
A: `result` holds the model's prose; the schema-conforming payload is in a separate **`structured_output`** field. Parse `.structured_output`.

**Q: Does `--json-schema` enforce a `"format": "email"` constraint? (docs)**
A: **No.** Claude Code accepts `format` but treats it as an annotation only, with no client-side validation. (Before v2.1.205 a schema containing `format` was rejected outright.)

---

## Domain 3 — What -p Does Not Solve

**Q: You added `-p` and the job now aborts instead of hanging. What is gating it? (docs)**
A: Tool permissions. `-p` removes the conversational wait, not the permission gate — Claude still needs approval for most Bash commands and file edits, and with nobody to approve, the run aborts.

**Q: Name two ways to grant tool access to a non-interactive run. (docs)**
A: `--allowedTools "Bash(git diff *),Read"` pre-approves specific tools; `--permission-mode dontAsk` is the documented choice for locked-down CI — anything outside your allow rules and the read-only command set is auto-denied, so the session never waits.

**Q: Why does the space matter in `--allowedTools "Bash(git diff *)"`? (docs)**
A: The trailing ` *` is prefix matching. Without the space, `Bash(git diff*)` would also match commands like `git diff-index`, widening the grant beyond what you intended.

**Q: A CI agent occasionally loops until the job's wall-clock timeout. Which flag bounds it, and why isn't `-p` enough? (docs)**
A: `--max-turns` caps agentic turns and exits with an error at the limit. `-p` only removes the wait for human input — it places no bound on how many turns Claude takes on its own.

---

## Domain 3 / Domain 1 — Session Isolation

**Q: Claude wrote a module. Why shouldn't the same session write its tests?**
A: It still holds the reasoning it used to write the code, so it rationalises instead of critiquing — typically producing happy-path tests and missing negative paths. Code and tests would share an author, and therefore a blind spot.

**Q: What is the human analogy for session isolation, and what does it explain?**
A: Teams separate developers from testers not because developers can't test, but because an author unconsciously tests along the path they built. A tester brings a fresh perspective — the same reason a fresh Claude session reviews better.

**Q: Do you need a flag to get a fresh session for each CI stage? (docs)**
A: **No** — every `claude -p` invocation starts a new session by default. `--continue` or `--resume <id>` are how you opt *out* of isolation.

**Q: What is the realistic session-isolation mistake in a CI pipeline, given that isolation is the default? (docs)**
A: Reaching for `--continue` to "give Claude context" in a review stage — which reintroduces exactly the author bias isolation prevents. Pass a summary artifact instead of resuming the session.

**Q: When you genuinely do want to continue a specific conversation across two `-p` calls, how do you capture the handle? (docs)**
A: `session_id=$(claude -p "..." --output-format json | jq -r '.session_id')`, then `claude -p "..." --resume "$session_id"`. Both commands must run from the same directory — ID lookup is scoped to the project directory and its git worktrees.

---

## Domain 3 — CLAUDE.md in CI

**Q: If every CI stage is a fresh session with no memory, how do project standards reach them?**
A: `CLAUDE.md` loads into each new session, carrying code standards, review criteria, security policies, and testing requirements.

**Q: Distinguish the two kinds of memory at play when you isolate CI sessions.**
A: Isolation removes **episodic** memory — what happened in the previous stage, including the author's reasoning. `CLAUDE.md` preserves **standing** memory — how this project does things. You want the first gone and the second kept.

**Q: A CI runner uses `--bare`. What silently stops working? (docs)**
A: `--bare` skips auto-discovery of hooks, skills, plugins, MCP servers, auto memory, **and `CLAUDE.md`** — so the standards strategy quietly stops applying, in the exact mode the docs recommend for CI.

**Q: Why does `--bare` exist, and what is the fix when you still need your standards? (docs)**
A: Reproducibility — a teammate's `~/.claude` hook or a project `.mcp.json` server won't run, so the result is the same on every machine. Load standards explicitly with `--append-system-prompt-file ./ci-standards.md`.

**Q: What else changes about authentication under `--bare`? (docs)**
A: Bare mode never reads OAuth credentials or the system keychain, so it needs `ANTHROPIC_API_KEY` (or an `apiKeyHelper` in `--settings`). Bedrock, Google Cloud, and Foundry read their own provider credentials as usual.

**Q: Which four testing conventions should `CLAUDE.md` document so generated tests conform?**
A: **Naming patterns, structure, mocking rules, and coverage targets.** Every automated generation step then inherits them instead of Claude inventing conventions per run.

**Q: Why put test conventions in `CLAUDE.md` rather than in the prompt of each generation step?**
A: A pipeline has many prompts and one config. Standards written into prompts drift step by step; standards in `CLAUDE.md` apply to every step that loads it.

---

## Domain 3 — Deduplication

**Q: Claude reviewed a PR. You push two more commits and the next run repeats every comment. Why?**
A: The new session has no memory of the previous review, so it re-reviews the whole PR and re-reports the same findings.

**Q: Describe the deduplication mechanism and the command that drives it.**
A: Add a step before the Claude step that fetches existing PR comments — `gh pr view "$PR" --json comments` — into a file, then pass those prior findings into the fresh session so it reports only new issues.

**Q: Isolation and deduplication seem to pull in opposite directions. Reconcile them.**
A: Isolation is what makes review critical; duplication is what isolation costs. You don't reuse the session — you pass the prior **findings** into a fresh one. Replay the conclusions, never the reasoning.

**Q: What exam phrasing signals the deduplication answer?**
A: Wording about Claude "doing double work" or re-reporting findings on a PR. Look for the option that fetches prior comments into a **fresh** session — not one that reuses or resumes a session.

---

## Domain 3 — Production Pipeline

**Q: List the six stages of the production CI pipeline architecture in order.**
A: `CLAUDE.md` for project context → fetch prior findings → fresh session → `-p` for non-interactive execution → `--output-format json` with `--json-schema` → act on the result (`jq`, `exit 1` on critical to block the merge).

**Q: How does the merge gate actually block a bad PR, and why did the schema matter?**
A: Parse the findings with `jq`, and `exit 1` when any severity is critical — a non-zero exit fails the step and stops the workflow. The schema is what makes this a field test (`severity == "critical"`) rather than a fragile grep over prose.

**Q: What is `anthropics/claude-code-action@v1` and how does it relate to a hand-rolled `claude -p` step? (docs)**
A: The official GitHub Action. It takes a `prompt` input and passes CLI arguments through via `claude_args`, so everything about `-p`, schemas, and permissions still applies — it adds GitHub App auth and `@claude` mention triggering.

**Q: What is the security rule for the API key in a workflow file? (docs)**
A: Never hardcode it — store it as a repository secret and reference `${{ secrets.ANTHROPIC_API_KEY }}`, granting the workflow only the permissions it needs.

---

## Domain 4 — Batch API in CI

**Q: Which CI use case is inappropriate for the Message Batches API, and why?**
A: **Blocking pre-merge checks.** Batch guarantees only a 24-hour processing window with no latency SLA, so nothing that gates a merge can depend on it.

**Q: State the general rule for choosing batch versus synchronous in a pipeline.**
A: Batch for latency-tolerant work — overnight reports, weekly audits, nightly test generation. Synchronous for anything a human or a pipeline stage is waiting on.

**Q: What is the exam's discriminator between a batch-suitable and a batch-unsuitable CI job?**
A: Whether something **blocks** on the answer. "Nightly" and "weekly" point to batch; "pre-merge", "gate", and "on push" rule it out — regardless of how much cheaper batch is.

**Q: Most batches finish within an hour. Why size a deadline against 24 hours instead?**
A: Because one hour is typical behaviour, not a commitment. The only guaranteed figure is the 24-hour window, so any deadline you must meet has to fit inside that ceiling.

---

*Back to: [[EP13 - Claude Code CI-CD Pipelines]]*
