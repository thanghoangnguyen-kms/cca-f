---
tags:
  - CCA-F
  - handbook
  - anti-patterns
date: 2026-08-24
status: done
---

# 07 — Anti-Patterns Catalog (recognize the wrong answer instantly)

Most distractors on the exam are recycled from this list. If an option matches
one of these, it's almost certainly **wrong**. Grouped by theme.

---

## A. Prompt vs. code

- ❌ Enforcing a **mandatory/compliance** rule with a **prompt** ("emphatic
  language", "few-shot", "prefer newest"). ✅ Use a **hook / gate / code check**.
- ❌ Relying on the model to **interpret a tool error and choose** to escalate for
  a compliance rule. ✅ Intercept the tool call deterministically.
- ❌ Escalation on **sentiment** or a fixed **failed-call counter** or an
  **issue×segment×product rules engine**. ✅ Clear NL criteria for judgment +
  code for hard limits.
- ❌ Trusting **uncalibrated** model self-reported confidence as a safety mechanism.

## B. The agentic loop

- ❌ Stopping the loop by **iteration count**. ✅ Stop on `stop_reason == "end_turn"`
  (cap is a safety ceiling only).
- ❌ **Not appending** the assistant turn or tool results before re-invoking.
- ❌ Returning tool errors as **ambiguous strings** or **empty success**;
  throwing an **uncaught exception**. ✅ `isError=true` (API field: `is_error`; `isError` is MCP/TS camelCase) + structured metadata.

## C. Tools & architecture

- ❌ Fixing wrong-tool selection by **adding system-prompt routing** or
  **deleting** the competing tool. ✅ **Rewrite the tool description** (when-to-use,
  I/O, differentiation).
- ❌ Giving **one agent 15+ tools** ("god agent"). ✅ Split into specialized agents
  (~4–5 tools each). **Architecture before prompt.**
- ❌ Splitting a fuzzy tool into more tools that are **also thinly described**.
- ❌ "Return JSON please" in the prompt for **structured output**. ✅ Schema +
  `tool_choice` + validation.
- ❌ `tool_choice:"any"` to force a **specific** tool (it forces *a* tool). ✅ Named `tool`.

## D. Multi-agent

- ❌ Assuming subagents **share context automatically** or **call each other**.
  ✅ Coordinator forwards context in the prompt; subagents are isolated.
- ❌ **Replaying the full conversation log** into every subagent. ✅ Compact
  structured reports.
- ❌ **Vector store / message queue / nested spawning** for resume or fan-out.
  ✅ Structured per-agent reports + flat coordinator fan-out.
- ❌ Giving a subagent a **rigid procedural script**. ✅ Delegate **goals + quality criteria**.
- ❌ Spawning a **multi-agent system for a simple task** (~15× tokens). ✅ Simplest
  thing that works; coordinator handles simple summaries directly.
- ❌ **Reconstructing citations** post-hoc via logs or semantic similarity.
  ✅ Preserve **structured claim→source mappings** from the start.
- ❌ **Homogenizing** all outputs into one format. ✅ Render each type natively.

## E. Context & sessions

- ❌ Curing context degradation with a **bigger window**. ✅ Multi-pass /
  scratchpad / compaction / subagents.
- ❌ **Auto-clearing on a timer** or **pre-summarizing every file upfront**.
- ❌ **Fresh start** (re-read everything) when you could **resume**; or re-reading
  **all** files when only a few changed. ✅ Resume + communicate the **delta**.
- ❌ Resuming **silently** on stale files. ✅ Tell the agent what changed.
- ❌ `--continue` when the target **isn't the most recent** session. ✅ `--resume <name>`.
- ❌ **Bulk-reading** a whole codebase or choosing files **by size**; keyword
  **dragnet**. ✅ Grep entry points → follow imports just-in-time.
- ❌ Building a **full plan for an unknown error** before exploring. ✅ Adaptive decomposition.
- ❌ Giant/brittle `old_string`, or `replace_all` that **corrupts** the file.
  ✅ Read → edit in memory → Write when uniqueness can't be met.

## F. Customer support

- ❌ **Editing/deleting tool_result** from the transcript middle (orphans
  `tool_use`). ✅ New session + structured summary + fresh calls.
- ❌ Handing a human the **full transcript** or a **raw dump**. ✅ Structured brief
  (ID + root cause + amount + recommended action).
- ❌ **Confirming** an action that didn't process; **immediate escalation** of a
  transient blip; **open-ended retry loops**.
- ❌ **Sliding a fixed window** that drops the asked-about topic; **paraphrasing**
  verbose tool output (loses exact values). ✅ Compact resolved / prune to fields.
- ❌ Concluding the model has a **"2-turn memory limit"**. ✅ It's stateless —
  re-send the full `messages` array.

## G. Extraction

- ❌ **Everything to sync** (wastes 50% discount) or **everything to batch**
  (misses latency SLA). ✅ Segment by latency.
- ❌ Meeting a 30-min SLA with **hourly batches** (still up to 24h). ✅ Sync API.
- ❌ Raising **`max_tokens`** for `context_length_exceeded` (that's *input*).
  ✅ **Chunk the input**; retry only failed `custom_id`s.
- ❌ Making missing fields **required** (forces hallucination). ✅ Null when not stated.
- ❌ **Retrying** to recover information **absent from the input**; **regex over prose**.
  ✅ Few-shot for recall; accept the data isn't there.
- ❌ **Endlessly expanding an enum** / free-form / lossy mapping. ✅ `"other"` + detail.
- ❌ **Silently auto-correcting** financial numbers. ✅ Computed + stated, flag mismatch.
- ❌ Automating on **aggregate accuracy**; **random sampling** for coverage;
  post-processing for what the model should normalize at extraction time.

## H. Claude Code ops

- ❌ Running in CI **without `-p`** (hangs). ✅ `claude -p --output-format json`.
- ❌ **MCP/env in CLAUDE.md** or **coding rules in settings.json** (swapped).
- ❌ Assuming **`--bare`** still runs hooks / loads CLAUDE.md / writes memory.
- ❌ Using **`sed -i`** where **Edit** is the right tool.

---

## The two questions to ask on every item

1. **Must this always be true?** → code/hook/schema (not prompt).
2. **Am I preserving structure & provenance, or losing it?** → keep metadata in
   structured fields; ground to the source; prefer "unknown" over a guess.
