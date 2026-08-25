---
tags:
  - CCA-F
  - domain-1
  - domain-2
  - multi-agent
  - tool-design
  - flashcards
  - youtube-course
date: 2026-08-03
status: done
domain: "1 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 04"
---

# 🃏 EP04 Flashcards — Multi-Agent System in Python (Claude SDK)

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP04 - Multi-Agent System in Python (Claude SDK)]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[EP03 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 1 — Coordinator & Task Decomposition

**Q: In the raw Anthropic Python SDK, what physical thing is the coordinator?**
A: A **plain Python function**. It decomposes, delegates by calling a helper, passes context forward, and aggregates — all ordinary control flow. No SDK construct represents it, and no model decides the sequence.

**Q: Name the coordinator's four responsibilities in execution order.**
A: **Decompose** the request into subtasks · **delegate** each to a subagent · **pass context forward** into the next subagent's prompt · **aggregate** the results.

**Q: A refund pipeline has two subtasks: verify the customer, then refund the order. Why does this dependency matter architecturally rather than just logically?**
A: Because subtask 2 is meaningless without subtask 1's result, the coordinator is **forced** to pass a finding forward — exercising context passing. Two independent subtasks would only demonstrate parallel delegation and would teach nothing about the handoff.

**Q: A coordinator built with model-driven delegation and one built as deterministic Python differ in what respect — and do the coordinator's four responsibilities still apply to both?**
A: They differ in **who decides the sequence**: in the Agent SDK, Claude decides via the spawn tool; in the raw API SDK, your code decides. The four responsibilities apply **to both** — only the implementation changes.

**Q: What three arguments distinguish one subagent from another in this capstone's implementation?**
A: **Role**, **tool list**, and **prompt**. Same function, same loop, same model — those three are the entire difference.

**Q: Map role / tools / prompt onto the Claude Agent SDK, and say which one is fixed at definition time versus invocation time.**
A: **Role** → the agent's key + `description` (definition time). **Tools** → `AgentDefinition.tools` (definition time). **Prompt** → the **spawn tool call's `prompt`** (invocation time). Per-task context belongs in the last one.

**Q: An instructor says a subagent "is nothing different from a normal agent." What does that mean mechanically?**
A: A subagent runs **its own complete agentic loop** — the same `stop_reason` cycle and tool dispatch as any agent. "Subagent" names a **role in an architecture** (spawned by a coordinator), not a distinct class or mechanism.

---

## Domain 1 — The Agentic Loop in Code

**Q: How many `stop_reason` values exist, and which ones drive loop control?**
A: **Seven** exist — `end_turn`, `tool_use`, `max_tokens`, `stop_sequence`, `pause_turn`, `refusal`, `model_context_window_exceeded`. **Three** drive a production loop: `end_turn` (exit), `tool_use` (execute and continue), `pause_turn` (resume). **On the exam, only two are on the blueprint:** `tool_use` continues, `end_turn` terminates.

**Q: A loop branches only on `end_turn` and `tool_use`. It hangs forever in production. Which stop reason is responsible and what's the fix?**
A: **`pause_turn`** — a server-side tool paused mid-turn matches neither branch, so `while True` spins with no progress. Fix: append the assistant turn and re-send to resume.

**Q: The same two-branch loop sometimes raises `StopIteration` when extracting the final text. Why?**
A: On **`refusal`**, `content` is **empty**, so a generator like `next(b.text for b in response.content ...)` finds nothing. Always check `stop_reason` **before** indexing `content`.

**Q: In the `tool_use` branch, why append `response.content` rather than just the assistant's text?**
A: Because `response.content` carries the **`tool_use` blocks**. Every `tool_result` must pair with a `tool_use` by `tool_use_id`; a text-only assistant turn breaks that pairing and the API rejects the next request.

**Q: Claude returns three `tool_use` blocks in one response. How many user messages carry the results, and what breaks if you split them?**
A: **One** user message with all three `tool_result` blocks. Splitting them silently trains Claude to **stop making parallel tool calls** — degraded throughput with no error to signal it.

**Q: What is the join key between a `tool_result` and the tool call it answers?**
A: **`tool_use_id`** — it must exactly match the `id` of the corresponding `tool_use` block. Omitting or mistyping it fails the request.

**Q: A tool raises an exception mid-loop. What should go back to Claude instead of crashing the program?**
A: A `tool_result` with the error text as `content` and **`is_error: true`**. Claude acknowledges it and either retries differently or asks for clarification — dropping the result entirely leaves an unpaired `tool_use`.

---

## Domain 2 — Tool Schemas & the Registry

**Q: Name the three top-level fields of a Messages API tool definition, and the keys inside the schema object.**
A: `name`, `description`, **`input_schema`** (snake_case). Inside: `type: "object"`, `properties`, `required`.

**Q: Why does one tool need to be declared twice — once as a schema and once in a registry?**
A: Claude needs a **description it can reason over** (the schema, sent in the request); Python needs a **callable it can invoke** (the registry). The tool's `name` is the only thing linking them.

**Q: Every tool schema is correct, but the loop raises `KeyError` when Claude calls `lookup_order`. What's missing?**
A: The **tool registry entry**. The tool was advertised to Claude but never mapped to a Python function, so the loop can't dispatch it — declared and unreachable.

**Q: What information does Claude actually use to decide which tool to call?**
A: The tool's **`name` and `description` strings** — nothing else. A vague description is therefore a routing bug, not a documentation nit.

**Q: In `AgentDefinition`, what is the tool-restriction field called — and what is `allowedTools`?**
A: The inner field is **`tools`**. `allowedTools` / `allowed_tools` is a **top-level option on `query()`** governing which tools auto-approve without a permission prompt — a different thing at a different level. Confusing them is a classic distractor.

**Q: Which two fields are required on `AgentDefinition`?**
A: **`description`** and **`prompt`** — only those two. `tools`, `model`, `skills`, `maxTurns`, `permissionMode` and the rest are optional.

---

## Domain 1 — Isolation & Least-Privilege Scoping

**Q: A verification subagent gets `[get_customer]`; the refund subagent gets `[lookup_order, process_refund]`. Beyond clarity, what does the narrow list actually buy?**
A: A **capability boundary**. A tool left out of `tools` is **absent from that subagent's session entirely** — the verification agent physically cannot issue a refund. There's no permission prompt and no error; Claude just works without it.

**Q: A team hands every subagent the full tool list "so it has what it needs." What have they given up?**
A: The isolation that justified the architecture. A read-only verification agent can now call `process_refund`, and the wider decision space raises tool-misrouting risk.

**Q: What happens if you omit the `tools` field on an `AgentDefinition` entirely?**
A: The subagent **inherits every tool available to subagents**. That's a deliberate "full access" choice — not a safe default to fall into.

**Q: Two subagents each make 4 tool round trips of ~1,200 tokens. Isolated, each returns a 150-token finding. What shrinks, and what does not?**
A: The **coordinator's context window** shrinks from ~9,600 to ~300 tokens (~97%). **Total tokens billed barely change** — the round trips are still processed, just inside the subagents. Isolation buys headroom and focus, not a smaller bill.

---

## Domain 1 / 5 — Gating, Structure & Hallucination Control

**Q: A pipeline runs with a customer ID that doesn't exist. The refund subagent never runs. Which component stopped it?**
A: The **coordinator's gate** — a conditional that inspects the verification finding before delegating. It is ordinary control flow in the coordinator, not anything the subagent or the model does.

**Q: Why can't the downstream subagent be trusted to refuse on its own when verification failed?**
A: It has **no parent history and no peer to ask**, so a vague prompt gives it nothing to fail against. Its likely move is to **hallucinate** a verification and proceed — producing output indistinguishable from a real success.

**Q: Sequential gating and structured context passing both protect the pipeline. What does each one prevent, specifically?**
A: **Gating** stops the bad subagent from **running at all**. **Structure** ensures that if it *does* run, it can tell "verified" from "not verified". Different layers — gating is the coordinator's job, structure is the payload's.

**Q: Why is structured context passing a reliability control rather than a style preference?**
A: Because the alternative failure is **silent**. Prose gives a subagent no field to branch on, so it invents a plausible finding; the resulting output reads exactly like a correct result, so nothing surfaces the error.

**Q: A colleague's coordinator always spawns both subagents and passes `f"Previous agent said: {finding}"` forward. Name the two independent defects.**
A: **(1) No gating** — subagent 2 runs even when verification failed. **(2) Unstructured context** — prose gives it no field to branch on. Independent: fixing the gate still leaves an agent that can't distinguish verified from unverified when it does run.

---

## Domain 1 — SDK Boundaries (the episode's key correction)

**Q: Which SDK owns the subagent-spawn tool, and what does the other one have instead?**
A: The **Claude Agent SDK** (`claude-agent-sdk`) owns it — the Claude Code harness as a library. The **Anthropic API SDK** (`anthropic`, Messages API) has **no spawn tool at all**; multi-agent structure there is code you write.

**Q: What is the exam answer for how a coordinator spawns a subagent, and what changed about that tool's name?**
A: **The `Task` tool.** It was renamed **`Task` → `Agent` in Claude Code v2.1.63**; current SDK releases emit `"Agent"` in `tool_use` blocks while `"Task"` survives in the `system:init` tools list and `result.permission_denials[].tool_name`. **Exam answer: `Task`.** Real code: match both.

**Q: Can a multi-agent system built entirely on the raw Messages API still be "architecturally correct"?**
A: **Yes.** The coordinator/subagent *pattern* — decomposition, isolation, context passing, aggregation — is implementable as plain functions. What's missing is the *plumbing* (`Task`/`Agent`, `AgentDefinition`), not the architecture.

**Q: Is subagent nesting allowed by default, and how deep?**
A: **Yes** — by default a subagent can spawn subagents up to **three layers** below the main conversation (`CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH` changes it; `1` disables nesting). Hub-and-spoke is a **design principle**, not an SDK constraint.

**Q: Which two things are the Tool Runner and the Claude Agent SDK, and what do they have in common?**
A: The **Tool Runner** (`client.beta.messages.tool_runner`) automates the tool-call loop for tools *you* define, inside the regular `anthropic` SDK. The **Agent SDK** is the full Claude Code harness with built-in tools and subagents. Both are **harness-only — you host them**; neither provides managed deployment.

**Q: Does the Tool Runner remove the need to write a coordinator in the raw SDK?**
A: **No.** It replaces the hand-written `stop_reason` **loop** — generating tool schemas from function signatures and dispatching automatically — but it offers no subagent-spawn tool, so coordination remains your code.

---

*Back to: [[EP04 - Multi-Agent System in Python (Claude SDK)]]*
