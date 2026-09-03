---
tags:
  - CCA-F
  - handbook
  - anti-patterns
date: 2026-09-03
status: done
---

# 07 — Anti-Patterns Catalog (why a wrong option looks right)

Knowing the concepts is not what the exam charges for. Every item hands you
three or four options that are each defensible in isolation, and asks which one
is *most appropriate for this scenario*. That is a discrimination task, not a
recall task.

So this file is read in three passes:

1. **The interrogation** — four questions to run on every option, every time.
2. **Distractor families** — the handful of reasons a wrong option feels right.
   Name the family (`DF1`–`DF7`) and the option collapses.
3. **The themed catalog** — the specific ❌ → ✅ pairs, grouped by mechanism.
   Most distractors on the exam are recycled from this list.

---

## Part 1 — The interrogation

Run this on **every option**, including the one you like. Answer in words, not
vibes. If you cannot answer 1 and 2 for an option, you do not understand it yet
and you are about to guess.

| # | Question | What a bad answer sounds like |
|---|---|---|
| 1 | **What problem does this option actually solve?** | "It improves things." Name the mechanism and its effect. |
| 2 | **Which stated requirement in the scenario does it address?** | Silence. If it maps to no requirement in the stem, it is out — however good it is in general. |
| 3 | **What is the anti-pattern or hidden trade-off?** | "None." Everything trades something: tokens, latency, determinism, provenance, complexity. |
| 4 | **Why is each other option less suitable?** | "It just is." You must be able to name the family (Part 2) each rejected option belongs to. |

> [!IMPORTANT] Question 4 is the one that moves your score
> Being able to justify the right answer is not the same skill as being able to
> eliminate the other three, and the exam only ever tests the second. If you can
> only do the first, you will feel confident and score in the 600s.

**Fast pre-filter.** Before the four, two questions resolve a large share of
items outright:

1. **Must this always be true?** → code / hook / schema, never a prompt.
2. **Am I preserving structure and provenance, or losing it?** → keep metadata in
   structured fields; ground to the source; prefer "unknown" over a guess.

---

## Part 2 — Distractor families

A wrong option is rarely absurd. It is one of these seven. Learn to name the
family out loud — naming it is what makes elimination fast and repeatable.

> [!NOTE] Where this list comes from
> Families 1, 2, 5, 6 and 7 were derived empirically in
> `05 - Practice/Weak Areas Deep Dive.md` from the 180 CyberSkill answer slots —
> they are what the wrong options in a real bank actually did. Families 3 and 4
> were added from passer reports of the live exam. The empirical five are the
> load-bearing ones; treat 3 and 4 as likely but less proven.

### DF1 · A proxy standing in for the real signal

The option measures something correlated with the thing that matters instead of
the thing itself. Attractive because the proxy is easy to compute.

*Tells:* escalating on **sentiment**, on a **failed-call counter**, or via an
`issue × segment × product` rules engine; trusting **uncalibrated** self-reported
confidence as a safety mechanism; automating on **aggregate** accuracy when the
risk is per-field.
*Killer question:* what is the real signal here, and is this option measuring it
or something that merely tracks it?
*Examples:* § A bullets 3–4, § G last bullet.

### DF2 · A prompt where code belongs — or a rule table where judgment belongs

Both directions of the same error. The option makes the model *more likely* to
behave when the scenario demands it *always* behaves; or it hard-codes a rules
engine where the scenario needs natural-language judgment.

*Tells:* "add more few-shot examples", "write a more precise description", "use a
stronger model", "instruct it to double-check", "emphatic language", "return JSON
please".
*Killer question:* does the stem ask for a tendency or a guarantee? Watch for
**compliance, regulatory, must never, cannot rely on the model, 100%, guaranteed**.
*Examples:* § A bullets 1–2, § C fourth bullet.

### DF3 · Right mechanism, wrong layer

The mechanism is correct and you have seen it work — it is installed in the wrong
place. The hardest family, because nothing about the option is false.

*Tells:* MCP servers or env vars in `CLAUDE.md`; coding conventions in
`settings.json`; user-level config for something the team must share;
system-prompt routing where the **tool description** is the layer that decides
selection.
*Killer question:* which layer *owns* this concern — prompt, tool schema, rules
file, hook, or code?
*Examples:* § C first bullet, § H second bullet.

### DF4 · Solves a real problem this scenario does not have

Textbook-correct, and answers a question nobody asked. Attractive because you
recognise it as a Good Practice.

*Tells:* raising `max_tokens` for an *input* overflow; hourly batches against a
30-minute SLA; a bigger context window for mid-context degradation.
*Killer question:* point at the sentence in the stem this option serves. No
sentence, no option.
*Examples:* § E first bullet, § G bullets 2–3.

### DF5 · Complexity that does not pay

The option ignores a cost, latency, or simplicity constraint the stem stated out
loud, or reaches for infrastructure where a mechanism exists.

*Tells:* multi-agent system for a simple task (~15× tokens); a god agent with
15+ tools; vector store, message queue, or nested spawning where flat structured
reports suffice.
*Killer question:* what is the simplest mechanism that satisfies **every** stated
requirement?
*Examples:* § C second bullet, § D bullets 3 and 5.

### DF6 · Information thrown away, then reconstructed

Discards something cheap to keep and expensive to rebuild. Attractive because the
intermediate output looks tidier.

*Tells:* reconstructing citations post-hoc from logs or similarity; paraphrasing
verbose tool output; handing a human a raw transcript; lossy enum mapping; regex
over prose; homogenising every output into one format.
*Killer question:* what is irreversibly lost here, and does anything downstream
need it?
*Examples:* § D bullets 6–7, § F bullets 2 and 4, § G bullets 5–6.

### DF7 · Adds a component where the fix is to repair the contract

Bolts on a new part — or resets state — instead of fixing the thing that is
already wrong.

*Tells:* editing or deleting a `tool_result` from the middle of a transcript
(orphans its `tool_use`); a fresh start where resume + the delta would do;
re-reading every file when three changed; patching a synthesiser when the
upstream output contract is what broke.
*Killer question:* does this repair the existing contract, or paper over it with
another moving part?
*Examples:* § E bullets 3–4 and 8, § F first bullet.

---

## Stem tells — read these before the options

- **A quoted proposal** — "a team member suggests…", "the team is considering…" — is
  usually the trap. It is framed sympathetically so that agreeing feels collegial.
  Treat a quoted proposal as a hypothesis to test against the requirements, never as
  a premise you have to accept.
- **Qualifying clauses eliminate whole categories.** "The context window isn't
  full yet" kills every compaction answer. "This must never happen" kills every
  prompt answer. The disqualifier is often buried mid-sentence — the passers who
  fail report missing it, not misunderstanding the concept.
- **Multiple-response items state how many to select.** Read that line first;
  select exactly that many.
- **Under two minutes per item.** Flag and move; the second read is reliably
  easier than the first.

---

## Part 3 — The themed catalog

Grouped by mechanism. If an option matches one of these, it is almost certainly
wrong. Each bullet is an instance of one of the families in Part 2.

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

**Back to:** [README.md](README.md) · [00-golden-rules-cheatsheet.md](00-golden-rules-cheatsheet.md) · [08-quick-reference-tables.md](08-quick-reference-tables.md)
