---
tags:
  - CCA-F
  - practice-exam
  - answer-key
date: 2026-08-25
status: done
---

# CCA-F Question Bank — Answer Key

**60 worked answers** for certificationpractice.com practice exam #2564. Questions: [CCA-F-practice-exam-questions.md](CCA-F-practice-exam-questions.md) · index: [README.md](README.md) · blueprint: [[Official Exam Blueprint]]

> [!WARNING] This key is **vault-reasoned**, not grader-confirmed
> The source publishes questions without answers, so unlike the three CyberSkill keys there is **no `correct_key` behind any of these**. Every answer below is derived from the [[Official Exam Blueprint]], official Anthropic docs, and the vault's `D1`–`D5` notes, and cross-checked against the officially-authored [Exam Guide answer key](../Exam%20Guide%20-%20Sample%20Questions/Answer%20Key.md) wherever an item restates one of its principles.
>
> Confidence is high — 55 of the 60 restate a principle the vault already has a sourced answer for — but treat a disagreement between this key and a grader-authoritative one as **this key losing**, and log it in [[Weak Areas Deep Dive]].
>
> **Four items are flagged 🔶** where the bank's own wording is technically wrong even though the intended answer is clear: Q14, Q21, Q51, Q55. Read those rebuttals — the exam will not phrase them that way.

---

## Answer grid

| Q | A | Q | A | Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | **C** | 11 | **C** | 21 | **C** 🔶 | 31 | **C** | 41 | **D** | 51 | **C** 🔶 |
| 2 | **A** | 12 | **B** | 22 | **A** | 32 | **D** | 42 | **A** | 52 | **A** |
| 3 | **D** | 13 | **B** | 23 | **A** | 33 | **C** | 43 | **D** | 53 | **A** |
| 4 | **B** | 14 | **A** 🔶 | 24 | **D** | 34 | **D** | 44 | **D** | 54 | **A** |
| 5 | **C** | 15 | **A** | 25 | **B** | 35 | **D** | 45 | **B** | 55 | **D** 🔶 |
| 6 | **C** | 16 | **B** | 26 | **A** | 36 | **C** | 46 | **B** | 56 | **C** |
| 7 | **B** | 17 | **A** | 27 | **D** | 37 | **C** | 47 | **C** | 57 | **D** |
| 8 | **D** | 18 | **D** | 28 | **D** | 38 | **A** | 48 | **B** | 58 | **C** |
| 9 | **C** | 19 | **B** | 29 | **A** | 39 | **D** | 49 | **D** | 59 | **D** |
| 10 | **B** | 20 | **D** | 30 | **D** | 40 | **D** | 50 | **C** | 60 | **D** |

**Distribution:** A ×13 · B ×11 · C ×15 · D ×21.

> [!TIP] The bank's tell — and why you must not lean on it
> **D is the answer 35% of the time**, and on this bank the correct option is very often the **longest, most qualified one** (Q3, Q6, Q10, Q28, Q35, Q49). Q6's option C literally narrates both halves of its own justification. That is an artefact of *this* writer, not of the exam. The official items in [Exam Guide - Sample Questions](../Exam%20Guide%20-%20Sample%20Questions/README.md) key **A** five times out of twelve and keep option lengths even. Grade yourself on the reasoning, not the pattern.

## Coverage by domain

| Domain | Questions | Count |
|---|---|---|
| **D1** — Agentic architecture & orchestration | 3, 16, 20, 23, 28, 29, 30, 31, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48 | 19 |
| **D2** — Tool design & MCP integration | 5, 8, 22, 26, 27, 32, 33, 34, 35, 49 | 10 |
| **D3** — Claude Code configuration & workflows | 1, 9, 12, 37, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60 | 15 |
| **D4** — Prompt engineering & structured output | 2, 4, 6, 7, 13, 17, 19, 21, 36, 58 | 10 |
| **D5** — Context management & reliability | 10, 11, 14, 15, 18, 24, 25 | 7 |

D3 is **25% of this bank** — the exact material the three CyberSkill sittings barely touch. That is the single biggest reason to work this set: see [README.md](../README.md) § Gaps worth filling.

---

## Q1 — CI flags for schema-conforming output → **C**

**C.** `--output-format json` combined with `--json-schema`.

**Why C wins.** Two flags, two jobs: `--output-format json` puts a JSON envelope (result + session metadata) on stdout instead of prose; `--json-schema` constrains the payload inside it to a named structure. Neither alone gives a downstream script a shape it can rely on.

| Distractor | Why it fails |
|---|---|
| **A** `--output-format structured` / `--schema-file` | Both fabricated. `--output-format` takes `text`, `json`, or `stream-json` |
| **B** `--format json-strict` / `--validate-output` | Both fabricated |
| **D** `-p` + `--json-schema` only | `-p` only makes the run non-interactive. Without `--output-format json` you don't get the parseable envelope |

> [!WARNING] Exam trap — `--json-schema` takes inline JSON, not a path
> `--json-schema review-schema.json` does **not** load a file. Interpolate it yourself: `--json-schema "$(cat review-schema.json)"`.
> Source: <https://code.claude.com/docs/en/cli-reference>

**Takeaway.** Non-interactive = `-p`. Parseable = `--output-format json`. Shaped = `--json-schema`. See [[D3 - Claude Code Configuration & Workflows]] §3.6.

---

## Q2 — Correlating 15 batch failures back to source documents → **A**

**A.** Use the `custom_id` assigned to each request.

**Why A wins.** `custom_id` exists for exactly this. Batch results are **not returned in submission order**, so it is the only reliable correlation handle — and the documented remedy for partial failure is to resubmit only the failed `custom_id`s, never the whole batch.

| Distractor | Why it fails |
|---|---|
| **B** compare against the ordered list | Assumes ordering the API does not guarantee |
| **C** parse filenames out of error bodies | Error bodies carry error detail, not your prompt's document names |
| **D** query batch status for an ordered failed index | No such endpoint response — status gives counts and a results URL, not an index list |

**Takeaway.** Every batch request gets a `custom_id`, always, even when you think you won't need it. See [[D4 - Prompt Engineering & Structured Output]] § Message Batches API.

---

## Q3 — Subagent times out on 1 of 3 sources → **D**

**D.** Attempt local recovery; if unresolved, propagate structured error context **with partial results** to the coordinator.

**Why D wins.** Two obligations at once: recover what you can locally (retry the transient failure), and be honest upward about what you couldn't. Partial results plus a structured description of the gap lets the coordinator decide — retry elsewhere, proceed with a caveat, or escalate. That decision belongs to the coordinator, and it can only make it if the subagent tells the truth.

| Distractor | Why it fails |
|---|---|
| **A** report 3-of-3 success | Silently fabricates coverage. The coordinator synthesises a report that claims a source it never read |
| **B** terminate the whole workflow | Discards two successful retrievals over one transient timeout |
| **C** block the coordinator on a background retry | Background retry that *blocks* is a contradiction, and it stalls a pipeline that already has usable data |

**Takeaway.** Subagent failure contract: **recover locally, then surface structured error + partial results**. Never swallow, never over-escalate. See [[D1 - Agentic Architecture & Orchestration]] §1.6 · pairs with **Q38**.

---

## Q4 — "Only report high-confidence findings" didn't help → **B**

**B.** The instruction defines no categories to report or skip, so the model has no actionable filtering criteria.

**Why B wins.** "High-confidence" is a *self-assessment* word, not a *criterion*. The model has no calibrated notion of its own confidence to threshold against, so the instruction reduces to "be good" — which changes nothing. What works is naming the decision boundary in terms the model can evaluate against the code in front of it.

| Distractor | Why it fails |
|---|---|
| **A** conflicts with `tool_choice` | `tool_choice` governs *whether/which tool* is called, not what a review reports |
| **C** non-interactive mode blocks system prompts | `-p` changes I/O, not prompt processing |
| **D** can't calibrate without labeled training data | Directionally gestures at the real issue but states it as a hard incapacity; the fix is better criteria, not training data |

**Takeaway.** Vague quality adjectives ("high-confidence", "be conservative", "prioritize precision") are non-instructions. **Q13** is the same failure with the fix as the answer — read them as a pair. See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q5 — Jira + a proprietary approval system → **C**

**C.** Community MCP server for Jira; custom MCP server only for the proprietary workflow.

**Why C wins.** The build-vs-adopt rule is decided by **whether the integration is standard**. Jira is a mainstream product with maintained community servers — rebuilding it buys nothing and costs maintenance forever. The internal approval system exists nowhere but your org, so nothing can be adopted for it. "Consistency" is not a reason to write code someone else already maintains.

| Distractor | Why it fails |
|---|---|
| **A** bend the proprietary API to fit a community server | Contorts a unique system into a shape it doesn't have — fragile and misleading |
| **B** build both | The stated rationale ("consistent patterns") is precisely the trap |
| **D** one server for both | Couples an adoptable integration to a bespoke one; you now maintain the Jira half too |

**Takeaway.** Standard system → community server. Proprietary system → custom server. Mixed estate → **mixed answer**. See [[D2 - Tool Design & MCP Integration]] §2.4.

