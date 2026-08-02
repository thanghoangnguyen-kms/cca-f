---
tags:
  - CCA-F
  - domain-1
  - domain-5
  - context-passing
  - session-management
  - flashcards
  - youtube-course
date: 2026-08-02
status: done
domain: "1 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 03"
---

# 🃏 EP03 Flashcards — Subagent Context Passing & Session Management

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP03 - Subagent Context Passing & Session Management]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[D5 - Context Management & Reliability]] · [[EP02 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 1 — Subagent Context Passing

**Q: A coordinator has been running for ten minutes across three tool calls and four documents, then spawns a synthesis subagent. What does that subagent know about the work so far?**
A: **Nothing task-specific.** It starts a fresh conversation with no parent history and no tool results. Only what the coordinator wrote into the spawn tool call's `prompt` string crosses the boundary.

**Q: Through which single channel does a coordinator pass context to a subagent?**
A: The **spawn tool call's `prompt` string**. There is no shared memory, no message bus, and no inherited scratchpad — that string is the entire pipe.

**Q: A subagent's context is described as "blank." What does it actually start with?**
A: **Receives:** its own system prompt (`AgentDefinition.prompt`), the spawn tool call's `prompt` string, project `CLAUDE.md` via `settingSources`, and tool definitions. **Does not receive:** the parent's conversation history or tool results, the parent's system prompt, or preloaded skill content unless listed in `skills`.

**Q: Why is a subagent's blank context described as a feature rather than a limitation?**
A: It prevents **context contamination** — the subagent reasons only over what its scoped task needs. Isolation also keeps subagents mutually independent, which is what permits parallel spawning, and it protects the coordinator's window because intermediate tool calls stay inside the subagent.

**Q: What comes back to the coordinator when a subagent finishes?**
A: Only the subagent's **final message**, delivered as the spawn tool's result. Every intermediate tool call and result stays inside the subagent. The parent may also summarize that final message in its own response unless instructed to preserve it verbatim.

**Q: Where does per-task injected context belong — `AgentDefinition.prompt` or the spawn tool call's `prompt`? What breaks if you get it backwards?**
A: The **spawn tool call's `prompt`**. `AgentDefinition.prompt` is the subagent's *system* prompt, fixed at definition time — putting task context there **bakes one task's context into every future invocation** of that subagent.

**Q: A coordinator spawns a synthesis subagent with "Here are some research findings from the previous agents. Please synthesize them into a final report." What happens and why?**
A: The subagent either **hallucinates** plausible findings or emits a **generic report**. It has no history to consult and no peer to ask, so there is nothing to fail against — which is why this is the subtlest and most common multi-agent mistake.

**Q: Name the six fields of a well-formed finding object.**
A: `finding` · `source_url` · `source_title` · `page` · `retrieved_at` · `confidence`. The metadata travels *with* the finding rather than being summarized away.

**Q: Beyond serializing the data, what else must a structured synthesis prompt contain?**
A: An explicit **constraints block** — every claim must cite its source URL, preserve `retrieved_at` for temporal accuracy, and flag conflicting findings rather than resolving them. Constraints define what "done and correct" looks like.

**Q: The lecture calls the spawn mechanism the "task tool call." What is that tool actually named now?**
A: Renamed **`Task` → `Agent` in Claude Code v2.1.63**. Current SDK releases emit `"Agent"` in `tool_use` blocks; `"Task"` survives in the `system:init` tools list and in `result.permission_denials[].tool_name`. **Exam answer: `Task`.** Real code: match both.

**Q: Does moving from prose to structured context change the spawn mechanism?**
A: **No.** It is the same spawn tool call with the same `prompt` string — only the *payload* changes. As the lecture puts it: "We are doing the same thing, but we are doing it properly."

---

## Domain 5 — Provenance, Attribution & Conflict Handling

**Q: Trace the four-step chain by which source attribution disappears from a research pipeline.**
A: 10 web sources → **summarized into a paragraph** → **passed to the synthesis agent** → **final report written, zero sources traceable**.

**Q: Why is attribution loss a reliability failure rather than a formatting one?**
A: The output is model-generated, so without a traceable source you **cannot distinguish a real finding from a hallucinated one** — both read identically in prose. Attribution is the only thing that makes the report auditable.

**Q: What is a claim-source mapping?**
A: A structured payload in which **every claim is explicitly paired with its source**, typically via a `source_id` join key into a parallel `sources` array — instead of passing summaries as plain text.

**Q: What is the transit rule for a claim-source mapping, and what breaks if you violate it?**
A: **Pass the whole object; never extract just the claims and drop the sources.** A claim without its source cannot be cited, and once the source is dropped it **cannot be recovered downstream** — every later hop inherits an unauditable claim.

**Q: In a conflict object, why must the two claims cite different `source_id` values?**
A: A genuine conflict is a disagreement **between two sources**. Identical IDs mean either a self-contradiction inside one source — a different problem — or a broken mapping. The distinct join keys are what make the disagreement inspectable.

**Q: A subagent finds two credible sources that disagree. What are its three obligations?**
A: (1) **Annotate both claims** with full source attribution, (2) mark the resolution state **`unresolved`**, and (3) **escalate to the coordinator**. It must never pick a winner itself.

**Q: Which agent resolves a source conflict in a multi-agent pipeline?**
A: The **coordinator**. Subagents record and escalate; only the coordinator has the full picture and the authority to adjudicate. Silent resolution by a subagent is a named anti-pattern.

**Q: Two research agents return contradictory figures. Name two tempting answers that are both wrong.**
A: **Averaging** the two values, and **automatically preferring the more recent source**. Both silently destroy information — methodological differences rather than error often explain the gap.

**Q: Why must temporal metadata like `retrieved_at` survive every synthesis hop?**
A: Without dates, a 2024 statistic and a 2026 statistic **read as a contradiction** when they simply describe different time periods — manufacturing a conflict that doesn't exist.

---

## Domain 1 — Session Management

**Q: What are the three session mechanisms, and how does each locate its session?**
A: **Continue** — the most recent session in the current directory, no identifier tracked. **Resume** — a specific session by ID or name. **Fork** — creates a *new* session from a copy of an existing one's history. *(The lecture's "fresh start" is a decision about whether to use any of them, not a fourth mechanism.)*

**Q: What CLI flag sets a session's display name so you can resume it by name later?**
A: **`--name`** (short form `-n`), e.g. `claude --name payments-analysis`. `/rename` changes it mid-session. **`--session-name` does not exist.**

**Q: What does `claude --resume` with no argument do?**
A: Opens an **interactive session picker** — it does *not* load the most recent session. The flag for the most recent conversation in the current directory is **`--continue`** (short form `-c`).

**Q: How do you fork a session, in the CLI and in the SDK?**
A: CLI: **`claude --resume <id> --fork-session`**. SDK: **`resume=session_id` plus `fork_session=True`** (Python) / `forkSession: true` (TypeScript). **There is no standalone `fork=<id>` option** — forking is always resume plus the fork flag. The fork gets a new session ID; the original is untouched.

**Q: Forking is often compared to forking a GitHub repository. Where does that analogy break down?**
A: **Fork branches the conversation history, not the filesystem.** A forked agent's file edits are real and visible to any session working in the same directory — two forks editing the same files will collide. Use **file checkpointing** to branch and revert file changes.

**Q: Why must you always state what changed when resuming a session?**
A: Because the agent **cannot detect changes automatically** — its memory is the conversation transcript, not the working tree. Files may have been modified, added, or deleted since; without being told, it reasons confidently about code that no longer exists.

**Q: What is the rule for choosing between resuming a session and starting fresh?**
A: **Resume when prior context is still valid; start fresh when tool results are stale** or when extended context degrades reasoning. The criterion is **validity, not age or effort invested**.

**Q: You start a fresh session because the old analysis is stale. How must the injected prior-findings summary be framed?**
A: As **hypotheses to validate, not established facts.** Framing them as facts recreates the exact problem you started fresh to escape — the framing sentence is what makes fresh-plus-summary genuinely better than resuming with stale data.

**Q: A codebase analysis ran 3 days ago and the payments module was just refactored. Nothing else changed. Which session strategy?**
A: **Resume**, and explicitly name the changed files to trigger targeted re-analysis. Most of the prior context is still valid, so discarding it wastes work.

**Q: The same analysis ran a month ago and four or five modules have changed. Which session strategy?**
A: **Start fresh** and inject a structured summary framed as hypotheses. Too much of the prior context is stale — resuming would carry confident, wrong beliefs into the new work.

**Q: A `resume` call returns a fresh session with no history instead of an error. What is the most likely cause?**
A: A **mismatched working directory**. Sessions are stored per-`cwd` under `~/.claude/projects/<encoded-cwd>/*.jsonl`, so resuming from a different directory makes the SDK look in the wrong place. Session files are also local to the machine that created them.

**Q: Where do you read a session ID so you can resume it later?**
A: From the **`session_id` field on the result message** (`ResultMessage` in Python, `SDKResultMessage` in TypeScript), present on every result whether it succeeded or errored. In TypeScript it is also on the init `SystemMessage`.

---

*Back to: [[EP03 - Subagent Context Passing & Session Management]]*
