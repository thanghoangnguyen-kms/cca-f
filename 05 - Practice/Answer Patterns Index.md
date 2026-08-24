---
tags:
  - CCA-F
  - practice-exam
  - patterns
  - answer-key
date: 2026-08-24
status: done
domain: All
---

# 🧭 Answer Patterns Index

**Back to:** [[00 - START HERE]] · [[CCA-F Study Roadmap]] · [README.md](README.md)

> [!NOTE] What this is
> The two CyberSkill answer keys explain **why each answer is right, one question at a time**. This note reads those 120 explanations *sideways* — grouping them into the handful of principles they actually test, and tying each principle back to the trigger rows in [[00-golden-rules-cheatsheet]].
>
> Use it to drill **one pattern across many questions** instead of grinding Q1→Q60. That's what builds transfer to a question you've never seen.

## Notation

| Tag | Set | Questions |
|---|---|---|
| **N-Q##** | [CyberSkill CCAF - New Mock Exam](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/README.md) — has the question text | 60 |
| **O-Q##** | [CyberSkill CCAF - Mock Exam](CyberSkill%20CCAF%20-%20Mock%20Exam/README.md) — answers only, but every entry cites a docs URL | 60 |

> [!WARNING] Numbers never cross sets
> `N-Q7` and `O-Q7` are different questions. The two sittings draw from the same bank (**40 of 60 overlap**) but renumber and reorder every time. This note groups by **principle**, not by item identity — where the same principle appears in both sets, expect that some pairs are literally the same item under two numbers and others are two different questions teaching one rule. Don't try to pair them off.

---

## Part 1 — Golden rule → questions

All 29 trigger rows from [[00-golden-rules-cheatsheet]], with the questions that test each one. **Read down the Hits column: that is your study priority order.**

| Golden rule (trigger → answer) | Hits | New Mock | Old Mock |
|---|:--:|---|---|
| Citations/sources **lost between agents** → structured claim→source mappings | **11** | N-Q24, 25, 28, 43, 48 | O-Q2, 6, 7, 8, 9, 11 |
| **Escalation trigger design** → NL criteria for judgment, code for hard limits | **11** | N-Q2, 23, 45, 58, 59 | O-Q32, 33, 35, 36, 40, 41 |
| Model **invents values** for missing fields → return null, ground to source | **11** | N-Q3, 21, 27, 36, 46, 49 | O-Q49, 50, 52, 54, 56 |
| **Unfamiliar large codebase** → grep entry points → read → follow imports | **10** | N-Q7, 9, 10, 14, 20, 38 | O-Q17, 23, 25, 30 |
| Subagent says **"no findings provided"** → coordinator didn't forward it | **9** | N-Q5, 17, 47, 55, 60 | O-Q4, 5, 10, 14 |
| Long session **degrades** → compaction / scratchpad / subagent with a summary | **8** | N-Q1, 11, 44, 53 | O-Q13, 18, 20, 24 |
| **Resume / fork / delta** (`--resume`, `fork_session`, name the delta) | **8** | N-Q31, 42, 50 | O-Q19, 21, 22, 26, 28 |
| Extraction **format/split/recall** inconsistent → diverse few-shot examples | **8** | N-Q22, 33, 35, 57 | O-Q47, 51, 53, 57 |
| Tool **errors handled inconsistently** → category + `isRetryable` + cause, `isError=true` | **6** | N-Q8, 15, 16 | O-Q38, 43, 44 |
| **Batch vs sync** + `custom_id` retry + SLA math | **6** | N-Q4, 18, 19 | O-Q46, 58, 60 |
| Agent picks the **wrong tool** → improve tool descriptions first | **5** | N-Q12, 37 | O-Q3, 16, 27 |
| **Stale tool result** / verbose tool output → new session + summary, or prune fields | **5** | N-Q26, 54 | O-Q31, 42, 45 |
| "**compliance / must always**" → deterministic hook or gate in code | **4** | N-Q34, 41 · N-Q32 *(inverse)* | O-Q34 |
| **Limited review capacity** / "97% in aggregate" → calibrated confidence, disaggregate | **4** | N-Q51, 56 | O-Q48, 59 |
| Give a subagent a **rigid script** → delegate goals + quality criteria | **2** | N-Q29 | O-Q12 |
| Agent **"forgot" earlier turns** → you aren't re-sending `messages` (stateless) | **2** | N-Q30 | O-Q37 |
| Loop mechanics → `tool_result` appended, model reasons, model picks next | **2** | N-Q6 | O-Q39 |
| **Force a specific tool first** → `tool_choice: {"type":"tool",...}`, then `auto` | **2** | N-Q13 | O-Q55 |
| Agent has **too many tools** → split into specialized subagents (~4–5 each) | 1 ⚠ | — | O-Q3 *(oblique)* |
| "return JSON reliably" → tool input schema + `tool_choice`, then validate | 0 ⚠ | — | — |
| Loop **won't stop / stops too early** → `stop_reason == "end_turn"` | 0 ⚠ | — | — |
| **Non-interactive / CI** → `claude -p --output-format json` | 0 ⚠ | — | — |

