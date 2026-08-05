---
tags:
  - CCA-F
  - domain-4
  - prompt-engineering
  - structured-output
  - json-schema
  - tool-use
  - youtube-course
  - flashcards
date: 2026-08-05
status: done
domain: "4 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 16"
---

# 🃏 EP16 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP16 — Structured Output & JSON Schema** (Domain 4, task statements 4.3 and 4.4). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry material the lecture doesn't reach: `strict: true`, `additionalProperties: false`, the `tool_result`/`is_error` feedback turn, the constraints a schema can't express, and `output_config.format`. Three cards flag places where the lecture is **incomplete or self-contradictory** — the "API enforces the exact schema" claim, the assistant-only retry turn, and defaulting a missing number to `0`. Each names the exam answer and the real-code answer separately.

**Related:** [[EP16 - Structured Output & JSON Schema]] · [[D4 - Prompt Engineering & Structured Output]] · [[EP14 - Prompt Engineering - Explicit Criteria & False Positives]] · [[EP15 - Few-Shot Prompting]] · [[EP17 - Batch API & Multi-Pass Review]]

---

## Domain 4 — Why Freeform Prompting Breaks

**Q: Name the three ways freeform "return JSON" prompting breaks a production pipeline.**
A: **Markdown wrapping** — JSON fenced in backticks, so the parser hits a backtick instead of a bracket. **Schema drift** — omitted optional fields, renamed keys, altered nesting. **Fabrication of required fields** — the model invents a value the source doesn't contain.

**Q: Of markdown wrapping, schema drift, and fabrication, which is most dangerous and why?**
A: **Fabrication** — it's the only one that raises no error anywhere. Wrapping crashes the parser immediately; drift eventually misroutes data loudly enough to notice. Fabricated data is schema-valid, so it passes every structural gate and reaches the user as plausible falsehood.

**Q: A model returns correct invoice figures inside a markdown code fence. Is the extracted data wrong?**
A: No — the data is right, the delivery is wrong. It's purely structural: *"intellectually the data is correct but the way it is returning the data is not correct."* This is the *loud* failure mode, which is why it's the one teams fix first.

**Q: Why does schema drift often survive longer in production than a JSON parse error?**
A: Because it doesn't crash. A renamed or omitted key means the consumer silently reads the wrong field or gets `undefined` rather than throwing — so the pipeline keeps running while quietly producing wrong results.

**Q: Your tools all work correctly in isolation. What new requirement appears the moment their output feeds a subagent?**
A: A **shape contract**. Correct-and-parseable are different properties, and only correctness was ever specified. Each tool returns data in whatever format it likes; the consumer needs one known schema.

---

## Domain 4 — The Fake Tool Paradigm

**Q: What is a "fake tool" in structured extraction?**
A: A tool with no business logic whose only job is to enforce schema compliance on the output — *"define a tool that does nothing but accept a structured payload."* It may not be a function at all.

**Q: Why is a tool schema more reliable than a prompt instruction that says "respond in this JSON format"?**
A: It changes the **enforcement layer**. A prompt instruction is a request the model may or may not honour; a tool schema is part of the request and steers decoding directly.

**Q: What is the "zero-parsing" payoff of the fake-tool pattern, in code terms?**
A: You read `response.content[0].input` and get an already-parsed dictionary — no `json.loads`, no regex, no stripping code fences. There is no text to parse because the payload arrives as a structured tool input.

**Q: A fake tool has no function body. What does that reveal about what "tool" means in the API?**
A: That a tool definition is a **schema contract**, not necessarily executable code. Everything the API needs is the name, description, and `input_schema`; whether anything runs on your side is your business.

**Q: The lecture calls the fake tool a "strongly typed contract." What single property of the request makes that phrase earn its keep?**
A: The `input_schema` — declaring `type: object`, the `properties`, and `required` moves the shape requirement out of prose and into the request payload, where the decoder is steered by it.

---

## Domain 4 — Schema Guarantees and strict Mode

