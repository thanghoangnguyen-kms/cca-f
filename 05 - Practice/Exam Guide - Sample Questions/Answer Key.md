---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - official
date: 2026-08-25
status: done
---

# Exam Guide — Sample Questions Answer Key

**12 answers, all officially authored.** Questions: [Questions.md](Questions.md) · index: [README.md](README.md) · blueprint: [[Official Exam Blueprint]]

> [!IMPORTANT] Highest-authority key in the vault
> The **Why X wins** lines and distractor rebuttals below are the exam author's own reasoning, condensed. Anything in a **Takeaway**, a `[!TIP]` or a `[!WARNING]` is the vault's commentary — useful, but not the author's. A handful of rebuttal cells add a vault gloss in parentheses; those are marked *(vault)*.
> Where a CyberSkill key disagrees with this one, **this one wins** — and that disagreement is worth logging in [[Weak Areas Deep Dive]].

---

## Scenario: Customer Support Resolution Agent

### Q1 — Skipped verification before refunds → **A**

**A.** Programmatic prerequisite blocking `lookup_order` and `process_refund` until `get_customer` returns a verified customer ID.

**Why A wins.** When a specific tool *sequence* is required for critical business logic, programmatic enforcement gives a **deterministic guarantee** that no prompt-based approach can. Identity verification before a financial operation is exactly that case.

