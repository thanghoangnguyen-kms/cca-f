---
tags:
  - CCA-F
  - scenarios
date: 2026-09-03
status: done
---

# Scenario 1 — Customer Support Resolution Agent

> [!NOTE] What the blueprint states
> Agent SDK agent for **returns, billing disputes, account issues**. MCP tools
> `get_customer`, `lookup_order`, `process_refund`, `escalate_to_human`.
> Target **80%+ first-contact resolution**. Primary domains **D1, D2, D5**.
> Official sample questions Q1–Q3 · `customer_support` in all three CyberSkill sittings.

---

## Requirements → what actually matters

| The stem says | What it actually constrains |
|---|---|
| "**process_refund**" with any monetary limit | A money-moving action with a hard ceiling. Ceilings are **never** prompt-enforced |
| "**80%+ first-contact resolution**" | Escalation must be *selective*. An agent that escalates on any wobble fails this target as surely as one that never escalates |
| "**escalate_to_human**" exists as a tool | Escalation is a designed path, not a failure. The question is *what triggers it* |
| "compliance / regulated / must never / cannot rely on the model" | Deterministic gate. Stop reading the prompt-shaped options |
| "the customer asks for a human" | An **explicit signal**. Explicit beats inferred every time |
| "long conversation" / "earlier in the chat" | A context question wearing a support costume — see D5 |

> [!IMPORTANT] The qualifier that decides most items here
> Escalation questions split on **explicit customer signal vs. model-perceived
> complexity**. An explicit request, a regulated topic, or a hard limit breached
> are deterministic triggers. "The model thinks this is hard" is not one.

---

## Requirement → mechanism → layer

| Requirement | Mechanism | Why this layer |
|---|---|---|
| Refund ceiling always honoured | **`PreToolUse` hook / gate intercepting the tool call** | Prompts move probability; code fixes outcome. This is the single most-tested item in the vault |
| Agent picks the right tool among similar ones | **Rewrite the tool description** — when-to-use, I/O, differentiation | The model selects on description text, not on system-prompt routing |
| Tool failures handled sanely | **Structured error**: `isError=true` + category + `isRetryable` + cause | The agent needs to *decide* retry vs escalate; a bare string gives it nothing to decide on |
| Escalation triggers | **Clear natural-language criteria** for judgment calls, **code** for hard limits | Judgment needs language; limits need determinism. Neither substitutes for the other |
| Handoff to a human | **Structured brief** — customer ID, root cause, amount, recommended action | The human needs a decision, not a transcript |
| Loop terminates correctly | Stop on `stop_reason == "end_turn"`; append the assistant turn **and** tool results before re-invoking | An iteration cap is a safety ceiling, not a stop condition |
| "Agent forgot earlier turns" | Re-send the **full `messages` array** — the API is stateless | Nothing to fix in the agent; the caller dropped history |
| Stale tool results poisoning a return visit | **New session + structured summary + fresh tool calls** | See the trade-offs below — this is not the same as "start over" |

Task statements in play: **1.1, 1.4, 1.5, 2.1, 2.2, 5.1, 5.2, 5.3**.

---

## Trade-offs — what each mechanism costs

- **Hooks/gates** buy determinism and cost flexibility. A gate cannot reason about a legitimate exception, so it must fail *closed* to escalation, not fail silently.
- **Richer tool descriptions** buy correct selection and cost tokens on every single request, since descriptions are always in context.
- **Aggressive escalation** buys safety and destroys the 80% target. The metric in the stem is a real constraint, not decoration.
- **Compaction** buys headroom and costs exact values — which is why you prune to *fields*, never paraphrase tool output.
- **A fresh session with a summary** buys clean context and costs whatever the summary omits. That is a real cost; it is still cheaper than reasoning over poisoned state.

---

## Attractive but wrong

| Option | Family | Why it fails here |
|---|---|---|
| Add emphatic language or few-shot examples so the agent respects the refund limit | `DF2` | A limit is a guarantee. Prompts cannot supply one |
| Escalate when sentiment turns negative | `DF1` | Sentiment is a proxy. Angry ≠ needs a human; calm ≠ doesn't |
| Escalate after N failed tool calls | `DF1` | A counter is a proxy for judgment. Three transient timeouts are not a support problem |
| Route with an `issue × segment × product` rules engine | `DF1` | A rule table where judgment belongs — the mirror image of using a prompt where code belongs |
| Trust the model's self-reported confidence as the safety mechanism | `DF1` | Uncalibrated confidence is not a measurement |
| Let the model read the tool error and decide whether to escalate for a compliance rule | `DF2` | Interception must be deterministic |
| Delete or edit the stale `tool_result` from the middle of the transcript | `DF7` | Orphans its `tool_use` and corrupts the conversation. Repair by starting clean with a summary |
| Hand the human agent the full conversation transcript | `DF6` | Throws the analysis away and makes the human redo it |
| Paraphrase verbose tool output to save context | `DF6` | Loses the exact values — amounts, IDs, dates — that the case turns on |
| Slide a fixed-size context window | `DF6` | Drops precisely the earlier topic the customer is now asking about |
| Escalate immediately on a transient tool blip | `DF4` | Solves a reliability problem the stem didn't pose, and torches the resolution target |
| Open-ended retry loop until the tool succeeds | `DF5` | Unbounded cost with no path to resolution |
| Confirm the refund to the customer before the tool result comes back | `DF7` | Reports an outcome that did not happen |
| Conclude the model has a "2-turn memory limit" | `DF7` | Names the symptom. The API is stateless |

---

## Drill this scenario

- [[Answer Patterns Index]] § Part 3 drill sets **2 (Escalation)** and **8 (Errors)**
- Official items **Q1–Q3** in `05 - Practice/Exam Guide - Sample Questions/`
- Unified Bank `customer_support` key
- Handbook: [[04-customer-support]] · [[01-agentic-loop-and-tools]] · [[00-golden-rules-cheatsheet]]

**Back to:** [[Scenario Index]] · [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D5 - Context Management & Reliability]]