> [!IMPORTANT] The four ⚠ rows are in the cheat sheet but **untested by either sitting**
> That is not permission to skip them — these two sittings are one bank, and `stop_reason` and `claude -p` are core published objectives. It does mean the CyberSkill sets **cannot** rehearse them, so drill those four from [[00 - Claude Model Family & API Fundamentals]], [[D1 - Agentic Architecture & Orchestration]] and [[D3 - Claude Code Configuration & Workflows]] instead. Both sittings are also silent on the whole of **D3/Claude Code ops** (`CLAUDE.md` hierarchy, hooks, plan mode, `.mcp.json` scoping) — a real blind spot in this practice source.

### Clusters the cheat sheet has no row for

Four principles appear in the keys with **no** matching trigger row. Candidates for adding to [[00-golden-rules-cheatsheet]]:

| Principle | Hits | Questions |
|---|:--:|---|
| Conflicting sources → verify against the **primary** source; state the conflict, never average it | 2 | N-Q40, N-Q52 |
| `Edit`'s unique-match contract can't be met → **Read → modify → Write**, stop tuning `old_string` | 2 | N-Q39, O-Q29 |
| Crash mid-run → persist **compact structured per-agent artifacts**, coordinator re-injects | 1 | O-Q1 |
| A coordinator that reasons about delegating but never acts is **missing the spawn tool from `AgentDefinition.tools`** | 1 | O-Q15 |

> [!IMPORTANT] `O-Q15` — resolved, and the mechanism is sharper than the key states it
> The old key attributes the silent no-op to the coordinator's **allowed tools**. [[02-multi-agent-orchestration]] (corrected against docs 2026-08-24) draws the distinction the exam actually tests:
>
> - Spawn tool absent from **`AgentDefinition.tools`** → the tool isn't in the session at all, so **no prompt and no error — just silence**. This is the `O-Q15` scenario.
> - Spawn tool merely absent from **`allowedTools`** → you get a **permission prompt**, not silence.
>
> On the tool's *name*, the Handbook now hedges deliberately: **`Task`/`Agent`**. `AGENTS.md` lists the `Task` → `Agent` rename as a known correction, and it is still not pinned to one string — so learn the **mechanism**, and don't bet on the name.

> [!NOTE] This cluster is no longer unmapped
> The Handbook gained a row for it in [[02-multi-agent-orchestration]] on 2026-08-24. It stays listed here because [[00-golden-rules-cheatsheet]] — the page this index maps against — still has no trigger row for it.

---

## Part 2 — The 22 principles behind 120 questions

Ranked by how often the bank tests them. **Anything at 6+ is a near-certain exam topic.**

### Tier 1 — test these cold (8+ hits)

1. **Provenance must be structural, not prose** — 11 · `N-Q24, 25, 28, 43, 48` · `O-Q2, 6, 7, 8, 9, 11`
   Claim + source pointer is the atomic unit. Standardize *metadata* (source, date, confidence); preserve *content shape*. If attribution can be lost by rewording, it will be.
2. **Escalate on reasons, not symptoms** — 11 · `N-Q2, 23, 45, 58, 59` · `O-Q32, 33, 35, 36, 40, 41`
   Asked for a human / needs authority / stuck — never retry-count or sentiment score. Hand off a structured brief, not a transcript dump. Declining in-scope work is not escalation; declining *out-of-scope* work is correct.