**Q: Your fake tool declares `properties` and a `required` list. Is the schema now a validated guarantee? (docs)**
A: **No.** A plain `input_schema` steers the model strongly but isn't validated. The guarantee requires **`strict: true`** on the tool definition plus **`additionalProperties: false`** in the schema.

**Q: What two things must a tool definition carry for schema compliance to be guaranteed rather than merely likely? (docs)**
A: `strict: true` as a top-level field on the tool definition, and `additionalProperties: false` on every object in the schema. Together they guarantee `tool_use.input` validates exactly — names and inputs both.

**Q: Why is `additionalProperties: false` mandatory for strict mode rather than optional? (docs)**
A: Without it there is no closed set of keys, so "the exact schema" has no meaning — the model can add fields the schema never declared. It's the only schema constraint in this episode that is never optional.

**Q: The lecture says "the API enforces the exact schema before returning data." How should you treat that claim? (docs)**
A: As a description of **strict mode without naming it**. Exam answer: tool use with a JSON schema is the structured-output mechanism. Real code: add `strict: true` and `additionalProperties: false`, or the "contract" is a strong suggestion.

**Q: What is the strongest single configuration for a schema-guaranteed extraction boundary? (docs)**
A: `tool_choice: {"type": "any"}` combined with `strict: true` — the first guarantees a tool *is* called, the second guarantees the input *conforms*. The lecture supplies only the first half.

**Q: You force a tool call and notice the model no longer explains itself before the payload. Bug or expected? (docs)**
A: **Expected.** Forcing tool use (`any` or `tool`) prefills the assistant message, so the model emits no natural-language text before the `tool_use` block *even if asked to*. It's a trade: clean payload, no commentary.

**Q: Your extractor must also report "I couldn't determine the vendor." Why can't that be prose, and what's the fix? (docs)**
A: Because forced tool use suppresses prose entirely. Model it as **data** — a nullable field, or an `"unclear"` enum member with a detail string. Alternatively use `{"type": "auto"}` and request the tool in the user turn, accepting that Claude may decline.

---

## Domain 4 — tool_choice

**Q: List all four `tool_choice` values and what each guarantees.**
A: `{"type": "auto"}` — may call a tool or answer in text. `{"type": "any"}` — must call one of the provided tools. `{"type": "tool", "name": "..."}` — must call that specific tool. `{"type": "none"}` — cannot call any tool.

**Q: Is `tool_choice` a string or an object?**
A: An **object** — `{"type": "any"}`, not `"any"`. A bare string is a common exam distractor.

**Q: What are the two defaults for `tool_choice`?**
A: `{"type": "auto"}` when `tools` **are** provided; `{"type": "none"}` when no `tools` are provided.

**Q: Why is `auto` the wrong choice for a strict extraction pipeline?**
A: It leaves the decision to Claude, which may answer in text instead of producing the payload the next stage needs. An extraction boundary has no room for model discretion — the payload *is* the point of the call.

**Q: What exactly does `{"type": "any"}` guarantee, and what does it not?**
A: It guarantees a `tool_use` block appears in the response **every time**. It does **not** guarantee *which* tool — with several tools declared, Claude may pick the wrong one.

**Q: Your extractor declares exactly one fake tool. Does `any` behave differently from naming that tool?**
A: **No — with one tool they're equivalent.** Both produce that tool's `tool_use` block on every call. The distinction only matters once `tools` holds more than one entry.

**Q: When is `{"type": "any"}` the better choice over naming a specific tool?**
A: When the input type — and therefore the schema — isn't known until runtime (invoice vs receipt vs purchase order). Choosing is genuine work you want the model to do. If the schema is fixed, name the tool and leave nothing to selection.

