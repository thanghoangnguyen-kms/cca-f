---
tags:
  - CCA-F
  - domain-1
  - multi-agent
  - flashcards
  - youtube-course
date: 2026-08-02
status: done
domain: "1 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 02"
---

# 🃏 EP02 Flashcards — Multi-Agent Systems & Coordinator Patterns

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP02 - Multi-Agent Systems & Coordinator Patterns]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[EP01 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 1 — Multi-Agent Architecture

**Q: What are the three failure modes that force a move from single-agent to multi-agent?**
A: **Context ceiling** (tool results accumulate and exhaust the window before the task finishes), **sequential bottlenecks** (tools fire one at a time, so independent subtasks queue), and the **specialization gap** (one agent doing everything yields generic output).

**Q: What is the routing mandate in hub-and-spoke topology?**
A: All communication routes **exclusively** through the coordinator. Subagent A cannot message subagent B — it doesn't know B exists.

**Q: Why is forbidding subagent-to-subagent communication an enabler rather than a restriction?**
A: Direct links create dependencies, and dependent workers can't run simultaneously. Mutual independence is precisely what permits **parallel spawning** — and routing everything through one node gives absolute observability.

**Q: What are the four coordinator responsibilities?**
A: **Task decomposition** → **delegation** → **result aggregation** → **error handling**. The exam asks these as a set.

**Q: Which agent produces the final output in a multi-agent system?**
A: The **coordinator only**. Subagents do their scoped piece and report back; they are never responsible for synthesizing the final result.

**Q: Fill in the spawn-tool permission matrix — and where does it stop being enforced?**
A: Coordinator ✅ (required — no spawn tool, no delegation). Standard worker subagent ❌. Hierarchical sub-coordinator ✅, but only if *explicitly designed* to delegate to a deeper tier. **This is a design principle, not an SDK constraint:** subagents nest up to **three layers** by default (Claude Code v2.1.219+). Set `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH=1` to actually forbid it.

**Q: The lecture calls the spawn tool `Task`. What is it actually called now?**
A: Renamed **`Task` → `Agent`** in Claude Code v2.1.63. Current SDK emits `"Agent"` in `tool_use` blocks; `"Task"` survives in the `system:init` tools list and `permission_denials[].tool_name`. **Exam answer: `"Task"`.** Real code: use `"Agent"`, and match both when parsing.

**Q: How do you spawn subagents in parallel rather than sequentially?**
A: Emit **multiple spawn-tool calls in a single coordinator response** — one assistant message carrying several sibling `tool_use` blocks. Calls spread across separate turns run sequentially, not in parallel.

**Q: Do subagents inherit the coordinator's conversation history?**
A: **No.** Each starts a fresh context and receives nothing of the parent's conversation or tool results. The coordinator must inject anything the subagent needs.

**Q: Give the wall-clock formulas for parallel vs sequential subagent spawning.**
A: $T_{\text{parallel}} = \max(S_1 \ldots S_n)$ — the slowest single subagent. $T_{\text{sequential}} = \sum S_i$ — runtimes add. Sequential defeats the purpose of multi-agent entirely.

**Q: Exam diagnostic — the system runs ~3× slower than expected. What's the fault and how do you confirm it?**
A: The **sequential spawning anti-pattern**. Confirm by inspecting the payload: does **one** assistant response carry multiple sibling `tool_use` blocks named `Task`, or does each turn carry only one?

**Q: A subagent returns irrelevant, duplicated, or context-unaware output. What is the root cause?**
A: **Missing explicit context injection.** The subagent started blank and the coordinator never gave it what it needed. The lecture puts this at ~99% of exam scenarios — and the exam may not even show you the `AgentDefinition`.

**Q: Through which channel does a coordinator inject context into a subagent?**
A: The **spawn tool call's `prompt` string** — the per-invocation task — written programmatically at spawn time. There is no other channel. **Not** the static `AgentDefinition.prompt`, which is the subagent's *system* prompt and is fixed at definition time; putting task context there bakes one task's context into every future invocation.

**Q: A subagent's context is described as "blank." What does it actually start with?**
A: **Receives:** its own system prompt (`AgentDefinition.prompt`), the spawn tool call's `prompt` string, project `CLAUDE.md` (via `settingSources`), and tool definitions. **Does not receive:** the parent's conversation history or tool results, the parent's system prompt, or preloaded skill content unless listed in `skills`. Nothing task-specific crosses unless the coordinator puts it there.

**Q: What belongs in a subagent prompt, and what must not?**
A: ✅ The **goal / success criteria**, **constraints and guardrails**, and the **injected context**. ❌ Step-by-step procedural instructions — they make the agent brittle the moment a source is down, moved, or unhelpful.

**Q: Why is "go to Google Scholar, take the first 5 results" wrong even when the site is up?**
A: It binds the agent to a procedure rather than an outcome. The top 5 may be irrelevant or off-constraint, and a procedurally-bound agent has no criteria to notice that or search elsewhere. Goals plus constraints let it adapt.

**Q: What typically causes siloed (fragmented) final output?**
A: **Overly narrow decomposition** combined with weak **result aggregation** — the coordinator concatenating independent outputs instead of synthesizing them into a unified whole.

**Q: The lecture names four `AgentDefinition` fields. Which are actually required?**
A: **Only `description` and `prompt`.** The lecture's other two are optional, and it names the wrong field: inside an `AgentDefinition` it is **`tools`**, while `allowedTools` is the separate coordinator-level permission list. Full optional set: `tools`, `disallowedTools`, `model`, `skills`, `memory`, `mcpServers`, `initialPrompt`, `maxTurns`, `background`, `effort`, `permissionMode`.

---

*Back to: [[EP02 - Multi-Agent Systems & Coordinator Patterns]]*