3. **Null is a valid answer; a plausible guess is not** — 11 · `N-Q3, 21, 27, 36, 46, 49` · `O-Q49, 50, 52, 54, 56`
   Explicitly license "not found." Give closed enums an `"other"` + detail field. Assert the source's own invariants (computed *and* stated), flag the mismatch, never auto-correct.
4. **Orient → narrow → read** — 10 · `N-Q7, 9, 10, 14, 20, 38` · `O-Q17, 23, 25, 30`
   Structure first, grep second, full reads last. Abstraction before instances. Docs are a hypothesis — confirm in the code you're about to edit.
5. **The coordinator is the sole communication hub** — 9 · `N-Q5, 17, 47, 55, 60` · `O-Q4, 5, 10, 14`
   Workers never talk to each other. Fan out flat from the coordinator. If you draw an arrow between two workers, it's the wrong diagram. Overlapping coverage is healthy — reconciling it is the coordinator's job.
6. **Delegate the reading, keep the synthesis** — 8 · `N-Q1, 11, 44, 53` · `O-Q13, 18, 20, 24`
   Sub-agents buy *context isolation*, not speed for its own sake — if the coordinator already holds the data, inline is the optimisation. Summarize *before* you isolate. A scratchpad is durable memory; a bigger window is only a slower leak.
7. **Session lifecycle: resume, fork, delta** — 8 · `N-Q31, 42, 50` · `O-Q19, 21, 22, 26, 28`
   `--resume <id>` targets a specific session, `--continue` only the latest. `fork_session` = shared history, divergent futures. Files changed? Resume and name the **delta**.
8. **Few-shot for variety; specify before you tune** — 8 · `N-Q22, 33, 35, 57` · `O-Q47, 51, 53, 57`
   "Inconsistent output" is a missing-specification problem — show the canonical shape before touching model tier or temperature. Schema constrains *structure*; the prompt constrains *convention*.

### Tier 2 — high yield (4–6 hits)

9. **Errors are a machine-readable contract, returned in-loop** — 6 · `N-Q8, 15, 16` · `O-Q38, 43, 44`
   In the tool result with `isError` set — never an empty result, never a thrown exception, never a fake success. Carry category + retryability + cause + customer-ready language.
10. **Batch is a throughput discount, not a latency tier** — 6 · `N-Q4, 18, 19` · `O-Q46, 58, 60`
    Split traffic by latency need. SLA = your accumulation window + up to 24h. `custom_id` is the retry key.
11. **Tool descriptions are the model's only selection input** — 5 · `N-Q12, 37` · `O-Q3, 16, 27`
    Write them like API docs: capability, when to prefer, what comes back. Fix the description *before* adding prompt routing or removing the competing tool.
12. **Prune the conversation, don't out-argue it** — 5 · `N-Q26, 54` · `O-Q31, 42, 45`
    Stale `tool_result` blocks poison reasoning. Carry a summary forward; select the task-relevant fields. Compact resolved threads, keep the active one verbatim.
13. **Must-always → code; usually-right → prompt** — 4 · `N-Q34, 41` · `O-Q34` · inverse: `N-Q32`
    Prompts move probability, code fixes outcome. The inverse matters just as much: when routing depends on judgment over an **open-ended** space, let the model route — static rules are for closed, stable sets.
14. **Never automate on an aggregate you haven't decomposed** — 4 · `N-Q51, 56` · `O-Q48, 59`
    Segment by document type × field. Route scarce human review by *calibrated* risk, not random sampling.

### Tier 3 — one item each way, but cheap to memorize (1–2 hits)