| Distractor | Why it fails |
|---|---|
| **B** system prompt | Relies on probabilistic LLM compliance — insufficient when errors have **financial** consequences |
| **C** few-shot | Relies on probabilistic LLM compliance — the guide groups B and C together here. *(vault: examples shift likelihood, they don't guarantee)* |
| **D** routing classifier | Addresses tool **availability**, not tool **ordering** — which is the actual problem |

> [!IMPORTANT] The vault's golden rule — the exam author states half of it
> The guide's rationale says only that *"programmatic enforcement provides deterministic guarantees that prompt-based approaches cannot."* The vault's fuller couplet — *must always happen* → **code**, *should usually happen* → **prompt** — is a fair reading of task statement 1.4 (*"the difference between programmatic enforcement… and prompt-based guidance"*), but the second half is the vault's phrasing, not the author's. The 12% in the stem is the *"non-zero failure rate"* the guide names under 1.4.

**Takeaway.** B and C are the *same* answer wearing different clothes — both are prompt-layer. Spotting that collapses a 4-option question into a 3-option one. See [[D1 - Agentic Architecture & Orchestration]] §1.4 · [[00-golden-rules-cheatsheet]].

---

### Q2 — Misrouting between two thin tool descriptions → **B**

**B.** Expand each tool's description with input formats, example queries, edge cases, and boundaries versus similar tools.

**Why B wins.** **Tool descriptions are the primary mechanism LLMs use for tool selection.** Minimal descriptions leave the model without the context to differentiate. B addresses the root cause with a low-effort, high-leverage fix — and the question asks for a **first step**.

| Distractor | Why it fails |
|---|---|
| **A** few-shot | Adds token overhead without fixing the underlying issue |
| **C** routing layer | Over-engineered; bypasses the model's natural-language understanding |
| **D** consolidate tools | A *valid architectural choice*, but more effort than a "first step" warrants when the immediate problem is inadequate descriptions |

> [!TIP] Note what D is not
> The rationale calls D **valid but disproportionate** — not wrong in principle. When a stem says *"first step"* or *"most effective initial"*, effort-to-leverage is part of the answer. Change the stem to "long-term architecture" and D climbs.

**Takeaway.** Symptom = misrouting. Root cause = descriptions. See [[D2 - Tool Design & MCP Integration]] §2.1 · [[EP06 - Tool Descriptions & Tool Misrouting]].

---

### Q3 — Escalation calibrated backwards → **A**

**A.** Explicit escalation criteria in the system prompt with few-shot examples showing escalate-versus-resolve.

**Why A wins.** The root cause is **unclear decision boundaries**, and A is the proportionate first response before adding infrastructure.

| Distractor | Why it fails |
|---|---|
| **B** self-reported confidence | **LLM self-reported confidence is poorly calibrated** — and the agent is *already* incorrectly confident on the hard cases. The mechanism is broken exactly where it's needed |
| **C** trained classifier | Over-engineered — needs labeled data and ML infrastructure when prompt optimization hasn't been tried |
| **D** sentiment analysis | Solves a different problem: **sentiment doesn't correlate with case complexity** |

**Takeaway.** B is the trap for anyone who has internalized "confidence-based routing" from task statement 5.5 — but there, confidence is **calibrated against a labeled validation set**, not self-reported and trusted raw. Same words, opposite answer. See [[D5 - Context Management & Reliability]] §5.2 and §5.5 · [[EP20 - When AI Needs a Human]].

---

## Scenario: Code Generation with Claude Code

### Q4 — Where a team-wide slash command lives → **A**

**A.** `.claude/commands/` in the project repository.

**Why A wins.** Project-scoped commands are version-controlled and automatically available to everyone who clones or pulls.

| Distractor | Why it fails |
|---|---|
| **B** `~/.claude/commands/` | Personal scope — **not shared via version control**, which is the entire requirement |
| **C** `CLAUDE.md` | For project instructions and context, not command definitions |
| **D** `.claude/config.json` with a `commands` array | **Does not exist.** A fabricated mechanism |

**Takeaway.** *"Available to every developer when they clone"* is the discriminator — it's a version-control question dressed as a config question. See [[D3 - Claude Code Configuration & Workflows]] §3.2 · [[EP11 - Custom Slash Commands & Skills]].

---

### Q5 — Monolith → microservices → **A**

**A.** Plan mode.

**Why A wins.** Plan mode is designed for **large-scale changes, multiple valid approaches, and architectural decisions** — the stem names all three. It enables safe exploration and design before committing.

| Distractor | Why it fails |
|---|---|
| **B** incremental direct execution | Risks costly rework when dependencies surface late |
| **C** direct + comprehensive upfront instructions | Assumes you already know the right structure without exploring the code |
| **D** direct, switch if complexity appears | **The complexity is already stated in the requirements** — it isn't something that might emerge later |

**Takeaway.** D is the sophisticated-sounding wrong answer: it *sounds* adaptive but ignores information already in the stem. See [[D3 - Claude Code Configuration & Workflows]] §3.4 · [[EP12 - Plan Mode vs Execute]].

---

### Q6 — Conventions for test files scattered across directories → **A**

**A.** `.claude/rules/` files with YAML frontmatter glob patterns.

**Why A wins.** Globs (`**/*.test.tsx`) apply conventions **by file path regardless of directory location** — essential when test files are spread throughout the codebase.

| Distractor | Why it fails |
|---|---|
| **B** one root `CLAUDE.md` with headers | Relies on **inference** rather than explicit matching — unreliable |
| **C** skills per code type | Requires manual invocation or Claude *choosing* to load them; contradicts the need for **automatic**, path-driven application |
| **D** per-subdirectory `CLAUDE.md` | `CLAUDE.md` files are **directory-bound** — they can't handle files spread across many directories |

**Takeaway.** The stem plants the discriminator twice: *"spread throughout"* and *"automatically"*. Directory-bound loses to glob-bound; loaded-on-demand loses to always-conditional. See [[D3 - Claude Code Configuration & Workflows]] §3.3 · [[EP10 - CLAUDE.md Hierarchy & Config Rules]].

---

## Scenario: Multi-Agent Research System

### Q7 — Reports cover only visual arts → **B**

**B.** The coordinator's task decomposition is too narrow.

**Why B wins.** The coordinator's logs name the culprit outright: *"creative industries"* became three visual-arts subtasks. **The subagents executed their assigned tasks correctly — the problem is what they were assigned.**

| Distractor | Why it fails |
|---|---|
| **A** synthesis gap-detection | Blames a downstream agent working correctly within its assigned scope |
| **C** search queries | Same — the searches were comprehensive *for the subtopics given* |
| **D** analysis filtering | Same |

> [!IMPORTANT] The diagnostic move
> When a stem hands you a log, the log **is** the answer. Every option that blames a component described as succeeding is wrong by construction. This is the exam guide's *"risks of overly narrow task decomposition by the coordinator"* under task statement 1.2.

**Takeaway.** Trace failures upstream to the decision point, not downstream to the symptom. See [[D1 - Agentic Architecture & Orchestration]] §1.2 · [[EP02 - Multi-Agent Systems & Coordinator Patterns]].

---

### Q8 — Subagent timeout propagation → **A**

**A.** Structured error context: failure type, attempted query, partial results, potential alternatives.

**Why A wins.** It gives the coordinator what it needs to decide — retry with a modified query, try an alternative, or proceed with partial results.

| Distractor | Why it fails |
|---|---|
| **B** retry then generic status | The generic status **hides valuable context** from the coordinator, preventing informed decisions |
| **C** empty result marked successful | **Suppresses the error by marking failure as success** — prevents any recovery, risks silently incomplete research |
| **D** terminate the workflow | Unnecessary when recovery strategies could succeed |

**Takeaway.** C and D are the two named anti-patterns of task statement 5.3 — *silent suppression* and *whole-workflow termination on a single failure*. B is subtler: retry logic is fine, **discarding the context afterwards is not**. See [[D5 - Context Management & Reliability]] §5.3 · [[EP19 - Subagent Error Propagation & Context Management]].

---

### Q9 — Synthesis agent's verification round-trips → **A**

**A.** A scoped `verify_fact` tool on the synthesis agent for simple lookups; complex verifications still route through the coordinator.

**Why A wins.** **Least privilege:** give the agent exactly what the 85% common case needs while preserving the coordination pattern for the 15%.

| Distractor | Why it fails |
|---|---|
| **B** batch verifications at end of pass | Creates **blocking dependencies** — synthesis steps may depend on earlier verified facts |
| **C** all web search tools | **Over-provisions** the synthesis agent, violating separation of concerns. *(vault: it also runs into the too-many-tools reliability penalty of task statement 2.3)* |
| **D** proactive caching | **Speculative** — cannot reliably predict what synthesis will need to verify |

**Takeaway.** The exam guide calls this *"scoped cross-role tools for high-frequency needs"* under 2.3. The 85/15 split in the stem is the design signal: it justifies a narrow exception, not a wholesale grant. See [[D2 - Tool Design & MCP Integration]] §2.3.

---

## Scenario: Claude Code for Continuous Integration

### Q10 — CI job hangs on interactive input → **A**

**A.** `claude -p "…"`.

**Why A wins.** `-p` / `--print` is the documented non-interactive mode: it processes the prompt, writes the result to stdout, and exits.

| Distractor | Why it fails |
|---|---|
| **B** `CLAUDE_HEADLESS=true` | **Non-existent** environment variable |
| **C** stdin from `/dev/null` | A Unix workaround that doesn't properly address Claude Code's command syntax |
| **D** `--batch` | **Non-existent** flag |

> [!TIP] Two of four options are fabricated
> The exam guide's Technologies appendix is the defence: if a flag isn't in it or the CLI reference, treat it as invented. [[EP13 - Claude Code CI-CD Pipelines]] catalogues the fabricated-flag family.

**Takeaway.** See [[D3 - Claude Code Configuration & Workflows]] §3.6.

---

### Q11 — Batch API for both CI workflows? → **A**

**A.** Batch for technical-debt reports only; real-time for pre-merge checks.

**Why A wins.** The Message Batches API gives **50% cost savings** but processing takes **up to 24 hours with no guaranteed latency SLA** — unsuitable for a blocking check developers wait on, ideal for an overnight job.

| Distractor | Why it fails |
|---|---|
| **B** both, with polling | Relying on *"often faster"* completion isn't acceptable for a **blocking** workflow |
| **C** real-time for both | Rests on a **misconception** — batch results *can* be correlated, via `custom_id` |
| **D** both, with timeout fallback | Unnecessary complexity when the simpler answer is matching each API to its use case |

**Takeaway.** The discriminator is the word **blocking**, not cost. Note C's failure mode: a wrong answer justified by a technical claim that is simply false. See [[D4 - Prompt Engineering & Structured Output]] §4.5 · [[EP17 - Batch API & Multi-Pass Review]].

---

### Q12 — Inconsistent single-pass review over 14 files → **A**

**A.** Per-file passes for local issues, then a separate integration pass for cross-file data flow.

**Why A wins.** It addresses the root cause — **attention dilution** when processing many files at once. File-by-file gives consistent depth; the integration pass catches what per-file misses.

| Distractor | Why it fails |
|---|---|
| **B** developers split the PR | **Shifts burden to developers without improving the system** |
| **C** bigger context window | Misunderstands the failure: **larger context windows don't solve attention quality** |
| **D** 3 passes, flag 2-of-3 consensus | Would **suppress detection of real bugs** — issues caught only intermittently are exactly the ones you want reported |

> [!WARNING] C is the trap for anyone thinking in tokens
> The symptoms — superficial coverage, missed bugs, self-contradiction — read like "ran out of room". They aren't: the guide says larger windows *"don't solve attention quality issues"*. *(vault reasoning: contradictory feedback within a single pass is itself evidence the content **fit** — the model just couldn't attend to it evenly.)*

**Takeaway.** Prompt chaining (task statement 1.6) and multi-pass review (4.6) are the same answer from two directions. See [[D4 - Prompt Engineering & Structured Output]] §4.6 · [[D1 - Agentic Architecture & Orchestration]] §1.6.

---

## Cross-cutting summary

| Principle | Questions | Recognize it by |
|---|---|---|
| Code enforces, prompts persuade | 1 | "guaranteed", "compliance", financial consequence, a stated failure % |
| Fix the root cause named in the stem | 2, 7, 12 | The stem hands you logs or a symptom trail |
| Proportionate first response | 2, 3 | "first step", "most effective", an untried cheap fix |
| Least privilege / scoped access | 9 | An 80/20 split in the stem justifying a narrow exception |
| Fabricated options exist | 4, 10 | A flag, env var, or config file you've never seen in the docs |
| Scaling the resource ≠ fixing the design | 12 (window); 3 is adjacent — uncalibrated confidence, not scale | Bigger model, bigger window, more confidence |
| Never suppress or over-terminate on error | 8 | "marked as successful", "terminates the entire workflow" |
| Version control is the scoping question | 4 | "every developer", "when they clone or pull" |

Log any of these you missed in [[Weak Areas Deep Dive]], and cross-reference [[Answer Patterns Index]] — these eight overlap heavily with the patterns it derived from the three CyberSkill sittings.
