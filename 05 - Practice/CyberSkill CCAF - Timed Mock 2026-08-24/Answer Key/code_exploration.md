---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - code-exploration
date: 2026-08-24
status: done
---

# Timed Mock 2026-08-24 — `code_exploration` Answer Key

**16 answers** — Q4, 9, 11, 22, 28, 29, 37, 39, 40, 43, 45, 52, 55, 56, 57, 58. Scored **15/16 (94%)** — the strongest domain of this sitting by a wide margin.

Question numbers are this sitting's own and interleave with the other domains; they match [../Questions.md](../Questions.md). Back to [../README.md](../README.md).

> [!NOTE] Every answer here is grader-authoritative
> These are the site's own `correct_key` values as rendered on the review page. Where an item was answered correctly, the correct answer *is* the recorded selection. Distractors were not recoverable — see [../README.md](../README.md) § Fidelity.

---

## ❌ Missed — the single miss in this domain

### Q40 — Context degraded after 25 minutes on the rendering subsystem

> Your agent has spent 25 minutes exploring a game engine's rendering subsystem—reading shader code, buffer management, and frame synchronization logic. An engineer now asks it to understand how the physics engine integrates with rendering for collision debug overlays. You notice recent responses reference "typical rendering patterns" rather than the specific `VulkanPipeline` and `FrameGraph` classes it discovered earlier.
>
> What's the most effective approach?

- **Correct: C.** Summarize key rendering findings, then spawn a sub-agent for physics exploration with that summary in its initial context.
- **You answered B.** "Continue in the current context with more targeted prompts referencing the specific classes by name."

**Why C wins.** The tell in the stem is that the agent has started saying *"typical rendering patterns"* instead of `VulkanPipeline` and `FrameGraph` — it has lost the specifics it discovered, which is context exhaustion, not a prompting problem. Naming the classes again only reintroduces the tokens by hand into a window that is already full, and the request is now a **new subject** (physics) that will need its own room. C does both jobs: it distils the rendering work into a durable summary, then gives physics a clean context seeded with exactly what carries over.

> [!WARNING] "Just prompt more specifically" is never the fix for context exhaustion
> Sharper prompts fix *ambiguity*. They cannot fix a window that no longer holds the findings. The symptom to recognise: the agent **degrades from specific to generic** about material it previously handled correctly. Once you see that, the answer is always summarize/compact, and — if the topic is shifting — hand off to a fresh context with the summary injected.
>
> You handled the identical pattern correctly in **Q11** (spawn subagents while the main agent coordinates) and **Q58** (scratchpad file). Same rule, third presentation.

**Takeaway.** Generic-where-it-was-specific = context exhausted → summarize, then fresh context for the new subject.

---

## ✅ Answered correctly

The option shown is the keyed correct answer.

### Q4 — Locating why an error message is thrown
**B.** Search for the exact error string, then open only the files and functions that produce or handle it.
*Takeaway: the literal string is the cheapest possible index into a large codebase. Search first, read narrowly.*

### Q9 — `Edit` can't find a unique `old_string`
**D.** Use `Read` to load the file, add the function at the appropriate location, then `Write` the updated file.
*Takeaway: when `old_string` genuinely cannot be made unique, `Read` → modify → `Write` is the correct fallback. 150 lines is cheap to rewrite wholesale.*

> [!WARNING] Don't generalise this into "`Edit` fails → rewrite the file"
> The documented **first** remedies are a longer `old_string` carrying enough surrounding context to pin one occurrence, or `replace_all: true`. A whole-file `Write` is the last resort. It wins *here* only because the stem establishes that no unique anchor exists — repetitive docstrings, names and structure throughout. On any item where a longer anchor would work, rewriting the file is the wrong answer.
> Source: <https://code.claude.com/docs/en/tools-reference>

### Q11 — 45-file payment module, accuracy degrading after 8 files
**B.** Spawn subagents to investigate specific questions (e.g., "find all test files for payment processing", "trace refund flow dependencies") while the main agent coordinates findings and preserves high-level understanding.
*Takeaway: parallel context isolation. Each subagent burns its own window; the coordinator keeps only the synthesis.*

### Q22 — Resuming a named session from yesterday
**D.** Use `--resume auth-deep-dive` to load that specific session by name.
*Takeaway: `--resume` targets one specific prior session, as against `--continue`, which picks up the most recent — wrong here, since three other codebases have been worked on since.*