**Q: You use `any` with six extraction tools and see occasional wrong-tool calls. What are the two fixes?**
A: (1) Improve tool selection — precise names, detailed descriptions, examples (Domain 2's misrouting material). (2) If the correct tool is actually knowable at request time, stop using `any` and name it.

**Q: What does `disable_parallel_tool_use: true` do, and why does it suit a single-tool extractor? (docs)**
A: It caps the response at one tool call. For a lone fake tool that removes any possibility of two competing payloads in one response that you'd have to reconcile.

---

## Domain 4 — Required and Nullable

**Q: Why does marking a field `required` with type `string` actively cause fabrication?**
A: Because the schema tells the model the field must have a value — *"those fields I need to fill at any cost."* With nothing in the source, the only way to satisfy the schema is to invent something. **The schema created the pressure.**

**Q: Write the exact JSON Schema for "this field must be present but may be null."**
A: `"vendor_name": {"type": ["string", "null"]}`, with `vendor_name` still listed in `required`. A **union type** — not a `nullable: true` key, which is OpenAPI, not JSON Schema.

**Q: What is the difference between "present but null" and "absent from the payload," and why does it matter?**
A: `null` says *we looked and it wasn't there*; absence says nothing at all. Keeping the field required-and-nullable preserves that signal, so a consumer can distinguish a genuinely empty source from an extractor that doesn't produce the field.

**Q: A missing field keeps failing validation. Why is "remove it from the schema" the wrong fix?**
A: It loses the **presence guarantee**. Every downstream consumer now has to treat a missing key as ambiguous. Making it nullable fixes the failure while keeping the field's meaning intact.

**Q: The lecture suggests defaulting a missing *number* to `0`. Why is that wrong?**
A: `0` is a number the document didn't contain — fabrication that validates. A `total_amount` of `0` becomes indistinguishable from a genuine zero-value invoice and passes every downstream check. Use `["number", "null"]`. Reserve `0` for cases where zero is the true semantic value.

**Q: A field's value is present in the document but genuinely ambiguous. What's better than forcing a guess or returning null? (docs)**
A: An `enum` with an explicit `"unclear"` or `"other"` member plus a free-text detail field. That gives three distinguishable states — confident value, explicitly unclear, absent — instead of collapsing two of them.

**Q: Which earlier episode's rule is required-plus-nullable the schema-level expression of?**
A: EP14's **null-is-valid rule** — *"if absent, return null; never fabricate."* Same principle: a prohibition only works when it ships with the positive alternative beside it. EP14 states it in prose; this states it in the type system.

---

## Domain 4 — Syntax vs Semantics

**Q: What does a strict JSON schema guarantee, and what does it explicitly not guarantee?**
A: It guarantees the output is **structurally flawless**. It guarantees **nothing about the truth of the data** — the model may have hallucinated every value and still satisfied the schema.

**Q: Give three examples of syntax errors and three of semantic errors in extraction.**
A: **Syntax:** missing required fields, a string where a number belongs, malformed JSON. **Semantic:** reading the wrong table row, confusing similar terms (invoice date vs due date), hallucinated values.

**Q: The model extracts the *due* date when you asked for the *invoice* date. Which error class, and why can't the schema catch it?**
A: **Semantic.** Both are strings, both are dates, both satisfy the schema — the difference is a wrong *reading* of the document, not a wrong *shape*. Type systems can't see it.

**Q: Why does a schema-valid falsehood travel further through a pipeline than malformed JSON?**
A: Because every gate it passes only checks shape. Malformed JSON dies at the first parser; a well-typed wrong value clears each stage without error and surfaces to the end user.

**Q: What fixes syntax errors, and what fixes semantic errors?**
A: **Syntax:** strict JSON schemas and tool-use enforcement — the fake tool. **Semantic:** business-logic validation, cross-field consistency checks, and human review. Neither set substitutes for the other.

**Q: Which hook is the natural home for semantic validation, and why a hook rather than the schema?**
A: `PostToolUse` — it runs deterministically after the tool produces data, and because it's **code** it can compute things a schema has no vocabulary for (sums, comparisons, lookups against a source of truth).

**Q: Name four constraints a structured-output JSON schema cannot enforce. (docs)**
A: Numerical constraints (`minimum`, `maximum`, `multipleOf`), string constraints (`minLength`, `maxLength`), recursive schemas, and array constraints beyond `minItems` of 0 or 1. Unsupported constraints are **stripped** and appended to field descriptions as prose.

**Q: Why does the unsupported-constraint list make "add business logic" the only option for semantic checks rather than just the recommended one? (docs)**
A: Because the rules you'd want — total > 0, date not in the future, line items sum to total — are literally inexpressible as enforced schema constraints. There is nowhere else for them to live but code.

**Q: If unsupported constraints are stripped from the schema, what happens to a `minimum: 0` you wrote? (docs)**
A: It stops being a decoding constraint and becomes a **prose hint** in the field description, with the SDK validating the response against your original schema client-side. A hint plus a post-hoc check — not enforcement.

---

## Domain 4 — The Self-Correcting Retry Loop

**Q: Trace the four steps of the self-correcting retry loop.**
A: (1) Run the extraction. (2) Validation gate checks structural validity. (3) On pass, return. (4) On **format** failure, append the specific error to the conversation and re-run, bounded by `max_retries`.

**Q: `occupation` is absent from the source but present as `null`. Does the validation gate pass?**
A: **Yes.** `null` is a structurally valid value, so the payload is well-formed and the gate passes. That's exactly what makes `null` an honest escape hatch rather than a failure to fix.

**Q: Complete the rule: retries are ______, not ______.**
A: Retries are **format correctors**, not **data generators**. *"You cannot prompt the model to look harder for information that doesn't exist."*

**Q: An extraction pipeline fails validation on every retry; the documents genuinely lack the required `tax_id`. What's the correct fix?**
A: **Change `tax_id` to `["string", "null"]`.** The gate then passes on `null`, the loop stops failing, and nothing is invented.

**Q: For "retries fail every time, data is absent," name the three distractors and why each is wrong.**
A: *Make the model generate / look harder* — instructs a hallucination. *Increase `max_retries`* — more attempts never find absent data. *Remove the field* — loses the presence guarantee. All three are scripted traps.

**Q: Why does generic "validation failed" feedback burn your entire retry budget?**
A: *"Generic validation failed yields identical failures."* With no new information the model repeats the same choice, so each retry reproduces the original output and you exhaust `max_retries` on identical failures.

**Q: What must targeted retry feedback contain?**
A: The **field name**, the **expected format**, and ideally the **value received** — e.g. *"invoice_date must be ISO 8601 (YYYY-MM-DD); received '03/14/26'."* Plus a restatement of the null rule, so "fix this" isn't read as "fill it in."

**Q: Why should the retry error message restate "return null if absent"?**
A: Because a bare "this failed, fix it" applies pressure in exactly the fabrication direction — the fastest way to make a validation error disappear is to put *something* in the field. The feedback has to close that door explicitly.

**Q: Who decides `max_retries`, and what is the actual transferable concept?**
A: You do — *"it is completely your own logic."* The examinable concept is the **feedback loop** (validate → targeted error → re-run → bounded), not any particular retry count.

---

## Domain 4 — Wiring the Feedback Turn

**Q: The lecture's retry code appends `{"role": "assistant", "content": response.content}` and re-sends. What's missing? (docs)**
A: The **`user` turn carrying a `tool_result` block** with a matching `tool_use_id` and `is_error: true`. The assistant echo preserves the `tool_use` block but is only half the round trip.

**Q: Which role carries a `tool_result` block back to the model? (docs)**
A: The **`user`** role. The assistant turn holds the `tool_use` block; the answer to it — success or failure — always comes back as `tool_result` inside a `user` message.

**Q: What happens if you send a follow-up request where a `tool_use` block has no matching `tool_result`? (docs)**
A: The request is **rejected**. Every `tool_use` block must be answered by a `tool_result` whose `tool_use_id` matches — this is why the assistant-only append in the lecture produces an API error rather than a correction.

**Q: How do you report a failed validation to the model as a failure rather than as data? (docs)**
A: Set **`is_error: true`** on the `tool_result` block, with the error text as its content. Without the flag the model may read your error string as a legitimate tool result.

**Q: Why does the `tool_use_id` on a `tool_result` matter in a retry loop? (docs)**
A: It binds the error to the specific call. With several tool calls in flight, an unmatched result leaves the model unable to tell which call failed — and an unmatched `tool_use` block is a rejected request.

---

## Domain 4 — Architecture and Escalation

**Q: Retries are exhausted and the field is still empty. What is the correct architectural response?**
A: Stop retrying; return an honest error stating the data is **absent** rather than malformed, and escalate to the coordinator — *"I cannot handle it, please escalate."*

**Q: When absent data blocks extraction, where does the real fix live?**
A: **Upstream, in the input.** The document must be corrected or sanitized before it reaches the agent. Retrying downstream cannot reach an input defect, which is why it's a human handoff rather than a loop parameter.

**Q: Name the four layers of the exam's extraction architecture and the failure each addresses.**
A: **Schema** (required + nullable) → fabrication. **Protocol** (`tool_choice` forcing a call) → the model declining to produce a payload. **Semantics** (cross-field + business rules) → schema-valid falsehood. **Recovery** (bounded targeted retries → human handoff) → format errors, then genuine absence.

**Q: Why can't the four architecture layers substitute for each other?**
A: Each catches a different failure. A schema can't detect a misread row; semantic checks can't make the model emit a payload; retries can't manufacture absent data; forcing a call can't stop fabrication. Drop one and its failure class goes unhandled.

**Q: A subagent's extraction genuinely cannot be completed. What is the correct behaviour toward the coordinator?**
A: Report the failure upward explicitly rather than returning degraded or partial data. A subagent that can't meet its contract must say so — silent degradation is what propagates a bad value into the final output.

---

## Domain 4 — Beyond Tool Use

**Q: The lecture calls tool use "the apex structured output mechanism." What does that miss? (docs)**
A: There are **two** first-class mechanisms. `output_config.format` (JSON outputs) constrains **Claude's response** via constrained decoding; `strict: true` validates **tool parameters**. Docs say they *"solve different problems and work together"* and don't rank them.

**Q: Which parameter constrains Claude's *response* to a JSON schema, and what's its exact shape? (docs)**
A: `output_config={"format": {"type": "json_schema", "schema": {...}}}` on `messages.create()`. The schema needs `additionalProperties: false`.

**Q: When do you reach for `output_config.format` versus strict tool use? (docs)**
A: `output_config.format` when you want **the reply itself** to be a JSON object. `strict: true` tool use when you want **a tool call's inputs** guaranteed. Both together in agentic workflows — they answer different questions.

**Q: What is the deprecated predecessor of `output_config.format`? (docs)**
A: The top-level **`output_format`** parameter. It's deprecated API-wide, which makes the old name a plausible exam distractor.

**Q: Prefilling the assistant turn with `{"` used to force JSON output. What's its status now? (docs)**
A: **Removed** — a last-assistant-turn prefill returns a 400 on current models. Structured outputs and tool use are the replacements, which strengthens *"discard prompt-based JSON"*: the fallback trick isn't just unreliable, it's rejected.

**Q: Does switching from tool use to `output_config.format` improve semantic accuracy? (docs)**
A: **No.** Constrained decoding guarantees valid, type-safe output and says nothing about whether the values are true. The syntax/semantics boundary is a property of schemas as such, not of the enforcement mechanism.

**Q: The lecture says "discard prompt based JSON." Which half of the "apex mechanism" claim is right?**
A: That half. Plain "respond in JSON" carries no guarantees at all. What's incomplete is treating tool use as the *only* enforced mechanism — `output_config.format` is the other, for a different target.

---

*Back to: [[EP16 - Structured Output & JSON Schema]]*