15. **Delegate goals and quality bars, not procedures** — 2 · `N-Q29` · `O-Q12` — procedural delegation caps a sub-agent at your foresight.
16. **The API is stateless** — 2 · `N-Q30` · `O-Q37` — "the agent forgot" means the history wasn't resent.
17. **The agentic loop** — 2 · `N-Q6` · `O-Q39` — tool result appended → model reasons → model picks next. If you can draw the flowchart in advance, it isn't an agentic loop.
18. **`tool_choice` is a per-request lever** — 2 · `N-Q13` · `O-Q55` — force turn 1, then release to `auto`.
19. **Conflicting sources → primary source wins** — 2 · `N-Q40, 52` — surface with full context only when you can't resolve it.
20. **`Edit` unique-match unmeetable → Read → modify → Write** — 2 · `N-Q39` · `O-Q29`.
21. **Crash recovery = compact structured artifacts** — 1 · `O-Q1` — the coordinator re-injects relevant state.
22. **A coordinator can't delegate a tool it doesn't have** — 1 · `O-Q15` — spawn tool missing from `AgentDefinition.tools` fails **silently**; missing from `allowedTools` merely prompts. See the callout in Part 1.

---

## Part 3 — Drill sets

Do each set back to back in one sitting, then write the shared rule from memory **before** reading the key. If your sentence matches the principle above, you own the pattern.

| # | Set | Questions | Where |
|---|---|---|---|
| 1 | Provenance | N-Q24, 28, 25, 43, 48 | [research_pipeline.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/research_pipeline.md) |
| 2 | Escalation | N-Q2, 23, 45, 58, 59 | [customer_support.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/customer_support.md) |
| 3 | Grounding / null | N-Q27, 46, 36, 49, 21, 3 | [extraction_pipeline.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/extraction_pipeline.md) |
| 4 | Exploration order | N-Q7, 20, 38, 14, 9, 10 | [code_exploration.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/code_exploration.md) |
| 5 | Coordinator hub | N-Q47, 55, 60, 5, 17 | [research_pipeline.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/research_pipeline.md) |
| 6 | Context isolation | N-Q1, 11, 53, 44 | [code_exploration.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/code_exploration.md) |
| 7 | Sessions | N-Q31, 42, 50 **+ O-Q19, 22, 28** | [code_exploration.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/code_exploration.md) · [O](CyberSkill%20CCAF%20-%20Mock%20Exam/code_exploration.md) |
| 8 | Errors | N-Q8, 15, 16 **+ O-Q43** | [customer_support.md](CyberSkill%20CCAF%20-%20New%20Mock%20Exam/Answer%20Key/customer_support.md) · [O](CyberSkill%20CCAF%20-%20Mock%20Exam/customer_support.md) |

> [!TIP] Why set 7 and 8 reach into the old sitting
> The old key cites a **docs URL on all 60 entries**; the new key doesn't. When a principle needs grounding rather than another example, go read the old sitting's version of it — `O-Q19` is the cleanest `--resume` vs `--continue` statement in the vault, with the source attached.

---

## Two things the answer distribution tells you

**Letter frequency across the New Mock key: B ×26, C ×15, D ×11, A ×8.** B is 43% of the key; A is 13%.

> [!WARNING] This is an authoring artifact, not an exam property
> The bank's items are written as "naive option A → correct option B." Do not carry a letter bias into the real exam. Its only legitimate use is as a **last-resort tiebreaker** on a question you genuinely cannot reason through — and if you're using it more than once or twice, the fix is Part 2, not the coin flip.

**The more useful tell is structural:** in almost every item, the wrong answers fail in one of four recognizable ways —

1. **Proxy for the real signal** — retry counts, sentiment scores, aggregate accuracy standing in for the thing you actually care about.
2. **Prompt where code belongs** (or the inverse: a static rule table where judgment belongs).
3. **Information thrown away, then reconstructed** — post-hoc citations, an `analyze_error` tool to re-derive what the server already knew, retries to recover data that was never in the input.
4. **Complexity that doesn't pay** — spawning a subagent for work the coordinator already holds, nesting fan-out, enlarging the context window instead of chunking.

Learning to *name the failure mode* is faster than remembering 120 answers. Full catalogue: [[07-anti-patterns-catalog]].

---

## Related notes

- [[Weak Areas Deep Dive]] — log misses here; the **"Recurring traps I keep falling for"** section is still empty, and Part 2's tier list is the natural seed for it
- [[00-golden-rules-cheatsheet]] · [[07-anti-patterns-catalog]] · [[08-quick-reference-tables]]
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]
- [[Flashcards]] · [[Critical Terms Glossary]]