---

## Q6 — Vendor dates still arriving unnormalized → **C**

**C.** Explicit normalization rules in the prompt **and** ISO 8601 enforced in the output/tool schema.

**Why C wins.** Schema and prompt do different jobs. The schema states the *shape* of the destination; it does not teach the model that "15 March 2024" and "2024.03.15" are the same date. The prompt supplies the conversion rules; it cannot validate the result. Varied international input needs both — instruct the transformation, then validate it.

| Distractor | Why it fails |
|---|---|
| **A** regex constraint on the field | Still only validation. It rejects bad output without ever telling the model how to produce good output |
| **B** a schema per vendor | Doesn't scale, and breaks on the first vendor whose format you haven't seen |
| **D** validation-retry loop only | A legitimate reliability layer, but retrying an unchanged prompt just re-rolls the same dice. Fix the prompt first |

**Takeaway.** Schema constrains, prompt instructs — normalization needs both. See [[D4 - Prompt Engineering & Structured Output]] §4.3.

---

## Q7 — Strongest guarantee of valid JSON → **B**

**B.** Define the structure as a tool's JSON schema and read the `tool_use` response.

**Why B wins.** Tool use is the only option here where valid JSON is a **property of the API path**, not an outcome you hope for and then check. The model emits a structured `tool_use` content block whose `input` is already parsed — there is no text stage in which a bracket can go missing.

| Distractor | Why it fails |
|---|---|
| **A** few-shot JSON examples | Raises the odds, guarantees nothing — still freeform text generation |
| **C** parse-and-correct loop | Detects syntax errors after the fact; adds latency and cost, and can still fail to converge |
| **D** template in the system prompt | Weakest of all — instruction only |

**Takeaway.** "Strongest guarantee" of structure = **tool use / structured outputs**, never prompting. See [[D4 - Prompt Engineering & Structured Output]] §4.2.

---

## Q8 — Generic `fetch_url` reaching the open internet → **D**

**D.** Replace it with `load_internal_docs`, which validates URLs against the internal documentation domain **before** the request.

**Why D wins.** This is a **tool design** problem, not a prompting one. A tool that *can* reach the whole internet will occasionally be used to reach the whole internet. Narrowing the tool's capability makes the misuse structurally impossible rather than merely discouraged — and the validation runs pre-request, so nothing leaves the network.

| Distractor | Why it fails |
|---|---|
| **A** `PostToolUse` hook discarding off-domain content | Too late — the request already went out. It filters what the model *sees*, not what the system *does* |
| **B** system prompt instruction | Probabilistic; the stem already establishes the agent drifts |
| **C** force `fetch_url` every turn | Makes it worse — now it must call the over-broad tool constantly |

**Takeaway.** Scope capability at the **tool boundary**, not with instructions. Compare **Q27** (hook) — a hook is right when the tool must stay general and the rule is conditional on a *parameter*; a narrower tool is right when the whole capability is over-broad. See [[D2 - Tool Design & MCP Integration]] §2.2.

---

## Q9 — Exploration going vague after many turns → **C**

**C.** Maintain a scratchpad file of class names, method signatures, and file paths, and reference it.

**Why C wins.** The tell is **degrading from specific to generic about material it previously handled correctly** — that is context exhaustion. A scratchpad moves those findings **out of the context window into durable storage**, so they survive compaction and can be re-read on demand at a fraction of the token cost.

| Distractor | Why it fails |
|---|---|
| **A** restart from scratch | Throws away every finding — the exact thing you're trying to preserve |
| **B** `/compact` repeatedly | Compaction is lossy by design. Repeated compaction of already-compacted context degrades the specifics further |
| **D** raise `max_tokens` | `max_tokens` caps the **response** length; it has nothing to do with how much context is retained |

**Takeaway.** Specific-becomes-generic ⇒ context exhausted ⇒ **externalize findings** (scratchpad, summary, fresh subagent). Confirmed by the Timed Mock's Q40 and Q58. See [[D5 - Context Management & Reliability]] §5.1.

---

## Q10 — Inconsistent escalation, vague instructions didn't fix it → **B**

**B.** Explicit escalation criteria **plus** few-shot examples showing escalate-versus-resolve.

**Why B wins.** Near-verbatim the officially-keyed answer to the equivalent item in the exam guide. Root cause is an **unclear decision boundary**; criteria draw the line and examples calibrate the edge cases. It is also the proportionate first move — prompt-layer, before any infrastructure.

| Distractor | Why it fails |
|---|---|
| **A** sentiment threshold | **Sentiment does not correlate with case complexity.** An angry customer with a simple problem escalates; a polite one with a policy edge case doesn't |
| **C** force 3 turns before escalating | Makes the genuinely-should-escalate cases worse, and still doesn't define *when* |
| **D** self-reported confidence < 60% | **LLM self-reported confidence is poorly calibrated.** The stem says the agent is already inconsistent — you'd be thresholding a broken signal |

**Takeaway.** D is the trap for anyone who half-remembers confidence-based routing (task statement 5.5) — but there, confidence is **calibrated against a labeled validation set** (see **Q25**), not self-reported and trusted raw. See [[D5 - Context Management & Reliability]] §5.2 · official [Answer Key.md](../Exam%20Guide%20-%20Sample%20Questions/Answer%20Key.md) Q3.

---

## Q11 — 40-field tool output, quality declining → **C**

**C.** Trim the tool output to the return-relevant fields before appending it to context.

**Why C wins.** Attack the problem where it's created. 35 of 40 fields are noise the agent never needs; letting them into the conversation costs tokens on **every subsequent turn** and dilutes attention. Filtering at the boundary is cheap, deterministic, and loses nothing.

| Distractor | Why it fails |
|---|---|
| **A** progressive summarization | Treats the symptom. It compresses garbage you should never have stored — and summarization is lossy |
| **B** cap lookups at three | Caps the *feature*, not the waste. The agent now fails at legitimate work |
| **D** bigger context window | Buys time, not a fix — irrelevant content still degrades attention, and cost scales with it |

**Takeaway.** **Filter tool output at the boundary before it enters context.** Summarize what you had to keep; don't keep what you never needed. See [[D5 - Context Management & Reliability]] §5.3.

---

## Q12 — CI job hangs and times out with no output → **B**

**B.** Claude Code is waiting for interactive input because `-p` was not included.

**Why B wins.** The signature of a missing `-p` is exactly this: **hangs, no output, eventually killed by the job timeout**. Without `--print`, Claude Code starts its interactive REPL, which in a CI runner has no TTY to read from and nothing to answer it. Nothing is produced because nothing ever ran.

| Distractor | Why it fails |
|---|---|
| **A** context window exceeded | Produces an **error**, quickly — not an indefinite hang |
| **C** missing `--output-format` | Defaults to `text`; you'd get prose on stdout, not silence |
| **D** missing `CLAUDE.md` criteria | Missing memory files never cause a loop; Claude proceeds with what it has |

**Takeaway.** Every non-interactive invocation starts `claude -p`. Hang + zero output in CI ⇒ suspect `-p` first. See [[D3 - Claude Code Configuration & Workflows]] §3.6.

---

## Q13 — Reducing false positives on comment-accuracy checks → **B**

**B.** "Flag comments only when the described behavior **directly contradicts** the actual code logic."

**Why B wins.** It converts a vague quality preference into a **testable condition** the model can evaluate against the artifact in front of it: does the comment's claim contradict the code, yes or no? Everything ambiguous now falls outside the criterion by construction.

| Distractor | Why it fails |
|---|---|
| **A** "only high-confidence findings" | The exact instruction **Q4** establishes as ineffective |
| **C** "be conservative, avoid minor issues" | "Minor" is undefined — same non-instruction in different words |
| **D** "prioritize precision over recall" | Names the *goal*, not the *rule*. The model still has to invent the boundary itself |

> [!IMPORTANT] Q4 and Q13 are one lesson split in two
> Q4 asks **why the vague instruction failed**; Q13 asks **what to write instead**. A, C, and D here are all the Q4 failure mode wearing different clothes — spotting that collapses this into a one-option question.

**Takeaway.** Replace adjectives with conditions. See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q14 — 🔶 Prioritizing findings for scarce reviewer bandwidth → **A**

**A.** A verification pass producing a confidence score per finding, then confidence thresholds routing to human reviewers.

**Why A wins.** It's the only option that ranks findings by **likely correctness**. The other three rank by proxies that don't track actionability at all — location, category, or size. Confidence-based routing to human review is a named technique in the blueprint (task statement 5.5), and a **separate verification pass** is meaningfully stronger than asking for a confidence number inline: the reviewing instance re-examines the finding rather than rubber-stamping its own output (see **Q20**).

| Distractor | Why it fails |
|---|---|
| **B** filter by security-critical directory | Path is a poor proxy for actionability — high-value findings outside those paths are dropped entirely |
| **C** one example per category | Reduces *volume*, not *noise*. You lose 90% of real findings to save reviewer time |
| **D** rank by lines of code touched | Surface area has no relationship to whether a finding is correct or worth acting on |

