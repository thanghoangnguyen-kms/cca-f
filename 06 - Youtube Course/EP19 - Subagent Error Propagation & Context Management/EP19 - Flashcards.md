---
tags:
  - CCA-F
  - domain-5
  - error-propagation
  - context-management
  - reliability
  - flashcards
  - youtube-course
date: 2026-08-05
status: done
domain: "5 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 19"
---

# 🃏 EP19 Flashcards — Subagent Error Propagation & Context Management

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP19 - Subagent Error Propagation & Context Management]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D5 - Context Management & Reliability]] · [[D2 - Tool Design & MCP Integration]] · [[EP07 - Agent Error Handling & tool_choice]] · [[EP18 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 5 — Silent Failure and the Two Levels

**Q: An overnight multi-agent research job produces no report, no output, and no error. Why is this harder to debug than a crash?**
A: A crash yields a stack trace and a line number; **silence yields nothing**. And it was produced by a system designed to be quiet about failure, so the fix is architectural — structured propagation — not better logging.

**Q: Tool → agent and subagent → coordinator error propagation are separate exam objectives. Which domain owns each, and what signals failure in each?**
A: **Tool → agent is Domain 2** (tool design), signalled by the `tool_result` block's `is_error` flag. **Subagent → coordinator is Domain 5** (context and reliability), signalled by a structured payload the subagent returns as its final message.

**Q: A tool fails inside a subagent. Trace the propagation hierarchy to the coordinator.**
A: The tool signals failure to the **subagent** (`is_error`). The subagent **retries locally or tries other tools**. Only when the subagent has exhausted its own options does it **propagate a structured error to the coordinator**. Escalation is level by level, never a jump.

**Q: Which field name in an error payload is API-defined, and which are your own convention?**
A: **`is_error`** is a defined Messages API field on a `tool_result` content block. Everything in a **subagent → coordinator** payload — `error_category`, `retryable`, `partial_results` — is **your own JSON schema**; the vault's version uses `status` and `failure_type` instead.

---

## Domain 5 — Structured Error Context

**Q: A subagent returns `{"error": "operation failed"}`. What two options does that leave the coordinator, and why are both bad?**
A: **Silently kill the process** (producing the dead-silence failure) or **guess**. Both are bad because the payload gives no basis for choosing between retry, alternate source, or termination — the vagueness removes the coordinator's ability to act at all.

**Q: Name the four ingredients of a recovery-ready error response.**
A: **`error_category`** (what kind of failure) · **`retryable`** (can retrying help) · **`attempted_query`** (what it was doing when it broke) · **`partial_results` + `alternatives`** (what was salvaged, and what else to try).

**Q: `retryable` is called the single most important Boolean in the payload. What makes it decidable in the first place?**
A: **`error_category`.** Without knowing whether the failure was a timeout, a permission error, or a genuine no-results, `retryable` is a guess. The category is what carries the information; the Boolean just states the conclusion.

**Q: What does `attempted_query` buy the coordinator that a category alone does not?**
A: The ability to **understand the specific gap and re-issue a corrected version**, rather than reconstructing what the subagent was trying to do. It answers "what was it working on when it broke."

**Q: Beyond the lecture's four ingredients, which two fields does the vault add, and what failure do they prevent?**
A: **`retry_attempted`** and **`retry_count`**. Without them the coordinator cannot distinguish a subagent that already tried three times from one that tried zero, so it may **silently duplicate the retry budget** and repeat work.

**Q: Besides retrying or switching source, what third recovery option does a structured error unlock — the one the lecture never mentions?**
A: **Synthesize with a gap annotation** — ship the report with `Section B (gap — data unavailable): search agent timed out` rather than returning nothing. It is the concrete escape from the dead-silence scenario, and it requires partial results to be worth anything.

---

## Domain 5 — Partial Results and Alternatives

**Q: A subagent tasked with five documents fails after retrieving three. Why is discarding those three a design error?**
A: They are **real, valid, and expensively obtained**. Keeping them lets the coordinator **narrow the task to the two missing documents** instead of restarting from zero. A partial success is not a failure.

**Q: In what order does the coordinator consult `retryable` and `alternatives`, and why does the order matter?**
A: **`retryable` first** — if true, retry the same approach. **`alternatives` only after the retry has also failed**, at which point it switches source. Reading alternatives first would abandon a recoverable approach prematurely; without them, the coordinator's options end when the retry budget does.

**Q: Why does the instructor call `partial_results` the field the exam will test?**
A: Because it is the one most people omit — a failed task *feels* like it produced nothing. It is the difference between a coordinator that narrows the remaining work and one that starts over or gives up.

---

## Domain 5 — Access Failure vs Valid Empty Result

**Q: Two subagents both report "nothing found." What distinguishes the case worth retrying from the case that is not?**
A: Whether the **retrieval mechanism worked**. Mechanism broke (network timeout, auth error, service down) = **access failure**, `retryable: true`. Query ran and matched nothing = **valid empty result**, `retryable: false`.

**Q: In the librarian analogy, which reply is an access failure and which is a valid empty result?**
A: *"The database is down, come back later"* = **access failure** — the mechanism broke, so asking again may work. *"The book doesn't exist"* with a working database = **valid empty result** — asking again can never change the answer.

**Q: Give the `error_category` → `retryable` mapping for a timeout and for a genuine no-match.**
A: `error_category: "timeout"` → `retryable: true`. `error_category: "no_results"` → `retryable: false`.

**Q: Conflating access failure with valid empty result costs you in two directions. Name both.**
A: (1) Treating an empty result as retryable **wastes the retry budget on a query that can never succeed**. (2) Treating an access failure as empty **gives up on a query that would have worked on a second attempt**. Because the budget is shared, one misclassification starves the other case.

---

## Domain 5 — The Two Anti-Patterns

**Q: A subagent catches an exception and returns `{"status": "ok", "results": []}`. Name the anti-pattern and the specific reason it is dangerous.**
A: **Silent suppression.** It is dangerous because the failure **disguises itself as a valid empty result** — the coordinator sees expected emptiness and does nothing, producing a **confidently wrong, complete-looking report** with a gap nobody knows about.

**Q: Why is silent suppression worse than immediate termination?**
A: Immediate termination **fails loudly** — you know something went wrong. Silent suppression produces a plausible report built on missing data, so *"you don't even know that you did something wrong."*

**Q: A subagent hits an exception and aborts the pipeline. Name the anti-pattern and its two distinct harms.**
A: **Immediate termination.** (1) **Every other subagent's completed work is discarded.** (2) **The coordinator never gets to make the decision** — the subagent has usurped authority that is not its to hold.

**Q: Only the coordinator may terminate a pipeline. How is that rule enforced deterministically rather than requested in a prompt?**
A: With a **hook**. An Agent SDK hook returns a top-level **`continue`** field (`continue_` in Python) that determines whether the agent keeps running — so termination authority lives in code the model cannot override.

**Q: The vault lists three error anti-patterns where the lecture lists two. What is the third?**
A: **Returning a generic `"operation failed"`.** The lecture treats it as its own topic (vague versus structured errors) rather than as an anti-pattern — same content, different partitioning.

---

## Domain 5 — Local Recovery

**Q: Should a subagent escalate the moment something fails? State the rule and the analogy.**
A: **No — it should attempt local recovery first**, *"like a junior developer trying to fix the error before going to the architect."* It escalates only once local options are exhausted.

**Q: Which error class should a subagent retry locally, and which should it escalate immediately?**
A: **Transient** errors — timeouts, intermittent network — retry locally a limited number of times. **Non-transient** errors — permission, invalid input — **escalate immediately**; retrying them is pure waste. The lecture covers the first branch; the vault adds the second.

**Q: A subagent's tool returns the wrong data format, and separately its source goes down. What is the local action in each case?**
A: **Wrong format** → adjust the query and re-issue. **Source down** → try the backup source. Escalate only if all local options are exhausted.

---

## Domain 5 — Context Degradation

**Q: Name the three symptoms of context degradation in a long Claude Code session.**
A: (1) **Early findings pushed out** of the window. (2) **Already-read files re-read**, spending tokens unnecessarily. (3) **Cross-file connections broken**, because interdependent files are no longer all in context at once.

**Q: Which context-degradation symptom is most damaging for architecture work, and why?**
A: **Broken cross-file connections.** Individual facts survive but the **relationships between them do not** — so Claude can still describe each file yet lose how they depend on one another. It is the lost-in-the-middle effect applied to relationships rather than facts.

**Q: Why is "use a bigger model at max effort" not a fix for context degradation?**
A: Because the window still fills — *"you will run out of context in 15 minutes."* The persistence techniques let you complete the work on a **cheaper model** rather than on an expensive model briefly.

---

## Domain 5 — Scratchpad Files

**Q: What is a scratchpad file, and what is the key difference from conversation history?**
A: A plain `.md` or `.json` file where Claude Code records key findings — services examined, files analyzed, open questions. The difference: it lives **on disk, not in the transcript**, so it survives the context window filling and can be re-read to recover findings without re-exploring.

**Q: What is "the catch" with scratchpad files, and what are the three ways to trigger one?**
A: **Claude Code will not create one unprompted.** Trigger it via (1) **the prompt** in an interactive session, (2) **`CLAUDE.md`**, which loads at the start of every session, or (3) **agent code**, for a programmatic loop.

**Q: You need a scratchpad in an automated CI/CD run. Which trigger must you use, and name its second advantage.**
A: **`CLAUDE.md`** — there is no interactive turn in which to prompt. Second advantage: project-root `CLAUDE.md` is **re-read from disk and re-injected after compaction**, so the instruction survives a `/compact` pass that a prompt-only instruction would not.

**Q: The lecture says Claude Code never persists findings on its own. What is now true instead?**
A: **Auto memory is on by default** — Claude writes its own notes across sessions to `~/.claude/projects/<project>/memory/`, and the first **200 lines or 25KB** of `MEMORY.md` loads every session. The lecture is right that Claude won't create a file named `scratchpad.md` unprompted, but wrong that nothing is persisted automatically.

**Q: Auto memory exists and is on by default. Why does the scratchpad technique still fill a real gap?**
A: Auto memory holds **durable learnings and patterns** Claude judges reusable, and is machine-local per repository. A scratchpad is a **working log of one exploration** — which auto memory is not designed to be. Also, `--bare` disables auto memory in CI.

**Q: Where does auto memory live, and why does that cut against "keep everything in the `.claude` folder"?**
A: At `~/.claude/projects/<project>/memory/` — **outside the repository and machine-local**, so it is never committed. The lecture's fixed-predictable-path advice is a sound convention, not a documented requirement, and auto memory deliberately does not follow it.

---

## Domain 5 — Compaction

**Q: What does `/compact` do, and is it available in a non-interactive `claude -p` run?**
A: It **replaces conversation history with a summary** to free context. It is **terminal-only** and not available in `-p` mode — unlike user-invoked skills and custom commands, which do expand in the prompt string.

**Q: "You cannot compact context in a CI/CD pipeline." True or false, and why?**
A: **False as stated.** The `/compact` *command* cannot be invoked there, but **automatic compaction still runs** in non-interactive sessions, and its threshold is configurable with the **`CLAUDE_CODE_AUTO_COMPACT_WINDOW`** environment variable — which a pipeline can set.

**Q: The lecture says auto-compaction fires at "90% of the context window." What is the actual mechanism?**
A: A **configurable token count**, not a fixed percentage — set with `/autocompact <size>` (100K–1M) or `CLAUDE_CODE_AUTO_COMPACT_WINDOW`. Defaults are model-dependent: a 1M-window Sonnet 5 session compacts near **967K tokens**; models on a 200K window compact at that boundary.

**Q: Which instruction sources survive a `/compact` pass, and which do not?**
A: **Project-root `CLAUDE.md` survives** — it is re-read from disk and re-injected. **Instructions given only in conversation do not.** Invoked skills are re-injected truncated to roughly 5,000 tokens each, oldest dropped first, and the `PreCompact` hook can customize what is preserved.

---

## Domain 5 — Manifest and Recovery

**Q: What is a session manifest, and what analogy does the instructor use?**
A: A JSON checkpoint written **before every major step** to a fixed, predictable path, recording current state so a crashed run resumes from the checkpoint. The analogy is a **Git commit** — checkpoint before risky or expensive work, roll back to the last one.

**Q: Distinguish what a manifest records from what a scratchpad records.**
A: The **manifest records position** — which steps or subagents are completed and which are pending. The **scratchpad records findings** — files read, discoveries, open questions. Position versus findings.

**Q: The lecture says a closed terminal means restarting from absolute zero. Why is that wrong?**
A: **Sessions are saved continuously to disk as you work.** `claude --continue` resumes the most recent session in the directory and `--resume <name-or-id>` targets a specific one, restoring the full history including tool calls and results. `/rewind` additionally checkpoints code and conversation.

**Q: Given that sessions already persist, what does a manifest genuinely provide that `--resume` does not?**
A: (1) **Multi-agent workflow state** — which subagents finished and which are pending is your orchestration state, not conversation history, so no transcript exposes it as a resumable checkpoint. (2) **Portability** — a plain file any process can read, whereas sessions are directory-scoped, machine-local, and `claude -p` sessions are resumable only by explicit ID.

**Q: You resume a session that has been idle over an hour and exceeds 100,000 tokens. What choice are you offered, and what is the trade-off?**
A: **Resume from summary** (runs `/compact` immediately — cheaper per later request, but whatever the summary omits is gone) versus **resume full as-is** (every detail, with per-request cost scaling with conversation size). It is the compaction trade-off surfacing as a recovery decision.

---

## Domain 5 — The Three Layers

**Q: Name the three layers of a silence-proof agentic system and the mechanism in each.**
A: **Error layer** — structured error propagation (category, `retryable`, attempted query, partial results, alternatives). **Context layer** — scratchpad files plus compaction. **Recovery layer** — the session manifest.

**Q: What single principle unifies all three layers?**
A: Each replaces an **implicit assumption with an explicit artifact**: "it probably worked" becomes a payload, "it probably remembers" becomes a file, "it probably survived" becomes a checkpoint. Silence is what you get when the success and failure paths are indistinguishable.

**Q: State the authority rule that runs through the whole episode.**
A: **Whoever discovers a problem is rarely entitled to resolve it.** Tools report to subagents; subagents retry what they can and then report to coordinators; only the coordinator terminates.

---

*Back to: [[EP19 - Subagent Error Propagation & Context Management]]*
