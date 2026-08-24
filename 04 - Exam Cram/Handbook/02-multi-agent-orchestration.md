---
tags:
  - CCA-F
  - handbook
  - domain-1
date: 2026-08-24
status: done
domain: "1 of 5"
---

# 02 — Multi-Agent Orchestration (Research Pipeline)

Covers: hub-and-spoke, coordinator/subagent communication, delegation, parallel
fan-out, and provenance. (Mock Q1–Q15; concept chapters 2, 3.)

---

## Core concepts (memorize)

### Hub-and-spoke (orchestrator–worker)
- One **coordinator** receives the task, decomposes it into subtasks, and sends
  each subtask to a **specialized subagent**.
- Each subagent gets **only the tools and context it needs**.
- The coordinator **checks for failure, then synthesizes**.
- **The coordinator is the sole hub.** Subagents are **context-isolated**: they
  do NOT know each other exists and never talk directly. All information flows
  **through the coordinator's prompt.**
- Subtasks are **not pre-defined** — the orchestrator determines them
  dynamically from the specific input.

### Communication rules
- A subagent sees **only what the coordinator puts in its prompt.** If a
  subagent says **"no findings provided,"** the coordinator forgot to forward them.
- Subagents must return **structured results** and must **not fail silently.**
- The coordinator must handle **partial failure**: retry, escalate, or partial synthesis.

### The 4 pieces of info to pass a subagent
1. **Overall objective / goal.**
2. **Constraints / requirements.**
3. **Relevant findings from other subagents** (forwarded by the coordinator).
4. **Desired output format.**

Each subagent prompt must be **self-contained** — understandable from that
prompt alone. Never expect a subagent to infer the coordinator's context.

### Delegate goals, not procedures
- Give subagents an **objective + quality criteria** (coverage breadth, source
  diversity, recency), then let them choose their strategy.
- Rigid step-by-step scripts are brittle: they report "insufficient results"
  instead of adapting, miss emerging topics, and skip valuable tangents.
- Research is **dynamic and path-dependent** — you can't hardcode the path.

### Scale effort to complexity
- Simple fact-check ≈ 1 agent, few tool calls. Complex comparison → full pipeline.
- Let the **coordinator reason per-query** about which subagents to spawn.
  Static routers / trained classifiers **calcify** as the query mix evolves.
- **Multi-agent uses ≈ 15× the tokens** of a single chat → reserve it for tasks
  that truly need parallelism or exceed one context window. Don't spawn a
  subagent to redo work the coordinator **already holds in context** (e.g. a
  simple follow-up summary — do it directly).

### Parallelism & resume
- Parallelize via **coordinator-spawned subagents** (flat fan-out, single hub) →
  keeps it observable/debuggable. Avoid message queues, nested spawning, or
  recursive hierarchies that hide execution.
- For **crash/resume**: each agent persists a **compact structured report** to a
  known location; on resume the coordinator loads them and **re-injects** the
  relevant state into each prompt. (The LeadResearcher persists its plan to
  Memory so work survives a context reset.)
- Subagents are spawned via the **`Task` tool** — renamed to **`Agent`** in Claude
  Code v2.1.63, with `Task` still a valid alias and the **exam-safe answer**.
  Include it in `allowedTools` so delegation auto-approves.
- **`allowedTools` is an auto-approve list, not a restriction list.** Omitting the
  spawn tool doesn't silently disable delegation — the call falls through to your
  permission prompt, or is denied in `dontAsk` mode. The *silent* failure ("agent
  works without it, no prompt, no error") comes from a different surface: leaving
  a tool out of `AgentDefinition.tools`.
  Source: <https://code.claude.com/docs/en/agent-sdk/subagents> (checked 2026-08-24)

### Provenance / citations (don't lose the mapping)
- Keep **content summaries separate from source metadata** (URLs, doc names,
  page numbers, **dates**) in **structured fields** — prose gets dropped during
  summarization.
- Require subagents to output **structured claim→source mappings** that the
  synthesis agent **preserves and merges**. Never reconstruct attribution
  post-hoc (semantic similarity mis-attributes similar sources).
- Attach **provenance/metadata (e.g. dates)** to each data point so synthesis
  can reason ("18% in 2022 → 35% in 2024 = growth", not a contradiction).
- For handoff to a report generator: pass the **synthesis draft + a structured
  source index** (claim→URL+excerpt), not the raw 120K-token corpus.
- Preserve methodological nuance: separate **"well-established" vs "contested"**
  findings instead of averaging confidence into one number.
- **Render each content type natively** — tables for financials, prose for
  news. Don't homogenize everything into bullets.

---

## Decision rules

| Trigger | Answer |
|---|---|
| Subagent: "no research findings were provided" | Coordinator didn't include prior outputs in its prompt |
| Rigid query/step script fails on new topics | Delegate **goals + quality criteria**, not procedures |
| Simple queries traverse the whole pipeline slowly | Coordinator **dynamically decides** which subagents to invoke |
| Follow-up summary spawns a subagent + re-sends 80K tokens | Coordinator summarizes **directly** from its own context |
| Crash mid-run, must resume without redoing work | Persist **structured per-agent reports**; coordinator re-injects state |
| Sequential processing of N items is slow | Coordinator spawns **parallel subagents**, each a subset, then aggregates |
| Citations missing / attribution lost | Structured **claim→source mappings**, preserved through merges |
| Two data points look contradictory across time | Include **dates** in structured output |
| Coordinator reasons about delegating but nothing executes | Spawn tool (`Task`/`Agent`) missing from **`AgentDefinition.tools`** — the tool isn't in the session at all, so no prompt and no error. (If it's merely absent from `allowedTools`, you get a permission prompt instead, not silence.) |

---

## Anti-patterns

- Expecting subagents to **share context automatically** (they're isolated).
- Subagents **calling each other** directly (breaks the hub pattern).
- **Replaying the full conversation log** into every subagent (blows the budget).
- A **vector store** for resume state (over-engineered; lossy vs. a structured report).
- **Message queues / nested subagent spawning** (hurts observability).
- **Reconstructing citations** afterward via logs or semantic similarity.
- **Homogenizing** all subagent outputs into one lossy format.
- Spawning a **multi-agent system for a simple task** (~15× token waste).

---

## Key phrases to recognize

"hub-and-spoke" · "coordinator / orchestrator" · "context-isolated subagents" ·
"self-contained prompt" · "goals not procedures" · "scale effort to complexity" ·
"15× tokens" · "structured report / claim→source mapping" · "Task tool" ·
"re-inject state on resume."

## Sources
- How we built our multi-agent research system — https://www.anthropic.com/engineering/multi-agent-research-system
- Effective context engineering for AI agents — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Building effective agents — https://www.anthropic.com/engineering/building-effective-agents