> [!WARNING] 🔶 The bank's phrasing is loose here — and the exam's will not be
> A says the model **self-reports** a confidence score. Raw self-reported confidence is **poorly calibrated** — which is exactly why **Q10-D** is wrong and **Q25-B** is right. A survives only because the other three options are worse proxies, and because it wraps the score in a *verification pass* and a *threshold*.
> The complete answer, and the one the official items key, is **calibrate the thresholds against a labeled validation set** before trusting them. If an exam item offers both self-reported and calibrated confidence, **calibrated wins every time.**

**Takeaway.** Confidence-based routing is right; *uncalibrated* confidence is not. See [[D5 - Context Management & Reliability]] §5.5 · pairs with **Q25**.

---

## Q15 — 40% dismissal rate, no idea why → **A**

**A.** A `detected_pattern` field naming the code construct that triggered the finding.

**Why A wins.** The question the team cannot answer is *"which types of code constructs trigger dismissals?"* — so the field you add must carry **the construct**. With `detected_pattern` on every finding, dismissals group by pattern and the systemic false-positive sources fall out of a `GROUP BY`.

| Distractor | Why it fails |
|---|---|
| **B** `review_instance_id` | Traces a finding to a *session*. Useful for debugging one run; tells you nothing about construct types |
| **C** `confidence_score` | Lets you filter by the model's self-assessment, which the stem never asked about — and which is uncalibrated anyway |
| **D** `timestamp` | Correlates dismissals with time of day. Answers a question nobody asked |

**Takeaway.** Design the structured field to answer **the analysis question you actually have**. See [[D4 - Prompt Engineering & Structured Output]] §4.2.

---

## Q16 — Synthesis subagent doing its own searches → **B**

**B.** Restrict its `allowedTools` to synthesis-relevant tools, removing search and retrieval.

**Why B wins.** A subagent given 18 tools will use 18 tools. **Tool access is the enforcement layer for role boundaries** — remove the capability and the off-task behavior becomes impossible, deterministically, rather than merely discouraged. This is the same principle as **Q8**, applied to a subagent instead of a tool.

| Distractor | Why it fails |
|---|---|
| **A** prompt it not to search | Probabilistic. The tools are still right there, and the stem says instructions aren't holding |
| **C** more context so it has "less reason" | Guesses at motive. Also enlarges the context of the agent whose job is to *compress* |
| **D** `tool_choice: "any"` | Forces a tool call — including a search call. Actively worsens the problem |

**Takeaway.** Scope every subagent to the **minimum tool set its role requires**. See [[D1 - Agentic Architecture & Orchestration]] §1.2.

---

## Q17 — Line items not summing to the stated total → **A**

**A.** Extract both `calculated_total` (sum of line items) and `stated_total` (the printed total), then compare in code.

**Why A wins.** It makes the discrepancy **mechanically detectable**. Both numbers exist in the document, both get extracted, and the comparison happens in deterministic code — not in the model's arithmetic and not in its self-assessment. A mismatch is now a hard signal you can route on.

| Distractor | Why it fails |
|---|---|
| **B** `total_verified` boolean | Asks the model to grade its own arithmetic. Self-verification in the same pass is exactly the failure in **Q20** |
| **C** confidence score < 0.9 | Uncalibrated self-report, and confidence about a *number* says nothing about whether it *sums* |
| **D** extract twice and average | Averages two possibly-wrong values into a definitely-wrong one, and destroys the evidence of disagreement |

**Takeaway.** Extract the **inputs to the check**, then do the check in code. Never ask the model to certify itself. See [[D4 - Prompt Engineering & Structured Output]] §4.2.

---

## Q18 — Refunds applied to the wrong order in multi-issue sessions → **D**

**D.** Extract structured issue data (order IDs, amounts, statuses) into a **persistent case facts block** included in each prompt.

**Why D wins.** Cross-contamination between issues is a **context management** failure: the details are buried in a long conversational history where the model must re-derive which order goes with which complaint. A structured facts block re-presents the authoritative state on every turn, unambiguously keyed by issue, so there is nothing to conflate.

| Distractor | Why it fails |
|---|---|
| **A** "carefully track all details" | An instruction cannot fix a retrieval problem |
| **B** summarize every five turns | Summarization is **lossy** — it is likelier to blur order details than to sharpen them |
| **C** cap sessions at two issues | Degrades the product to dodge the bug, and two issues can still be swapped |

**Takeaway.** For facts that must stay exact across many turns, **externalize into structured state re-injected each turn**, don't rely on conversational recall. See [[D5 - Context Management & Reliability]] §5.4.

---

## Q19 — Few-shot examples generalizing to an unseen language → **B**

**B.** The examples taught the underlying **judgment criteria**, which generalized to a structurally similar pattern.

**Why B wins.** This is what few-shot prompting actually does: well-chosen examples convey a *decision principle*, not a lookup table. "Defensive null check versus genuine error-handling bug" is a language-independent distinction, so it transfers to any syntax expressing the same structure.

| Distractor | Why it fails |
|---|---|
| **A** recalled language-specific rules from pre-training | Then the four examples were irrelevant — which contradicts the stem's setup |
| **C** flagged it because it was unfamiliar | Would predict indiscriminate flagging of everything novel; the stem says it flagged the *problematic* pattern correctly |
| **D** decomposed into exact sub-pattern matches | Describes template matching. If that were the mechanism, a new language would break it |

**Takeaway.** Choose few-shot examples for the **principle** they demonstrate, not the surface they're written on. See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q20 — Same-session self-review missed the bug → **D**

**D.** The model retains its reasoning context from generation, making it less likely to question its own prior decisions in the same session.

**Why D wins.** The reasoning that produced the bug is still in context, and it reads as *justified*. Reviewing under that framing means re-deriving the same conclusion. The fix is architectural: a **fresh instance with no generation history**, given only the code and the review criteria.

| Distractor | Why it fails |
|---|---|
| **A** defaults to positive to stay agreeable | Anthropomorphizes a mechanism that is really context contamination |
| **B** review prompt lacked recursion criteria | Would improve the review, but doesn't explain why *this* reviewer was blind — a fresh instance with the same weak prompt does better |
| **C** context window exhausted | Nothing in the stem suggests exhaustion, and the symptom would be vagueness (see **Q9**), not confident approval |

**Takeaway.** **Multi-instance review** — generate in one context, review in a separate one. See [[D4 - Prompt Engineering & Structured Output]] § Multi-instance review · [[D5 - Context Management & Reliability]] §5.5.

---

## Q21 — 🔶 Batch API limitation forcing a redesign → **C**

**C.** No multi-turn tool calling within a single request — you can't execute a tool and feed its result back mid-request for a second call.

**Why C wins by elimination.** The pipeline's shape is `extract_metadata` → *read the result* → `extract_fields` with a type-specific schema. That round-trip requires **your code** to run between two model turns, which no single API request accommodates. The redesign is two batch passes: batch all 500 metadata calls, group by type, then submit a second batch per type. A, B, and D are all factually false.

| Distractor | Why it fails |
|---|---|
| **A** one system prompt/tool set per batch | False — **every request in a batch carries its own full parameters**, including system prompt and tools |
| **B** `tool_choice` limited to `auto` | False — forced tool selection works in batch |
| **D** 100 requests per batch | False — the limit is **100,000 requests or 256 MB** per batch |

> [!WARNING] 🔶 C is right about the workflow and wrong about the cause
> The Message Batches API **does** support multi-turn conversations and server-side tool use — it runs the same agentic loop as a synchronous `messages.create`. What you cannot do is an interactive **client-executed** tool round-trip inside one request, and that is true of *any* single Messages API call, sync or batch. It is not a batch-specific limitation.
> The genuine batch constraints worth memorizing: **asynchronous, up to 24 h, results `expired` after that and must be resubmitted, no ordering guarantee, correlate with `custom_id`, ~50% cost saving.**
> Source: <https://platform.claude.com/docs/en/build-with-claude/batch-processing> · checked 2026-08-24

**Takeaway.** Anything needing your code between two model turns must be split into **separate batch passes**. See [[D4 - Prompt Engineering & Structured Output]] § Message Batches API · pairs with **Q2**.

---

## Q22 — Agents burning tokens on exploratory discovery calls → **A**

**A.** Expose a content catalog as an **MCP resource** — a browsable hierarchy of topics and article summaries.

**Why A wins.** This is the resources-versus-tools distinction. **Tools are for actions the model chooses to invoke; resources are context the model can read.** Discovery is a read, so publishing the catalog as a resource replaces N exploratory tool calls with one cheap lookup — and the agent's actual searches become targeted from the first call.

| Distractor | Why it fails |
|---|---|
| **B** cache exploratory results | Makes repeated exploration cheaper without stopping it. Agents still issue the calls, and the first agent always pays |
| **C** raise `max_tokens` | Accommodates the waste instead of removing it. `max_tokens` is a response cap anyway |
| **D** preload all summaries into every system prompt | Thousands of articles into every invocation — the worst possible token profile, and most of it irrelevant to any given agent |

