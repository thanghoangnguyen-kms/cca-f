---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 3 — Multi-Agent Research System

> [!NOTE] What the blueprint states
> A **coordinator** delegating to **search / document-analysis / synthesis /
> report** subagents, producing **cited reports**. Primary domains **D1, D2, D5**.
> Official sample questions Q7–Q9 · `research_pipeline` in all three CyberSkill sittings.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "**cited** report" / "traceable to sources" | Provenance is a **structured field**, carried from the start — never reconstructed |
| "subagent says it received **no findings**" | The coordinator didn't put prior output **in the subagent's prompt**. Isolation is the design, not the bug |
| "**in parallel** / speed up the fan-out" | Multiple `Task` calls **in a single turn** |
| "sources **disagree**" | Annotate with attribution. Do not silently average or pick a winner |
| "the task is **simple**" | The trap is reaching for multi-agent at all |
| "one subagent **failed**" | Error propagation design — D5, not D1 |
| "outputs come back in **different shapes**" | Render each natively; homogenising is information loss |

> [!IMPORTANT] Subagents are isolated by design
> A subagent inherits **none** of the coordinator's history or tool results — it
> sees only the prompt string it is given. Subagents cannot call each other, and
> nesting is not the fan-out mechanism. Almost every wrong answer in this scenario
> either forgets the isolation or tries to defeat it.

> [!WARNING] `Task`, not `Agent` — for the exam
> The exam guide names the **`Task`** tool and requires `allowedTools` to include
> `"Task"`, stated three times. The SDK renamed it to `Agent` in Claude Code
> v2.1.63, keeping `Task` as a backward-compatible alias. **Answer `Task`.** See
> [[D1 - Agentic Architecture & Orchestration]].

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Coordinator can delegate at all | **`"Task"` in `allowedTools`** | Without it there is no delegation, whatever the prompt says |
| A subagent has what it needs | Coordinator **forwards findings in the subagent's prompt** | The only channel that exists |
| Parallel fan-out | **Multiple `Task` calls emitted in one turn** | Not nesting, not a queue |
| Results return usefully | **Compact structured reports** per subagent | Replaying the full log defeats the purpose of isolating it |
| Citations survive synthesis | **Structured claim → source mappings**, kept separate from prose | Prose loses the mapping; nothing downstream can rebuild it |
| Conflicting figures | **Annotate each with its source** + collection/publication date | Resolving arbitrarily destroys the disagreement, which is itself the finding |
| Subagents adapt to what they find | Delegate **goals + quality criteria**, not a procedure | A rigid script cannot respond to an unexpected source |
| Right-sized architecture | **Simplest thing that works** — coordinator handles simple summaries directly | Multi-agent costs roughly **15×** the tokens |
| A subagent fails | Explicit **error propagation**: the coordinator decides retry, degrade, or surface | Silent partial results are worse than a reported failure |

Task statements in play: **1.2, 1.3, 1.6, 2.3, 5.3, 5.6**.

---

## Trade-offs — what each mechanism costs

- **Multi-agent** buys parallelism and context isolation, and costs ~15× tokens plus a coordination surface that can fail. It pays only when the work genuinely partitions.
- **Context isolation** buys clean subagent reasoning and costs you every implicit assumption — everything must be stated in the prompt.
- **Compact structured reports** buy cheap synthesis and cost detail. Decide *before* fan-out which fields must survive.
- **Structured provenance** costs schema discipline at every hop and is the only thing that makes citation possible at the end.
- **Goals over procedures** buy adaptability and cost predictability; that is why quality criteria travel with the goal.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Assume subagents share the coordinator's context automatically | `DF7` | Names a mechanism that doesn't exist |
| Have subagents call each other directly | `DF7` | Not a capability. Fan-out is flat, through the coordinator |
| Nest subagent spawning to parallelise | `DF5` | Adds a layer where multiple `Task` calls in one turn already work |
| Replay the full conversation log into every subagent | `DF6` | Spends the isolation you paid for, and drowns the subagent |
| Add a vector store or message queue to pass findings between agents | `DF5` | Infrastructure where a prompt field suffices |
| Reconstruct citations afterwards from logs or semantic similarity | `DF6` | Information thrown away and then guessed at. Keep the mapping from the start |
| Average the conflicting statistics, or take the most recent | `DF6` | Destroys the disagreement the report exists to surface |
| Give each subagent a precise step-by-step script | `DF2` | A rule table where judgment belongs |
| Spawn a multi-agent system for a single-source summary | `DF5` | ~15× tokens for work the coordinator can do in one turn |
| Homogenise every subagent's output into one format | `DF6` | Loses the structure each type carries |
| Fix "no findings provided" by strengthening the subagent's system prompt | `DF2` | Raises the odds; the data was never sent |
| Retry the whole pipeline when one subagent fails | `DF5` | Discards valid work from the others |

---

## Drill this scenario

- [[Answer Patterns Index]] § Part 3 drill sets **1 (Provenance)**, **5 (Coordinator hub)**, **6 (Context isolation)**
- Official items **Q7–Q9** in `05 - Practice/Exam Guide - Sample Questions/`
- Unified Bank `research_pipeline` key
- Handbook: [[02-multi-agent-orchestration]] · [[01-agentic-loop-and-tools]]

**Back to:** [[Scenario Index]] · [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D5 - Context Management & Reliability]]
