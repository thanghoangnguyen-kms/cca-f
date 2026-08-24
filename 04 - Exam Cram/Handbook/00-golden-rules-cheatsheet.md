---
tags:
  - CCA-F
  - handbook
  - golden-rules
date: 2026-08-24
status: done
---

# 00 — Golden Rules Cheat Sheet (LEARN BY HEART)

This one page answers a large share of the exam. If you memorize nothing else,
memorize this.

---

## 🥇 The Golden Rule (prompt vs. code)

> **MUST always happen → CODE** (hook, gate, schema validation, deterministic
> routing, programmatic check).
> **Should USUALLY be right → PROMPT** (instructions, few-shot examples, tool
> descriptions).

Prompts change *probability*. Code changes *outcome*. Triggers that force the
**code** answer: "compliance", "cannot be left to model discretion",
"guaranteed", "100%", "regulatory", "must never".

---

## 🎯 Trigger → Answer decision table

| When the question says… | The answer is almost always… |
|---|---|
| "compliance / must always / cannot rely on the model" | Deterministic **hook / gate in code**, not a prompt |
| Agent picks the **wrong tool** | Improve **tool descriptions** first (architecture > prompt); if too many tools, **split into specialized agents** |
| Agent has **too many tools** (e.g. 15+) | Split into specialized subagents (**~4–5 tools per agent** — the exam answer; docs put measured degradation past **30–50** available tools) |
| "return JSON reliably / structured output" | Define a **tool input schema + `tool_choice`** to force it; then validate |
| Force a **specific tool first** | `tool_choice: {"type":"tool","name":"..."}` on turn 1, then back to `auto` |
| Loop **won't stop / stops too early** | Stop on `stop_reason == "end_turn"`; iteration cap is only a safety ceiling |
| Agent **"forgot" earlier turns** | You aren't re-sending the full `messages` array (**API is stateless**) |
| **Stale tool result** poisons answers on return visit | **New session + structured summary + fresh tool calls** |
| Long session, answers **degrade / go generic** | **Compaction / scratchpad**, or spawn a **subagent** with a summary |
| Resume **a specific** past session | `--resume <name/id>` (`--continue` = most recent only) |
| Explore **two mutually-exclusive approaches** from one baseline | **`fork_session`** (one branch each) |
| Resume but some files changed | Resume **+ tell it the exact delta** (which files changed) |
| Subagent says **"no findings provided"** | Coordinator didn't put prior outputs **in the subagent's prompt** |
| Citations/sources get **lost between agents** | Keep **structured claim→source mappings** (separate from prose) |
| Give a subagent a rigid script that can't adapt | Delegate **goals + quality criteria, not procedures** |
| Escalation trigger design | **Clear NL criteria** for judgment calls; **deterministic code** for hard limits — never sentiment/failure-count alone |
| Tool errors handled inconsistently | Return **structured error metadata**: category + `isRetryable` + cause, with `isError=true` |
| Model **invents values** for missing fields | Instruct **return null when not stated** (ground to source) |
| Extraction **format/split/recall** inconsistent | **Diverse few-shot examples** (cover edge cases) |
| Source has **multiple valid values** (amendments) | Fix the **schema** (value + location + effective date), don't force one |
| Enum keeps failing on new values | Add **`"other"` + a detail string field** |
| Numbers don't reconcile (invoice totals) | Extract **computed AND stated**, flag mismatch — never auto-correct |
| Limited human review capacity | **Calibrated field-level confidence** routing (not random sampling) |
| "97% accurate in aggregate, safe to automate?" | First **disaggregate by segment/field** — aggregate hides failures |
| High volume, no tight latency | **Batch API** (50% cheaper, up to 24h window) |
| Tight latency / user waiting | **Synchronous Messages API** |
| Retry only failed batch items | Use **`custom_id`**; fix a context-length error (e.g. `context_length_exceeded` — exact string illustrative/unverified) by **chunking input** |
| Non-interactive / CI (GitHub Actions) | `claude -p --output-format json` (missing `-p` = hangs) |
| Unfamiliar large codebase | **Grep entry points → read → follow imports** incrementally (just-in-time) |
| Agent **reasons wrongly about data** it was given (calls a trend a contradiction, loses a citation) | Ask **what it wasn't told** — fix the **upstream output contract** (add dates, source metadata, units). Never bolt a compensating rule onto the consumer |
| "How does the loop decide the **next** tool call?" | The `tool_result` is appended to `messages`, the whole conversation is resent, and **the model reasons**. There is **no decision tree / router** inside the loop |
| Heterogeneous inputs + an option offering a **normalization layer** (calibrate to 0–1, common intermediate format, all-to-bullets) | **Normalize noise** (units, casing, date format), **never signal** (methodology, confidence *kind*, content type). Render each type natively; separate well-established from contested |
| Customer asks for **regulated professional advice** (legal / medical / tax / immigration) | Say plainly it is **outside what support can advise on**, then route to the right resource or a human — never a best-effort opinion |
| Customer **demands a human before anything is known** (no tools called yet) | Acknowledge, ask **one targeted question** to scope the issue, *then* escalate. Not a cold handoff, not silent investigation |
| Context pressure — is the fix **summarize** or **structure**? | **In-session, one conversation** → compact the inactive spans, keep the active issue verbatim. **Across a boundary** (human handoff, crash resume, another agent) → structured fields |