**Takeaway.** Discovery/browsing → **MCP resource**. Action/mutation → **MCP tool**. See [[D2 - Tool Design & MCP Integration]] §2.3.

---

## Q23 — Two credible sources, conflicting market-size figures → **A**

**A.** Present both with full attribution, in a section that explicitly distinguishes **contested** findings from well-established ones, preserving each source's methodological context.

**Why A wins.** The conflict is itself a finding. A research synthesis that hides disagreement is less useful than one that surfaces it — the reader needs to know the number is contested and *why the sources differ* (different methodologies, scopes, or dates) in order to use it.

| Distractor | Why it fails |
|---|---|
| **B** average them | Manufactures a figure **no source supports** and that no methodology backs |
| **C** omit the data point | Destroys information over a resolvable disagreement; the reader loses the range entirely |
| **D** pick the more recent/authoritative | An editorial judgment the synthesis agent isn't positioned to make, and it silently discards a credible finding |

**Takeaway.** Synthesis **preserves and labels** conflict; it doesn't resolve, average, or bury it. See [[D1 - Agentic Architecture & Orchestration]] §1.5.

---

## Q24 — "I would like to speak with a real person, please" → **D**

**D.** Acknowledge the request and **immediately escalate**.

**Why D wins.** An explicit request for a human is an **unconditional escalation trigger**. Agent capability is irrelevant — the customer has stated what they want, and overriding that to demonstrate efficiency damages trust far more than a transfer costs.

| Distractor | Why it fails |
|---|---|
| **A** explain the agent is faster | Argues with the customer about their own request |
| **B** ask them to describe the issue first | A delay tactic dressed as helpfulness — the ask was already unambiguous |
| **C** investigate silently, then escalate | Better handoff context, but it makes the customer wait for something they explicitly declined, and "silently" means they can't tell anything is happening |

> [!TIP] Not every escalation is a judgment call
> Most escalation items test *criteria* (**Q10**). This one tests **the one case with no criteria at all** — the customer asked. Same for legal threats and self-harm disclosure: escalate immediately, no assessment step.

**Takeaway.** Explicit human request → escalate now. See [[D5 - Context Management & Reliability]] §5.2.

---

## Q25 — Confidence scores that don't track accuracy → **B**

**B.** Calibrate the thresholds against a **labeled validation set** of known-correct extractions.

**Why B wins.** The stem describes the textbook symptom of **miscalibration**: high-confidence errors and low-confidence correct answers. The signal isn't useless, it's just not aligned with reality — and the only way to align a score with actual accuracy is to measure it against ground truth and set the thresholds from that measurement.

| Distractor | Why it fails |
|---|---|
| **A** lower the threshold so nearly everything is reviewed | Abandons routing entirely and returns to full manual review |
| **C** drop confidence, route by document type | Substitutes a coarser proxy; error-prone types still contain correct extractions and clean types still contain errors |
| **D** one document-level score | Coarser again, and loses the field-level granularity that made targeted review possible |

**Takeaway.** Uncalibrated confidence is not a routing signal. **Calibrate against labeled data, then threshold.** See [[D5 - Context Management & Reliability]] §5.5 · pairs with **Q10** and **Q14**.

---

## Q26 — Unix timestamps from one MCP tool, ISO 8601 from another → **A**

**A.** A `PostToolUse` hook that normalizes both into a consistent representation before the agent sees them.

**Why A wins.** `PostToolUse` fires **after the tool returns and before the result enters the agent's context** — precisely the seam where format normalization belongs. It is deterministic, applies to every call, and needs no cooperation from either MCP server or from the model.

| Distractor | Why it fails |
|---|---|
| **B** change each MCP server's implementation | Often impossible (third-party servers), and it forces every other consumer of those servers to absorb your format choice |
| **C** tell the agent to convert mentally | Probabilistic reasoning where deterministic transformation is available — the stem already shows it failing |
| **D** few-shot examples of both formats | Same problem as C, with token cost added |

**Takeaway.** Reshaping tool output before the model sees it is a **hook** job. `PostToolUse` = the normalization seam. See [[D2 - Tool Design & MCP Integration]] §2.5 · [[D1 - Agentic Architecture & Orchestration]] §1.5.

---

## Q27 — Refunds over $500 must never bypass a supervisor → **D**

**D.** A tool-call interception hook that inspects `amount`, blocks calls above $500, and redirects to `handoff_to_human`.

**Why D wins.** "Guarantees this business rule **without exception**" rules out every probabilistic mechanism in one stroke. A `PreToolUse` hook runs in code, sees the actual parameter, and can refuse the call — and it does the second half of the job too by routing the blocked request to the approval path instead of just failing.

| Distractor | Why it fails |
|---|---|
| **A** three few-shot examples | Shifts likelihood. Non-zero failure rate |
| **B** JSON schema `maximum: 500` on `amount` | **The strongest trap.** Schema constraints guide generation and can be validated — but they don't reliably *stop* the call, they give no supervisor-approval path, and a legitimate $800 refund now has no representable route at all |
| **C** bold "NEVER" in the system prompt | Emphasis is not enforcement |

> [!IMPORTANT] The golden rule, in one line
> *Must always happen* → **code** (hook, prerequisite gate, interception). *Should usually happen* → **prompt**. The words "guarantees", "without exception", "must never" in a stem are the signal to reach for the programmatic answer.

**Takeaway.** Financial thresholds are enforced by hooks, never by prompts or schemas. See [[D1 - Agentic Architecture & Orchestration]] §1.4 · [[00-golden-rules-cheatsheet]].

---

## Q28 — Vague multi-concern complaint, scope unknown up front → **D**

**D.** Dynamic adaptive decomposition — investigate each concern based on what each step reveals, generating follow-up subtasks as findings emerge.

**Why D wins.** The stem's decisive clause is *"the scope of each concern is unclear until initial lookups are performed."* You cannot plan a pipeline for work whose shape you learn only by doing it. Adaptive decomposition is the pattern for open-ended, discovery-driven requests — an unauthorized-access finding might expand into a security review, or collapse into a duplicate charge.

| Distractor | Why it fails |
|---|---|
| **A** one comprehensive prompt | Two unrelated investigations at once, with no room to follow either where it leads |
| **B** fixed sequential pipeline | Pre-commits to `issue_refund` on any discrepancy — including one that turns out to be fraud on a compromised account |
| **C** keyword decision tree | Brittle by construction; the stem's complaint is deliberately vague, which is exactly what keyword matching handles worst |

> [!TIP] The discriminator between fixed and adaptive
> **Known scope, stable steps → fixed pipeline** (and it's the better answer when offered, because it's cheaper and auditable). **Scope discovered during execution → adaptive.** The stem always tells you which; here it says so explicitly.

**Takeaway.** Unknown scope ⇒ adaptive decomposition. See [[D1 - Agentic Architecture & Orchestration]] §1.1.

---

## Q29 — Auditing 50 tickets individually, then finding systemic patterns → **A**

**A.** Per-ticket analysis passes, then a **separate cross-ticket synthesis pass**.

**Why A wins.** The stem names two distinct jobs — evaluate each ticket *individually*, then identify patterns *across the set* — and they need different context. Per-ticket passes give each ticket full attention with no cross-contamination; the synthesis pass then reasons over 50 compact evaluations instead of 50 raw tickets. That's the map-reduce shape of prompt chaining.

| Distractor | Why it fails |
|---|---|
| **B** all 50 in one prompt | Attention is spread thin across a huge context; individual evaluation quality collapses, and early tickets get less scrutiny than late ones |
| **C** pairwise comparison | O(n²) comparisons, and pairwise similarity is not the same as a systemic pattern |
| **D** sample 5 and extrapolate | The stem requires evaluating **each** ticket. Extrapolation from 10% is not an audit |

**Takeaway.** Per-item analysis → aggregate synthesis. Two passes, two contexts. See [[D1 - Agentic Architecture & Orchestration]] §1.1 · [[D4 - Prompt Engineering & Structured Output]] § Prompt chaining.

---

## Q30 — Resuming yesterday's named session → **D**

**D.** `claude --resume refund-trace`

**Why D wins.** `--resume` takes a session ID **or a session name** and restores that session's full conversation history. The other three flags don't exist.

| Distractor | Why it fails |
|---|---|
| **A** `--load` | Not a Claude Code flag |
| **B** `--session … --continue` | `--continue` resumes the **most recent** session and takes no name; there is no `--session` flag |
| **C** `--fork` | **No standalone fork flag exists.** Forking is `--resume` (or SDK `resume`) **plus** `fork_session` / `forkSession: true` |

> [!WARNING] The `--continue` vs `--resume` split
> `--continue` = *the most recent* session, no argument. `--resume <id-or-name>` = *a specific* session. If the stem names the session, the answer is `--resume`.

**Takeaway.** Named session → `--resume <name>`. See [[D1 - Agentic Architecture & Orchestration]] §1.7 · [[D3 - Claude Code Configuration & Workflows]].

---

## Q31 — Two alternative strategies from one shared baseline → **C**

**C.** `fork_session` — two independent branches from the shared analysis baseline.

