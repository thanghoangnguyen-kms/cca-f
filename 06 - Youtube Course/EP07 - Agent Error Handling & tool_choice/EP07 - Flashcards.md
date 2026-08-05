---
tags:
  - CCA-F
  - domain-2
  - tool-design
  - error-handling
  - tool-choice
  - flashcards
  - youtube-course
date: 2026-08-03
status: done
domain: "2 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 07"
---

# 🃏 EP07 Flashcards — Agent Error Handling & tool_choice

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP07 - Agent Error Handling & tool_choice]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D2 - Tool Design & MCP Integration]] · [[D5 - Context Management & Reliability]] · [[EP06 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 2 — Silent Failure

**Q: A tool starts failing in production. The agent doesn't crash and logs nothing unusual. What is happening, and what is this failure mode called?**
A: **Silent failure.** The error was returned as ordinary content, so the model treated it as a success payload and reasoned on top of it — producing confident wrong output with no crash and no alert.

**Q: Why do the defaults for error propagation work in opposite directions in imperative code versus an agentic loop?**
A: An unhandled **exception propagates by default** — it unwinds until something catches it, and kills the process if nothing does. An unflagged **tool failure is absorbed by default** into context. So in code you write handlers to *stop* propagation; in an agent you write them to *cause* it.

**Q: A tool returns the string "DatabaseTimeout: connection refused" with no error flag. What does the model do with it?**
A: Treats it as a **successful result** and reasons on top of it. A tool result is just content — without a structural marker there is nothing distinguishing a failure message from a data payload.

**Q: Name the two opposite failure modes an unhandled tool error can produce.**
A: **Silent absorption** — the agent swallows the error and returns confident garbage — or **total collapse**, where the failure breaks the whole pipeline. Both are anti-patterns; bounded local recovery is the middle path.

---

## Domain 2 — The Error Flag

**Q: Give the exact field name and casing that marks a failed tool result on the Claude Messages API.**
A: **`is_error`** — snake_case, an **optional** boolean on the `tool_result` content block.

**Q: Give the field name MCP uses for the same purpose, and how it differs from the Messages API's.**
A: **`isError`** — camelCase. Same concept, different protocol layer: `is_error` on a Messages API `tool_result` block, `isError` on an MCP server's tool result.

**Q: A search tool finds no matching records. Should it set the error flag, and what breaks if it does?**
A: **No.** An empty result with **no** error flag means the query succeeded and no matching data exists. Setting the flag makes the coordinator treat a valid outcome as an access failure and retry it indefinitely.

**Q: Why is returning an empty array on tool failure worse than returning nothing at all?**
A: Because an empty array is a **valid success outcome** meaning "no matching records" — so the agent converts an infrastructure failure into a confident factual claim ("you have no orders") rather than merely lacking data.

**Q: Do you need to return `is_error` results for server tools like web search? Why or why not?**
A: **No.** Claude handles server-tool errors transparently and attempts an alternative response itself. The `is_error` discipline is a **client-tool** obligation.

**Q: Name three error codes web search can surface.**
A: Any three of **`too_many_requests`**, **`invalid_input`**, **`max_uses_exceeded`**, **`query_too_long`**, **`unavailable`**.

**Q: The lecture says an MCP-wrapped API is "exposed via the A2A protocol." What is wrong with that?**
A: It conflates two unrelated protocols. **MCP** connects a host's MCP client to MCP servers over **stdio**, **streamable HTTP**, **SSE** (legacy), or **WebSocket**, configured in `.mcp.json`. **A2A (Agent2Agent)** is a separate agent-to-agent protocol with no role in how Claude discovers or calls MCP tools.

---

## Domain 2 — Error Categories & Retryability

**Q: `is_error: true` reaches the model. Why isn't that enough to recover?**
A: It says *stop trusting this result*, not *what to do next*. Retry, reroute, and escalate are three different responses, and only the **error category** distinguishes them.

**Q: Name the four error categories and one trigger for each.**
A: **Transient** — timeout, network blip, service unavailable. **Validation** — malformed or invalid input parameters. **Business logic** — policy violation (refund over $500). **Permission** — authorization failure or inaccessible tool.

**Q: Which single error category is retryable?**
A: **Transient** only. Validation, business logic, and permission are all `isRetryable: false`.

**Q: State exactly what the `isRetryable` flag asks.**
A: **Would repeating this same call, unchanged, plausibly succeed?** Not "could this ever be made to work."

**Q: Why is a business-logic error non-retryable even though the tool is available and the input is well-formed?**
A: Because the rule is **deterministic** — a $750 refund hitting a $500 gate gets the identical denial on every attempt. Nothing about repeating the call changes the outcome.

**Q: Why is a permission error non-retryable?**
A: **Rights do not change between attempts.** Retrying cannot grant authorization; the fix is escalation or re-scoping the agent's tools.

**Q: A tool rejects `amount: "fifty"`. Calling again with `amount: 50` succeeds. Does that make the error retryable?**
A: **No.** A retry is repeating the **identical** call; changing the arguments is a **new call**. The fix was correcting the input, not the repetition — so validation stays `isRetryable: false`.

**Q: Officially, how many times does Claude self-correct an invalid tool call before giving up?**
A: **2–3 times with corrections**, then it apologises to the user. So the "retry with a fixed value" behaviour is something you get for free by returning an instructive error — not something you mark retryable.

**Q: Which tool-definition field eliminates validation errors structurally, and what does its schema require?**
A: **`strict: true`** at the top level of the tool definition — it guarantees inputs match the schema exactly. The schema must include **`additionalProperties: false`** and **`required`**. Note it goes on the tool, not inside `tool_choice`.

---

## Domain 2 — Structured Error Metadata

**Q: The coordinator receives `is_error: true` with content "Operation failed". Name the three decisions it cannot make.**
A: Whether to **retry**, whether to **reroute** to another subagent or fallback tool, and whether to **escalate to a human**. Every recovery path looks equally plausible, so it guesses.

**Q: Name the four jobs a structured error payload does, and the field that does each.**
A: **`errorCategory`** selects the recovery family · **`isRetryable`** answers whether to repeat the call · **`description`** serves the human reading logs · **`alternatives`** gives the coordinator a concrete fallback instead of a dead end.

**Q: Which fields of a structured error payload are defined by the Claude API, and which are your own convention?**
A: **Only `is_error`** is API-defined and validated. `errorCategory`, `isRetryable`, `retryAfterMs`, `description`, and `alternatives` are **application-level conventions** carried inside the free-form `content`.

**Q: If the API defines only one error field, what part of the structured-error pattern *is* official guidance?**
A: The **principle**: write instructive error messages, not labels — *"include what went wrong and what Claude should try next, e.g. 'Rate limit exceeded. Retry after 60 seconds.'"* The envelope's field names are yours; the requirement to be actionable is documented.

**Q: Why should `retryAfterMs` appear only on retryable errors?**
A: A retry delay on a permission or business-logic error is misleading metadata that invites the coordinator to make a wrong decision — it implies a retry is worth pacing when no retry should happen at all.

**Q: Two placement rules for `tool_result` blocks will 400 a request. Name both.**
A: (1) A `tool_result` must **immediately follow** its corresponding `tool_use` block — no messages in between. (2) Within the user message, `tool_result` blocks must come **first** in the `content` array; any text comes after.

**Q: One tool of three in a parallel batch fails. What must you return for it?**
A: Its `tool_result` with **`is_error: true`** — never drop it. A `tool_use` id with no matching `tool_result` is an error.

**Q: Why are tool results treated as untrusted input, and where should that content stay?**
A: They often carry content from outside your control — web pages, inbound email, user uploads, third-party APIs — which an attacker may use to embed instructions redirecting Claude (**indirect prompt injection**). Keep it inside `tool_result` blocks rather than promoting it into `system` prompts or plain user `text` blocks.

---

## Domain 2 — Local Recovery & Escalation

**Q: A subagent's tool times out. Should it report to the coordinator immediately?**
A: **No.** For a **transient** category it should retry **locally** once or twice first, and escalate only once local options are exhausted — with structured metadata attached.

**Q: Describe the three-layer recovery ladder and who owns each layer.**
A: **Layer 1 — tool:** execution fails. **Layer 2 — subagent:** catches the error and retries locally for transient categories only. **Layer 3 — coordinator:** receives the structured error once local options are exhausted, then routes (retry, alternate subagent, fallback tool, or human).

**Q: What makes a subagent's local retry safe rather than wasteful?**
A: The **error category** gates it. Retrying only happens for transient failures; a subagent that retries a permission or business-logic error is burning attempts on a deterministic denial.

**Q: Name the anti-pattern where a single failure kills the whole run, and the correct option that replaces it.**
A: The **all-or-nothing anti-pattern** — terminating a pipeline on a single untried failure. Correct: *subagents handle their own transient failures locally; the coordinator is involved only when local options are exhausted.*

**Q: Local retries are capped at one or two. What breaks if you remove the cap?**
A: An unbounded retry converts a transient blip into a **stalled agent** — it never fails and never escalates. It is the third anti-pattern hiding behind empty arrays and pipeline termination.

**Q: A tool fails transiently 30% of the time. Why stop at two local retries rather than three?**
A: Returns decay geometrically while latency accrues linearly. Escalations fall $0.3 \to 0.09 \to 0.027$; the first retry removes ~70% of escalations, the second ~21%, a third only ~0.8 percentage points of end-to-end success — for another full retry delay.

---

## Domain 2 — Tool Overload & Scoped Access

**Q: Does giving one agent twenty tools make it more capable than giving it five?**
A: **No** — it degrades accuracy. Selection happens at inference time over descriptions; each extra tool is another candidate to discriminate against, so with overlapping descriptions or near-synonym names selection confidence drops.

**Q: Give the tool-count reliability thresholds this vault records.**
A: **4–5 tools** reliable · **10+** degraded selection · **18+** significant misuse / wrong tool firing.

**Q: How do official docs address tool scale, given they publish no hard ceiling?**
A: Three levers: **consolidate** related operations into fewer tools with an `action` parameter · **namespace** by service (`github_list_prs`) · use the **tool search tool** with `defer_loading` to work with thousands of tools by loading definitions on demand.

**Q: What are the two constraints when using the tool search tool with `defer_loading`?**
A: The **search tool itself must not be deferred**, and **at least one tool must remain non-deferred** — otherwise the API returns **400** (*"All tools have defer_loading set"*).

**Q: What is scoped tool access, and what is its *primary* benefit?**
A: Role-based tool distribution — each agent sees only the tools its role needs. Its primary benefit is **selection accuracy** (a smaller, role-aligned set is an easier choice), with least-privilege security as a second payoff.

**Q: Stem: "Which tool configuration best improves synthesis agent accuracy?" Give the answer and reject each distractor.**
A: **A scoped `verify_fact` tool.** *Full web access* adds selection ambiguity and invites the agent out of its role. *More search tools* compounds that. *A more capable model* misdiagnoses the defect — it is the tool surface, not the reasoning.

**Q: Why does handing a synthesis agent `search_web` hurt more than it helps?**
A: An agent given a tool outside its specialism tends to **misuse** it — the synthesis agent starts researching instead of synthesising. That is a role failure caused by tool surface, not a tool failure.

**Q: Two mechanisms can stop an agent calling a tool outside its role. Name both and say which fixes the root cause.**
A: **Don't pass the tool** in that agent's definition (unselectable) or **deny it in a `PreToolUse` hook** (unexecutable). Scoping fixes the root cause — it removes the selection ambiguity; the hook is the deterministic backstop for an attempt that should have been impossible.

**Q: For a cross-role need that comes up often, what does the vault recommend instead of granting the full generic tool?**
A: A **scoped constrained tool** — e.g. `load_document` that validates document URLs only, rather than a general-purpose `fetch_url`.

---

## Domain 2 — tool_choice

**Q: How many `tool_choice` values are there, and what are they?**
A: **Four:** `auto`, `any`, `tool`, and **`none`**. (The lecture names only the first three — `none` prevents any tool call while the definitions stay in the request.)

**Q: What is the default `tool_choice` in each of the two conditions?**
A: **`auto`** is the default when `tools` **are** provided; **`none`** is the default when **no** tools are provided.

**Q: Distinguish `auto` from `any` in one sentence each.**
A: Under **`auto`** a tool call is **permitted** — the model may answer in text instead. Under **`any`** a tool call is **required** — the model must call something, though it picks which.

**Q: What form must `tool_choice` take, and what is invalid?**
A: Always an **object** — `{"type": "auto"}`, `{"type": "any"}`, `{"type": "none"}`, `{"type": "tool", "name": "..."}`. Bare strings like `"auto"` are not valid.

**Q: You set `tool_choice: {"type": "any"}` and also instruct Claude to explain itself first. What happens to the explanation?**
A: It **never appears.** With `any` or `tool` the API prefills the assistant message to force a tool call, so no natural-language text precedes the `tool_use` block regardless of instructions. For both, use `auto` and put the instruction in the user turn.

**Q: `tool_choice: {"type": "any"}` guarantees a tool is called. What does it *not* guarantee, and what fixes that?**
A: It does not guarantee the **arguments are schema-valid**. Add **`strict: true`** to the tool definition — officially, combine the two to guarantee both that a tool is called and that its inputs follow the schema exactly.

**Q: You need structured output but no tool call at all. Which mechanism?**
A: **`output_config: {format: {"type": "json_schema", "schema": {...}}}`** — it constrains the response itself. The older top-level `output_format` parameter is deprecated.

**Q: Which thinking mode is incompatible with forced tool use, and which is fine?**
A: **Manual extended thinking** (`thinking: {type: "enabled"}`) **errors** with `tool_choice` `any` or `tool` — only `auto` and `none` work. **Adaptive thinking** (`{type: "adaptive"}`, including models where thinking is on by default such as Claude Opus 5) supports forced tool use.

**Q: Which parameter caps the model at one tool call per turn, and what is the default?**
A: **`disable_parallel_tool_use: true`**, addable to any `tool_choice` value. Parallel tool use is **on by default**.

**Q: What does changing `tool_choice` mid-conversation do to your prompt cache?**
A: It **invalidates cached message blocks.** Tool definitions and the system prompt stay cached, but message content must be reprocessed — so don't vary it per request inside a long cached conversation without expecting the cost.

---

## Domain 2 — Exam Signals

**Q: What is the complete structured error signal the exam expects?**
A: **`is_error` + `errorCategory` + `isRetryable`** — the flag that a failure occurred, the family of recovery that applies, and whether repeating the identical call could succeed.

**Q: An exam stem asks how to improve tool selection reliability. What is the answer, and what is the tempting wrong one?**
A: **Scoped tool access** — role-based distribution. The trap is **adding more tools or a broader tool**, which actively degrades selection.

**Q: An exam stem requires guaranteed structured output generation. Which `tool_choice` value?**
A: **`{"type": "any"}`** — it forces a tool call rather than allowing a text response. (`auto` may produce no tool call at all if the model judges none applies.)

**Q: Name the three options this episode says to eliminate on sight.**
A: **Silently returning empty arrays** on failure · **terminating a pipeline** on a single untried failure · **escalating to the coordinator without attempting local recovery.**

**Q: Which claims from this episode about the exam's own structure are unverified?**
A: The precise **task-statement numbering** ("task 2.2, 2.3... taken from the syllabus"). The domain/subdomain mapping is sound — error responses and tool distribution are D2's 2.2 and 2.3 in this vault's map — but the published syllabus numbering can't be checked against public docs, and Domain 2's exam weight percentage isn't published in this vault.

**Q: The transcript says "is retrievable" throughout. What is the actual term?**
A: **`isRetryable`** / *retryable*. It is the episode's most frequent transcription artifact and has nothing to do with retrieval — every occurrence is about whether a call may be repeated.

*Back to: [[EP07 - Agent Error Handling & tool_choice]]*