> [!IMPORTANT] Verified against official docs, 2026-08-24
> `--resume` accepts a session **ID or name**, or opens an interactive picker with no argument. The CLI reference's own example is `claude --resume auth-refactor` — exactly this form. Sessions get names from `claude -n <name>` at startup, `/rename <name>` in session, or `Ctrl+R` in the picker; an auto-generated session title also works as a resume handle. `--continue` loads the most recent session in the current directory.
> Name resolution is scoped to the current repository and its worktrees (an ambiguous name opens the picker pre-filled); `--resume <session-id>` searches machine-wide.
> Sources: <https://code.claude.com/docs/en/cli-reference> · <https://code.claude.com/docs/en/sessions>

### Q28 — One function inside a multi-thousand-line file
**B.** Search within the file for the function and read only that region and its immediate dependencies.
*Takeaway: file-level granularity is not read-level granularity. Never load a whole file for one symbol.*

### Q29 — `README` claims the auth check lives in one module
**B.** Confirm in the current code where the auth check actually runs, then make the change there.
*Takeaway: documentation is a hypothesis. Verify against the code before editing on its authority.*

### Q37 — Exploring two refactoring approaches from one prior analysis
**D.** Use `fork_session` to create two branches from yesterday's analysis, exploring one approach in each fork.
*Takeaway: forking gives both branches the same accumulated context without either polluting the other. That is exactly the shape of "compare two approaches from a shared starting point".*

> [!IMPORTANT] `fork_session` is an option, not a tool — know the four spellings
> Python SDK `fork_session=True` · TypeScript `forkSession: true` (both paired with `resume=`) · CLI `claude --resume <id> --fork-session` · interactive `/branch`. There is no `fork_session` *tool*. One call yields **one** fork, so two branches means forking twice, or forking once and resuming the original.
> Source: <https://code.claude.com/docs/en/agent-sdk/sessions>

### Q39 — Valid session, but 3 of 12 files changed overnight
**C.** Resume the session and inform the agent which specific files changed for targeted re-analysis.
*Takeaway: don't discard an hour of context over a 25% delta. Resume and invalidate precisely what changed.*

### Q43 — Renaming a widely used function
**B.** Search the codebase for all references first, then plan the change across the call sites.
*Takeaway: establish the blast radius before the first edit.*

### Q45 — Subagent interrupted after 47 files; two functions renamed since
**D.** Resume the subagent from its previous transcript and inform it about the renamed functions.
*Takeaway: identical rule to **Q39** — resume + targeted invalidation. The cost of rebuilding 47 files of context dwarfs the cost of correcting two names.*

### Q52 — Caching logic across 15 files / ~8,000 lines
**B.** Analyze imports and class hierarchies to identify the base cache class, `Read` that file to understand the interface, then trace specific invalidation implementations.
*Takeaway: structure-first. Find the interface, then follow it to the implementations you actually need.*

### Q55 — New joiner, auth architecture, 800+ files
**C.** Use `Grep` to find authentication entry points, read those files, then follow imports and function calls to map the auth flow incrementally.
*Takeaway: grep-grounded incremental traversal. Entry points → imports → call graph.*

### Q56 — Intermittent 500s across 200+ unknown files
**B.** Have the agent dynamically generate investigation subtasks based on what it discovers at each step, adapting its exploration plan as new information about the error path emerges.
*Takeaway: when the components involved are unknown up front, decomposition must be **dynamic**. A fixed plan can only be written for a path you can already see.*

### Q57 — Orienting in an unfamiliar repository
**B.** Read the entry points and project structure, then search for the area the feature touches.
*Takeaway: structure before detail; search before bulk reading.*

### Q58 — Inconsistent answers in 30+ minute sessions
**A.** Have the agent maintain a scratchpad file that records key findings, referencing it for subsequent questions.
*Takeaway: an external scratchpad survives compaction. Findings written to disk are the durable memory of a long exploration. Compare **Q40**, where the summary is handed to a fresh subagent instead.*

---

## Domain pattern summary

| Rule | Items |
|---|---|
| Search first, read narrowly — never bulk-load | Q4, Q28, Q52, Q55, Q57 |
| Establish blast radius before editing | Q43, Q29 |
| Docs/README are hypotheses; the code is the fact | Q29 |
| Context exhaustion → summarize / scratchpad / subagent, **not** better prompts | Q40, Q11, Q58 |
| `--resume` (specific session) vs `--continue` (latest) | Q22 |
| `fork_session` to branch one context into parallel explorations | Q37 |
| Resume + targeted invalidation beats starting over | Q39, Q45 |
| Unknown component set → dynamic decomposition | Q56 |
| `old_string` not unique → `Read` → modify → `Write` | Q9 |

**Related:** [[Weak Areas Deep Dive]] · [[Answer Patterns Index]] · [[D3 - Claude Code Configuration & Workflows]] · [[D5 - Context Management & Reliability]]