**Why C wins.** Forking is built for exactly this: branch from a completed baseline so each branch inherits the full analysis, explore divergently, and leave the original untouched. Two forks means the strategies never contaminate each other and neither run pays to redo the codebase analysis.

| Distractor | Why it fails |
|---|---|
| **A** two fresh sessions, re-run the analysis | Duplicates the expensive step twice, and the two "baselines" may not even match |
| **B** paste the transcript into new prompts | Manual, lossy, and loses the actual session state |
| **D** `--resume` + `/compact` between strategies | **Sequential, not independent** — strategy 2 inherits strategy 1's reasoning, and `/compact` is lossy compression, not isolation |

> [!WARNING] Exam trap — fork is not a standalone option
> Forking = `resume=<session_id>` **plus** `fork_session=True` (Python) / `resume: sessionId` + `forkSession: true` (TS). There is **no** bare `fork=session_id`. The fork gets a **new** session ID; the original's history is untouched.

**Takeaway.** Shared baseline + independent exploration ⇒ fork. Sequential exploration ⇒ resume. See [[D1 - Agentic Architecture & Orchestration]] §1.7.

---

## Q32 — One `manage_account` tool doing four unrelated jobs → **D**

**D.** Split it into `update_profile`, `reset_password`, `change_subscription`, `deactivate_account`, each with a clear input/output contract.

**Why D wins.** The model conflates the operations because they are *presented as one operation*. Four distinct tools give four distinct descriptions, four distinct parameter sets, and no room to send password-reset parameters to a subscription change. Each tool should do **one thing**, with a schema that admits only that thing's inputs.

| Distractor | Why it fails |
|---|---|
| **A** force the call + add an `operation_type` enum | Keeps the overloaded surface and *forces* it to be used every turn. The parameter confusion remains |
| **B** one comprehensive description | A description listing four operations and four parameter sets is a manual, not a tool contract — and the model must still pick correctly from prose |
| **C** `PostToolUse` validation + retry | Detects wrong operations after they execute. On `deactivate_account` that is far too late |

> [!IMPORTANT] Q32 vs Q34 — same domain, opposite fix
> **Q34**: two well-scoped tools with **thin descriptions** → *expand the descriptions*. **Q32**: one tool with **too many responsibilities** → *split the tool*. Diagnose which problem the stem describes before picking. Writing a better description for `manage_account` cannot fix a tool that shouldn't exist in that shape.

**Takeaway.** One tool, one responsibility. See [[D2 - Tool Design & MCP Integration]] §2.1.

---

## Q33 — Every mention of "refund" triggers `handoff_to_human` → **C**

**C.** The keyword-sensitive system prompt instruction creates an unintended association that overrides the tool descriptions; revise it to state the precise conditions requiring human involvement.

**Why C wins.** The stem hands you the cause: *"When a customer mentions a refund, always ensure a human is involved."* That's a **keyword trigger**, and the system prompt outranks tool descriptions. The tools are fine — the instruction is over-broad. Rewrite it around the real condition (amount thresholds, policy exceptions, prior escalation), not around a word.

| Distractor | Why it fails |
|---|---|
| **A** strengthen `issue_refund`'s description | Fights the system prompt from a weaker position. The stem already says the descriptions are clear |
| **B** force `issue_refund` on refund keywords | Replaces one keyword rule with the opposite keyword rule — now genuinely-escalation-worthy cases get auto-refunded |
| **D** rename both tools | The names aren't the problem; nothing in the stem suggests confusion between them |