---

## 📐 Quick-reference micro-tables

### `tool_choice` modes
| Mode | Text reply allowed? | Must call a tool? | Must call a *specific* tool? |
|---|---|---|---|
| `auto` | Yes | No | No |
| `any` | No | Yes | No |
| `tool` | No | Yes | **Yes** |
| `none` | Yes | No | No tools available |

### Structured error categories
| Category | Meaning | Agent action |
|---|---|---|
| `transient` | timeout / rate limit | retry (backoff) |
| `validation` | bad input | fix input, retry |
| `business` | rule violated | explain to user, don't retry |
| `permission` | access denied | escalate / stop, don't retry |

### Session state
| Action | Use when |
|---|---|
| **Resume** | Continue the *same* task after interruption; keep context |
| **Fork** | Try a different direction from the same baseline; original preserved |
| **Fresh start** | Old context is noisy/wrong, or you need isolation |

### Batch SLA math
`worst case = (max wait before a doc is batched) + (up to 24h processing)`.
Leave a safety margin — don't land *exactly* on the SLA.
Example: SLA 30h → batch every **4h** (4 + 24 = 28h, 2h buffer) beats every 6h (=30h, no margin).

---

## 🚫 Top anti-patterns (instant wrong answers)

1. Using a **prompt to enforce** a mandatory/compliance rule (use a hook/code).
2. Using an **iteration cap** as the primary loop-stop (use `stop_reason`).
3. Fixing wrong-tool selection by **adding system-prompt routing** instead of
   rewriting the **tool description** / reducing tool count.
4. Giving **one agent too many tools** ("god agent").
5. **Homogenizing** all outputs to one format, destroying native structure.
6. **Reconstructing** citations/attribution *after the fact* (preserve mapping from the start).
7. Stuffing an **entire large codebase into one context** instead of multi-pass.
8. Relying on **model self-reported confidence** that isn't calibrated.
9. Automating on **aggregate accuracy** without checking per segment/field.
10. Making missing fields **required** (forces hallucination) instead of null.
11. Producing **best-effort regulated advice** (legal/medical/tax) instead of
    naming the scope limit and routing on.
12. Adding a **component** (calibration layer, conversion layer, classification
    step, extra context layer, post-processing normalizer) when the fix is to
    repair the existing **contract**. If two options differ mainly in that one
    adds infrastructure, the simpler one is usually right.

---

## 🧠 Meta-principles (architecture mindset)

- **Simplest thing that works.** Add agentic complexity only when it pays off
  (multi-agent ≈ 15× the tokens of a single chat).
- **Architecture before prompt.** Wrong tool? Reduce/split tools → then tweak
  descriptions → prompt last.
- **Structure survives; prose gets dropped.** Keep metadata (dates, sources,
  confidence) in structured fields, not inside free text.
- **Ground to the source; prefer "null / unknown" over a plausible guess.**
- **Validation finds known errors; evaluation + human review find new ones,
  drift, and unforeseen failure modes.** Monitor production continuously.
- **Ask clarifying questions** on ambiguous architecture problems before
  reaching for a specific pattern (CRDT/Kafka/microservices).