**Takeaway.** When behavior contradicts good tool descriptions, **read the system prompt** — it wins, and over-broad instructions there are a common root cause. See [[D2 - Tool Design & MCP Integration]] §2.1 · [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q34 — Thin one-line descriptions, agent alternates between two tools → **D**

**D.** Tool descriptions are the **primary mechanism** the model uses for tool selection, and minimal descriptions don't differentiate them.

**Why D wins.** "Fetches customer data" and "Gets order information" give the model nothing to decide between when the question is "tell me about my recent purchase" — both plausibly apply. Selection is driven by descriptions, so under-specified descriptions produce exactly the observed inconsistency. The fix is descriptions carrying input formats, example queries, edge cases, and **boundaries versus similar tools**.

| Distractor | Why it fails |
|---|---|
| **A** needs forced `tool_choice` to distinguish tools | False. Forcing selection also removes the model's ability to choose, which is the point of having two tools |
| **B** context exhausted, can't load both definitions | Fabricated failure mode; two tool definitions are trivially small |
| **C** names too similar in length | Nonsense — length isn't a selection signal |

**Takeaway.** Misrouting between well-scoped tools ⇒ **expand the descriptions first.** Officially keyed, verbatim, in the exam guide's Q2. See [[D2 - Tool Design & MCP Integration]] §2.1 · pairs with **Q32**.

---

## Q35 — Why structured error metadata matters for two failure kinds → **D**

**D.** Without metadata distinguishing transient errors from business-rule violations, the agent can't tell whether to **retry** or **explain the policy** — producing wasted retries or poor communication.

**Why D wins.** The two failures demand opposite responses. A database timeout should be retried and never mentioned to the customer. A non-returnable-item violation must **never** be retried — the answer will not change — and must be explained. `"Operation failed"` collapses both into one indistinguishable string, so the agent guesses.

| Distractor | Why it fails |
|---|---|
| **A** reduces token usage | A side effect at best, and not why it matters |
| **B** only for logging/observability | Backwards — structured errors change **agent behavior**, which is the whole point |
| **C** MCP requires an error category | False. It's a design best practice, not a protocol requirement |

**Takeaway.** Error metadata exists so the agent can pick a **recovery strategy**: `isRetryable` decides retry-versus-explain. See [[D2 - Tool Design & MCP Integration]] §2.6 · pairs with **Q49** and **Q38**.

---

## Q36 — Fabricated square footage from "spacious" → **C**

**C.** Few-shot examples showing `null` for informal descriptions, alongside examples correctly extracting explicit measurements.

**Why C wins.** The stem sets two requirements — stop fabricating **and** keep extracting correctly when a number is present — so the answer must demonstrate **both branches**. Few-shot examples do exactly that: they teach the model that "no measurable value present" is a legitimate, expected output rather than a failure to avoid.

| Distractor | Why it fails |
|---|---|
| **A** make the field required + reject non-numeric | **Required makes it worse** — a mandatory numeric field *compels* the model to invent one. And a fabricated number passes a non-numeric check |
| **B** "only extract values you are certain about" | The Q4/Q13 non-instruction again |
| **D** remove the field | Sacrifices the second requirement entirely; listings with real measurements now lose them |

> [!IMPORTANT] Make the "absent" case representable
> Hallucination in extraction is usually a **schema** problem: the model has no legal way to say *"not present."* Allow `null`, mark the field optional, and show an example of the null case. A required field with no escape hatch is an instruction to fabricate.

**Takeaway.** Optional/nullable field + few-shot showing the null case. See [[D4 - Prompt Engineering & Structured Output]] §4.3.

---

## Q37 — Finding `YYYYMMDD_description.sql` across a monorepo → **C**

**C.** `Glob`, with a pattern like `**/*_*.sql`.

**Why C wins.** The task is defined entirely by **filename and path**, not by content. `Glob` matches paths by pattern, recurses through arbitrary nesting with `**`, and returns the file list directly — one call, no content read.

| Distractor | Why it fails |
|---|---|
| **A** `Grep` for SQL keywords | Searches **contents**. Misses migrations without those keywords and matches non-migration SQL that has them |
| **B** `Bash` with a custom script | Works, but reinvents `Glob` — and the built-in tool is the documented choice for path patterns |
| **D** `Read` each directory | `Read` reads files, not directories, and would be absurdly expensive besides |

> [!TIP] The two-line rule for the built-in search tools
> **Know the path/name → `Glob`. Know the text inside → `Grep`. Know neither → `Explore` subagent.** `Bash` is the fallback for what none of them do.

**Takeaway.** Filename pattern ⇒ `Glob`. See [[D3 - Claude Code Configuration & Workflows]] § Built-in tools.

---

## Q38 — Subagent returns "search unavailable" to the coordinator → **A**

**A.** The coordinator can't distinguish a transient timeout from a permanent access restriction, so it can't choose an appropriate recovery strategy.

**Why A wins.** The two conditions call for opposite responses — retry with backoff versus route around the source permanently — and a generic string supports neither decision. The coordinator's whole job is choosing recovery strategies, and it has been handed a message with no strategy-relevant information.

| Distractor | Why it fails |
|---|---|
| **B** terminates the whole workflow | Would be a *consequence of bad coordinator design*, not the problem the vague message creates |
| **C** all other subagents halt | Subagents are independent; one failure doesn't block the others |
| **D** forces infinite retry | Overstates it, and describes one possible bad outcome rather than the underlying defect |

**Takeaway.** Same principle as **Q35**, one level up: error responses exist to let the **caller** pick a recovery strategy. See [[D1 - Agentic Architecture & Orchestration]] §1.6 · pairs with **Q3**.

---

## Q39 — Terminating the loop on the phrase "research complete" → **D**

**D.** It parses **non-deterministic natural language** instead of using the reliable `stop_reason` field, so termination is inconsistent.

**Why D wins.** The API already publishes the model's intent in a structured, enumerated field. Substituting a substring search over prose introduces every failure mode of natural language — paraphrase, the phrase appearing mid-reasoning, capitalization, a different wording entirely — for zero benefit.

| Distractor | Why it fails |
|---|---|
| **A** prevents `tool_use` blocks | The check doesn't affect what the model generates |
| **B** forces the phrase before any tool use | Backwards; nothing compels output ordering |
| **C** API error — text can't be inspected mid-conversation | False. Response text is fully available each turn |

**Takeaway.** Loop control reads `stop_reason`. Never grep the prose. See [[D1 - Agentic Architecture & Orchestration]] §1.3.

---

## Q40 — What the agentic loop actually checks → **D**

**D.** `stop_reason` is `tool_use` when the model wants a tool, `end_turn` when it considers the task complete.

**Why D wins.** That's the mechanism. `tool_use` → execute the requested tool, append the result, call again. `end_turn` → the model is finished; present the response. **Q39** is the anti-pattern; this is the pattern.

| Distractor | Why it fails |
|---|---|
| **A** count tool calls to a maximum | A useful **safety cap** against runaway loops — but a backstop, never the termination *mechanism* |
| **B** a termination keyword in the system prompt | The **Q39** anti-pattern stated as if it were the design |
| **C** a boolean `continue` field in the JSON body | Fabricated — no such field exists in the response |

> [!IMPORTANT] `stop_reason` values worth knowing
> `end_turn` · `tool_use` · `max_tokens` · `stop_sequence` · `pause_turn` · `refusal` · `model_context_window_exceeded`. The two that drive the loop are `tool_use` and `end_turn`; `max_tokens` means your response was **truncated mid-generation**, not that the task finished.

**Takeaway.** The agentic loop is a `stop_reason` switch. See [[D1 - Agentic Architecture & Orchestration]] §1.3.

---

## Q41 — Why append tool results to the conversation → **D**

**D.** So the model can incorporate the new information into its reasoning and decide the next action.

**Why D wins.** The API is stateless — the model sees only what's in the `messages` array. A tool result that isn't appended never existed. Appending it is what closes the loop and lets the next turn be informed by what the tool actually returned.

| Distractor | Why it fails |
|---|---|
| **A** server-side caching | Prompt caching is opt-in via `cache_control` and unrelated to why results are appended |
| **B** satisfy alternating-role validation | Real constraints exist on message structure, but they're a formatting consequence, not the purpose |
| **C** deduplicate repeated tool calls | The API performs no such deduplication |

**Takeaway.** Stateless API ⇒ the `messages` array **is** the model's memory. See [[D1 - Agentic Architecture & Orchestration]] §1.3.

---

## Q42 — Hub-and-spoke handoff of discovered URLs → **A**

**A.** The coordinator receives the URLs and includes them in the prompt when delegating to the document analysis agent.

**Why A wins.** That *is* hub-and-spoke: every exchange goes through the hub. Subagents return results to the coordinator and receive work from the coordinator; they never address each other. The coordinator stays the single point of orchestration, error handling, and observability.

| Distractor | Why it fails |
|---|---|
| **B** peer-to-peer call | Explicitly not hub-and-spoke — it's the mesh pattern the model is contrasted against |
| **C** shared memory store read concurrently | Bypasses the hub and introduces a race between a writer and a concurrent reader |
| **D** shared message queue both agents poll | A different architecture entirely (event-driven), and it removes the coordinator from the flow |

**Takeaway.** Hub-and-spoke: **all data flows through the coordinator.** Subagents never talk to each other. See [[D1 - Agentic Architecture & Orchestration]] §1.2.

---

## Q43 — Synthesis output oblivious to findings in the coordinator's history → **D**

**D.** Subagents do not inherit the coordinator's conversation history, so the findings were never in the synthesis agent's context.

**Why D wins.** Subagent invocations are **context-isolated**. That is the single most-tested subagent fact on this exam, and the stem sets it up perfectly: the data is *"clearly present in the coordinator's conversation history"* — which is exactly the context the subagent does not get.

| Distractor | Why it fails |
|---|---|
| **A** coordinator's window overflowed silently | Nothing silently drops; you'd get an explicit error. And the stem says the results **are** present |
| **B** encoding format problem | Invented failure mode |
| **C** system prompt excludes external data | Invented; no such instruction is described |

**Takeaway.** **No inheritance. Pass everything explicitly.** **Q45** is the same fact asked as "so what do I do about it." See [[D1 - Agentic Architecture & Orchestration]] §1.2.

---

## Q44 — Simple factual question hitting a four-agent pipeline → **D**

**D.** Analyze the query and invoke **only** the web search agent, skipping the rest.

**Why D wins.** Orchestration should be **proportionate to the request**. Routing a single-lookup question through document analysis, synthesis, and report generation multiplies latency and cost for output no better than the search result itself. The coordinator's job includes deciding how much machinery a query deserves.

| Distractor | Why it fails |
|---|---|
| **A** answer without any subagent | The question still needs a **web lookup**; the coordinator has no answer on its own |
| **B** route to synthesis first to triage | Synthesis combines findings — it isn't a router, and it has nothing to synthesize yet |
| **C** always run all four for consistency | The anti-pattern the stem is built to catch. Uniform pipelines waste the most on the simplest queries |

**Takeaway.** Match pipeline depth to query complexity. See [[D1 - Agentic Architecture & Orchestration]] §1.1.

---

## Q45 — Getting prior findings into a stateless synthesis call → **B**

**B.** Include the **complete findings** from both agents directly in the synthesis agent's prompt.

**Why B wins.** Given that subagents inherit nothing (**Q43**), the prompt is the only channel. And synthesis specifically needs the **full** material — its job is to weigh, reconcile, and attribute findings, which it cannot do from a thematic gloss that has already discarded the details.

| Distractor | Why it fails |
|---|---|
| **A** brief thematic summary | Pre-summarizing destroys the input to the summarizer. Conflicting figures (**Q23**) and citations (**Q46**) are exactly what gets lost |
| **C** database credentials to self-query | Adds infrastructure and an extra retrieval loop to move data you already hold |
| **D** re-invoke the upstream agents | Redoes completed work, at full cost, with possibly different results |

**Takeaway.** Stateless subagent + synthesis role ⇒ **pass complete findings in the prompt.** See [[D1 - Agentic Architecture & Orchestration]] §1.2.

---

## Q46 — Citations misattributed after narrative handoff → **B**

**B.** Have the document analysis agent return findings in a **structured format separating each claim from its metadata** — source URLs, document names, page numbers.

**Why B wins.** Citations get lost because prose **entangles claim and provenance**, forcing the synthesis agent to re-parse attribution out of flowing text. Structure it at the source — claim in one field, provenance in others — and attribution survives the handoff mechanically, with no re-derivation.

| Distractor | Why it fails |
|---|---|
| **A** re-verify against original documents | Expensive re-reading to recover information that shouldn't have been lost, and it can't fix data already dropped |
| **C** regex the citations out afterwards | Post-hoc parsing of natural language — brittle, and it runs after misattribution has already happened |
| **D** larger context budget | The problem is **format**, not capacity |

> [!TIP] The recurring inter-agent rule
> **Inter-agent boundaries carry structured data, not prose.** Prose is for humans at the end of the pipeline. Every hop between agents should move fields.

**Takeaway.** Separate content from metadata at the producing agent. See [[D1 - Agentic Architecture & Orchestration]] §1.5.

---

## Q47 — Prompt-enforced ordering vs a programmatic gate → **C**

**C.** Programmatic gates are **deterministic**; prompt instructions have a **non-zero failure rate** and cannot guarantee compliance.

**Why C wins.** This is the exam's own wording for the golden rule, and it names both halves: the guarantee on one side, the residual failure probability on the other. Nothing else in the option set is about reliability at all.

| Distractor | Why it fails |
|---|---|
| **A** gates are faster | Latency is not the argument, and the difference is negligible |
| **B** prompts are only evaluated at session start | False — the system prompt is in context for every turn |
| **D** system prompts can't reference other agents | False, and irrelevant to enforcement |

**Takeaway.** Memorize the phrase: *deterministic enforcement vs non-zero failure rate*. See [[D1 - Agentic Architecture & Orchestration]] §1.4 · pairs with **Q27** · official [Answer Key.md](../Exam%20Guide%20-%20Sample%20Questions/Answer%20Key.md) Q1.

---

## Q48 — One request, three distinct subtopics → **B**

**B.** Decompose into three research items, delegate for **parallel** investigation, then synthesize into a unified response.

**Why B wins.** The subtopics are independent — market size, regulation, competitors — so they have no ordering dependency and can run concurrently. Decompose, fan out, synthesize is the canonical coordinator pattern, and parallelism is the whole reason the multi-agent architecture exists.

| Distractor | Why it fails |
|---|---|
| **A** one agent handles all three sequentially | Forfeits parallelism and loads one context with three unrelated investigations |
| **C** synthesis agent delegates | Role confusion — synthesis combines results; the **coordinator** decomposes and delegates |
| **D** make the user resubmit three queries | Pushes orchestration onto the user and loses the unified synthesis they asked for |

> [!IMPORTANT] Parallel vs sequential — the one thing to check
> Fan out when subtasks are **independent**. Keep it sequential when one subtask's output is another's input. Q48's three topics are independent; **Q21**'s two extraction steps are not.

**Takeaway.** Independent subtopics ⇒ decompose → parallel → synthesize. See [[D1 - Agentic Architecture & Orchestration]] §1.1.

---

## Q49 — Refund exceeds the order total → **D**

**D.** `errorCategory: "validation"`, `isRetryable: false`, description explaining the amount exceeds the order total.

**Why D wins.** All three fields have to be right together. The failure is a **validation** error — the input is invalid against a business rule. It is **not retryable**: the same parameters will fail identically forever. And the description must carry the *reason*, because the agent's job now is to explain the situation to the customer, not to try again.

| Distractor | Why it fails |
|---|---|
| **A** `transient` / retryable | Wrong category and wrong flag. Guarantees a retry storm on a call that can never succeed |
| **B** success with an embedded warning | **Reports failure as success.** Downstream logic treats the refund as processed |
| **C** `permission` / retryable `true` | Misdiagnoses the cause (nothing about supervisor access is involved) and retryable is wrong again |

> [!TIP] Read all three fields before choosing
> Error-response items on this exam vary **category**, **`isRetryable`**, and **description** independently. An option with the right category and the wrong `isRetryable` is still wrong. Check every field.

**Takeaway.** Business-rule failure ⇒ `validation` + `isRetryable: false` + an explanatory description. See [[D2 - Tool Design & MCP Integration]] §2.6 · pairs with **Q35**.

---

## Q50 — 500-line `CLAUDE.md` nobody can maintain → **C**

**C.** Split into focused topic files in `.claude/rules/` — `testing.md`, `api-conventions.md`, `deployment.md`.

**Why C wins.** `.claude/rules/` is the documented alternative to a monolithic `CLAUDE.md`, and the option's three example filenames are the exam guide's own. It solves both stated problems at once: maintainability (one topic per file, per-file ownership) and reliability (smaller focused units, and rules gain the option of `paths:` scoping later).

| Distractor | Why it fails |
|---|---|
| **A** move to user-level `~/.claude/CLAUDE.md` | Turns shared project standards into per-engineer personal copies that immediately diverge |
| **B** duplicate into every subdirectory | Maximal duplication — the maintenance problem multiplied by the directory count |
| **D** into MCP server system prompts | Category error. MCP servers expose tools and resources; they don't carry project coding conventions |

**Takeaway.** Oversized `CLAUDE.md` ⇒ split into `.claude/rules/`. See [[D3 - Claude Code Configuration & Workflows]] §3.1.

---

## Q51 — 🔶 One engineer's conventions not applying → **C**

**C.** Ask the engineer to run `/memory` to check which memory files are loaded in their session.

**Why C wins by elimination.** The stem asks for a **first diagnostic step**, and C is the only option that diagnoses anything — the others are changes made before knowing the cause. Same repo, different behavior points squarely at a **configuration-loading** difference (a file not committed, a local override, a declined import approval, a different working directory), so inspecting what loaded is where to start.

| Distractor | Why it fails |
|---|---|
| **A** consolidate `.claude/rules/` into one `CLAUDE.md` | A restructure, not a diagnosis — and it discards path scoping across the whole team to chase one machine's problem |
| **B** reinstall Claude Code | Cargo-cult troubleshooting with no evidence pointing at the install |
| **D** conventions as comments in every source file | Enormous duplication, and it abandons the memory system rather than fixing it |

> [!WARNING] 🔶 The vault's verified distinction — `/context` is the load check, not `/memory`
> `/memory` lists memory-file **locations** across user and project scope — **including files that don't exist yet** — toggles auto memory, and opens a file in your editor. Because it reports locations rather than load state, it will happily list a path whether or not anything loaded from it, so it **cannot** diagnose a file that failed to load.
> **`/context` shows what actually loaded this session** (look under *Memory files*). For debugging path-scoped rules specifically, the `InstructionsLoaded` hook logs which instruction files loaded, when, and why.
> C is the keyed answer here because the alternatives are worse, and `/memory` is the command the blueprint names — but the option's justification ("verify which memory files are **currently loaded**") describes `/context`. On a real item offering both, **pick `/context`.**
> Source: <https://code.claude.com/docs/en/memory#view-and-edit-with-memory> · checked 2026-08-04

**Takeaway.** Same repo, different behavior ⇒ inspect loaded configuration first. See [[D3 - Claude Code Configuration & Workflows]] § `/memory` vs `/context`.

---

## Q52 — A TypeScript convention that must always apply → **A**

**A.** Project-level `CLAUDE.md` or a `.claude/rules/` file, so it is always loaded.

**Why A wins.** "Automatically every time Claude Code writes or modifies any TypeScript file" is the definition of **always-loaded project configuration**. Both named locations load without anyone remembering anything, and both are committed, so the rule applies to the whole team by default.

| Distractor | Why it fails |
|---|---|
| **B** user-scoped command run at session start | Requires every developer to remember, every session, on every machine |
| **C** project slash command called before each task | Same failure — "must remember to call" is disqualifying against "automatically" |
| **D** skill invoked on demand | Skills are **model-invoked or user-invoked capabilities**, not always-on conventions |

> [!IMPORTANT] The Claude Code config decision tree
> **Always on, every interaction** → `CLAUDE.md` or an unscoped `.claude/rules/` file. **Only when touching matching files** → `.claude/rules/` with `paths:`. **On demand, invoked by name** → `.claude/commands/` (slash command) or `.claude/skills/` (skill). **Deterministic enforcement on tool calls** → a hook.

**Takeaway.** "Automatically, every time" ⇒ always-loaded config, never an invoked one. See [[D3 - Claude Code Configuration & Workflows]] §3.1.

---

## Q53 — Sharing a scaffolding slash command with the team → **A**

**A.** Put the command file in the project's `.claude/commands/` directory and commit it.

**Why A wins.** Project-scoped commands live in `.claude/commands/`, and committing them is what makes them available to everyone who clones the repo — **no manual setup**, which the stem requires explicitly. They're versioned with the code they scaffold, so the command and the conventions it encodes stay in step.

| Distractor | Why it fails |
|---|---|
| **B** `SKILL.md` in `~/.claude/skills/` | User scope — that's one machine. Wrong location and wrong artifact type for a scaffolding command |
| **C** `~/.claude/commands/` + a wiki link | User scope again, plus per-machine manual setup — the exact thing ruled out |
| **D** inline in root `CLAUDE.md` via `@import` | `@import` pulls file **contents into memory**; it does not define a slash command |

> [!TIP] Project vs user scope, in one line
> `.claude/…` (in the repo, committed) = **the team**. `~/.claude/…` = **just you**. Any stem saying "all developers", "without manual setup", or "version control" is asking for project scope.

**Takeaway.** Team-shared command ⇒ `.claude/commands/`, committed. See [[D3 - Claude Code Configuration & Workflows]] §3.2.

---

## Q54 — Jest conventions loading only when editing test files → **A**

**A.** A `.claude/rules/` file with YAML frontmatter `paths: ["**/*.test.ts", "**/*.test.tsx"]`.

**Why A wins.** Both requirements are met by exactly one mechanism. **Conditional loading** — path-scoped rules trigger when Claude reads a matching file, and stay out of context otherwise. **Scattered across dozens of directories** — a glob matches by pattern regardless of location, which is precisely where subdirectory `CLAUDE.md` files fall down.

| Distractor | Why it fails |
|---|---|
| **B** user-level `~/.claude/CLAUDE.md`, added manually | Per-machine manual setup, always loaded, and not shared |
| **C** project `CLAUDE.md` | Loads on **every** interaction — the opposite of "only when editing a test file" — and adds permanent context noise |
| **D** a `CLAUDE.md` per test directory | Duplication across dozens of directories, guaranteed to drift, and it misses any directory someone forgets |

**Takeaway.** Conditional + cross-directory ⇒ `.claude/rules/` with `paths:`. See [[D3 - Claude Code Configuration & Workflows]] §3.3 · officially keyed in the exam guide's Q6.

---

## Q55 — 🔶 Migration conventions in three unrelated directories → **D**

**D.** A single `.claude/rules/` file with a glob matching `**/migrations/**/*.py`.

**Why D wins.** One file, one owner, one place to change — matching all three locations by pattern, and automatically covering the fourth migrations directory someone adds next quarter. It is the same mechanism as **Q54**, with maintainability rather than conditional loading as the stated goal.

| Distractor | Why it fails |
|---|---|
| **A** identical `CLAUDE.md` in each directory | Three copies to keep in sync; they will drift, and a new migrations directory silently gets nothing |
| **B** shared file `@import`ed from three `CLAUDE.md` files | Better than A — one source of truth — but still three files to create and maintain, and still no coverage for new directories |
| **C** everything in the root `CLAUDE.md` | The option **states its own defect**: loaded even when editing non-migration files. Permanent context cost for narrow rules |

> [!WARNING] 🔶 The frontmatter key is `paths:`, not `globs:`
> Option D writes `globs: [...]`. The documented key for path-scoped rules in `.claude/rules/` is **`paths:`** — which the bank itself uses correctly one question earlier, in **Q54-A**. D is still the intended answer because the mechanism is right and the alternatives are all worse, but do not memorize the key from this option. `globs:` is Cursor's spelling, not Claude Code's.

**Takeaway.** Same convention, several unrelated directories ⇒ one path-scoped rule file. See [[D3 - Claude Code Configuration & Workflows]] §3.3 · pairs with **Q54**.

---

## Q56 — Logging-library migration across 60+ files → **C**

**C.** Plan mode to audit usage and design the strategy, then **switch to direct execution** to apply the changes.

**Why C wins.** The task has two phases with different needs. Auditing patterns and choosing replacement APIs per log level is **investigation under uncertainty** — plan mode's job, and cheap to get wrong. Applying an agreed mapping across 60 files is **mechanical execution** — direct mode's job, and pointlessly slow if every edit is re-planned.

| Distractor | Why it fails |
|---|---|
| **A** 60 parallel sessions, one per file | No shared audit, so 60 independent and mutually inconsistent decisions — the opposite of "consistent changes" |
| **B** plan mode throughout | Plan mode on every individual edit adds enormous overhead once the strategy is settled |
| **D** direct execution throughout | Skips the audit, so the mapping gets invented file-by-file and consistency is lost |

**Takeaway.** Investigate in plan mode, execute directly. The switch point is when the **unknowns are resolved**. See [[D3 - Claude Code Configuration & Workflows]] §3.4 · pairs with **Q57**.

---

## Q57 — One-line null check, stack trace already points at it → **D**

**D.** Direct execution — well-scoped, single file, clear fix, no architectural exploration needed.

**Why D wins.** Every uncertainty plan mode exists to resolve is already gone: the file is known, the line is known, and the fix is known. Planning here produces a plan describing a one-line change, which costs turns and delivers nothing.

| Distractor | Why it fails |
|---|---|
| **A** plan mode, "every change benefits" | The overgeneralization the item is testing. Plan mode is for **uncertainty**, not ceremony |
| **B** direct execution **plus** `Explore` to check other functions | The strongest distractor — genuinely good engineering, and `Explore` is the right tool for that sweep. But it is **scope the engineer didn't ask for**; the stem is one function with one clear fix |
| **C** plan mode, "multiple strategies across the codebase" | Contradicts the stem, which says the fix is one conditional check |

**Takeaway.** Known file + known fix ⇒ direct execution. Unknown scope or competing approaches ⇒ plan mode. See [[D3 - Claude Code Configuration & Workflows]] §3.4 · pairs with **Q56**.

---

## Q58 — Inconsistent INI → YAML transformations → **C**

**C.** Provide 2–3 concrete input/output examples showing the exact transformation.

**Why C wins.** Consistency of **format** is what few-shot examples convey best. Prose can describe a mapping rule; only an example pins down the exact key casing, nesting depth, and indentation you want. Two or three worked pairs remove the ambiguity that detailed paragraphs kept leaving open.

| Distractor | Why it fails |
|---|---|
| **A** longer prose | The stem says detailed prose already failed. More of the failing thing is not the fix |
| **B** "always be consistent" in `CLAUDE.md` | The Q4/Q13 non-instruction, now in a config file |
| **D** plan mode to outline the approach | Produces an outline in the same under-specified prose. Plan mode addresses uncertainty about *what to do*, not precision about *output format* |

> [!IMPORTANT] Prose describes, examples specify
> When the failure is **inconsistent output format**, reach for few-shot examples — in Claude Code, the API, and CI prompts alike. When the failure is **inconsistent decisions**, reach for explicit criteria *plus* examples (**Q10**).

**Takeaway.** Format inconsistency ⇒ show examples, don't write more rules. See [[D4 - Prompt Engineering & Structured Output]] §4.1.

---

## Q59 — Iterative refinement of a CSV parser with many edge cases → **D**

**D.** Write a comprehensive test suite first, then iterate by sharing test failures with Claude Code.

**Why D wins.** Tests turn "handle quoted delimiters, multiline fields, and malformed rows" into an **executable, objective specification**. Each failure is a precise, reproducible signal to correct against, and the suite prevents regressions as later edge cases get fixed — which is exactly what "supports iterative refinement" asks for.

| Distractor | Why it fails |
|---|---|
| **A** generate, test manually, file bug reports | Manual verification is slow and inconsistent, and bug reports are a worse feedback channel than a failing assertion |
| **B** implementation and tests in one pass | Tests written alongside the implementation encode the **same misunderstandings** — they pass without proving anything |
| **C** implement, then self-review in the same session | The **Q20** failure exactly: same-session self-review is compromised by generation context |

**Takeaway.** Well-defined requirements + many edge cases ⇒ **tests first, then iterate on failures**. See [[D3 - Claude Code Configuration & Workflows]] §3.5.

---

## Q60 — `Edit` fails because the target text isn't unique → **D**

**D.** `Read` the full file, apply the modification, and `Write` the complete updated file.

**Why D wins.** When the surrounding text is genuinely non-unique — identical boilerplate repeated across sections — `Read` → modify → `Write` is the documented fallback. It sidesteps uniqueness entirely by replacing the whole file with content you have verified.

| Distractor | Why it fails |
|---|---|
| **A** split the file so sections become unique | Restructures the project to work around a tool constraint |
| **B** `sed` on a line number | Line numbers shift with every prior edit, and `Bash` text surgery bypasses the tools' safety checks |
| **C** retry with "a larger context window setting" | Fabricated — `Edit` has no such setting, and context size was never the issue |

> [!WARNING] Don't over-generalize into "`Edit` fails ⇒ rewrite the file"
> The documented **first** remedies are a **longer `old_string`** carrying enough surrounding context to pin one occurrence, or **`replace_all: true`** when every occurrence should change. A whole-file `Write` is the last resort. D wins *here* only because the stem establishes that the boilerplate is identical across sections. On any item where a longer anchor would work, rewriting the file is the wrong answer.
> Source: <https://code.claude.com/docs/en/tools-reference>

**Takeaway.** No unique anchor available ⇒ `Read` → modify → `Write`. See [[D3 - Claude Code Configuration & Workflows]] § Built-in tools.

---

## Cross-cutting patterns in this bank

Read the key sideways and the 60 items collapse into about a dozen rules. Each row lists the questions that test it — drill them as a set.

| Rule | Questions |
|---|---|
| **Deterministic requirement ⇒ code, not prompt** (hook, gate, interception) | 8, 16, 27, 47 |
| **Vague quality adjectives are non-instructions** — replace with testable criteria | 4, 13, 36, 58 |
| **Few-shot conveys judgment and format**, not lookup tables | 10, 19, 36, 58 |
| **Structured output is guaranteed by tool use**, never by prompting | 6, 7, 17 |
| **Design the structured field to answer your actual question** | 15, 17, 46 |
| **Tool descriptions drive selection**; overloaded tools get split | 32, 33, 34 |
| **Error metadata exists to pick a recovery strategy** (`isRetryable`) | 3, 35, 38, 49 |
| **Subagents inherit nothing — pass everything explicitly** | 43, 45, 46 |
| **Hub-and-spoke: all data flows through the coordinator** | 42, 44, 48 |
| **`stop_reason` drives the agentic loop** — never parse prose | 39, 40, 41 |
| **Self-assessment is uncalibrated**; self-review in-session is compromised | 14, 17, 20, 25, 59 |
| **Context degradation ⇒ externalize or filter**, don't instruct or upsize | 9, 11, 18 |
| **Match effort to the task** — pipeline depth, plan vs direct, tool choice | 28, 37, 44, 56, 57 |
| **Always-on config vs invoked config vs path-scoped rules** | 50, 52, 53, 54, 55 |
| **Escalate on explicit request, no assessment step** | 24 |

## How to use this key

1. **Sit the 60 closed-book first.** Record your answers before opening this file — the grid at the top is deliberately the first thing you'd see, so scroll past it.
2. **Grade against the grid**, then read the worked answer for every item you missed **and every item you got right by elimination**.
3. **Log misses in [[Weak Areas Deep Dive]]** by *rule*, using the table above — not by question number. This bank's numbering corresponds to nothing else in the vault.
4. **Read the four 🔶 items regardless of your answer.** Q14, Q21, Q51, and Q55 each contain a technical inaccuracy in the bank's own wording. Memorizing them as written would cost you marks.
5. **Then drill D3.** A quarter of this bank is Claude Code configuration and workflow — the material the three CyberSkill sittings barely reach, and the subject of two of the six official scenarios.

---

**Back to:** [README.md](README.md) · [../README.md](../README.md) · [[Weak Areas Deep Dive]] · [[Answer Patterns Index]] · [[Official Exam Blueprint]]
