---
tags:
  - CCA-F
  - practice-exam
  - answer-key
date: 2026-08-23
status: done
---


# Practice 05 — Answer Key

> [!NOTE] How to read this
> One entry per question from [[Questions]]. Each entry gives the **correct answer with its full text**, why it wins, and then **every wrong option quoted in full** with the reason it fails — so you never have to flip back to the question file.

> [!IMPORTANT] Where these answers come from
> ~40 of the 60 questions recur (sometimes reworded) in the vault's already-verified `CCA-F Mock Exam Answer Key/` folder; those keyed answers are reused directly. The remaining short-form questions were keyed against official Anthropic guidance on the agentic loop, tool-result error reporting, orchestrator–worker research systems, context management, and structured extraction. The practice site's own key was **not** consulted.

**Answer grid**

| Q | A | Q | A | Q | A | Q | A | Q | A | Q | A |
|---|---|---|---|---|---|---|---|---|---|---|---|
| 1 | C | 11 | B | 21 | B | 31 | D | 41 | C | 51 | A |
| 2 | A | 12 | D | 22 | B | 32 | C | 42 | C | 52 | C |
| 3 | B | 13 | C | 23 | B | 33 | D | 43 | A | 53 | B |
| 4 | B | 14 | B | 24 | B | 34 | B | 44 | A | 54 | A |
| 5 | C | 15 | A | 25 | C | 35 | D | 45 | B | 55 | C |
| 6 | C | 16 | A | 26 | B | 36 | B | 46 | B | 56 | A |
| 7 | B | 17 | B | 27 | B | 37 | D | 47 | B | 57 | D |
| 8 | B | 18 | C | 28 | B | 38 | B | 48 | C | 58 | B |
| 9 | B | 19 | D | 29 | D | 39 | D | 49 | D | 59 | B |
| 10 | B | 20 | C | 30 | C | 40 | B | 50 | D | 60 | C |

---

### Q1 — `code_exploration`

**Correct: C — "Summarize key rendering findings, then spawn a sub-agent for physics exploration with that summary in its initial context."**

The symptom — generic "typical rendering patterns" instead of the `VulkanPipeline`/`FrameGraph` specifics it actually read — is **context degradation**: the concrete detail has been squeezed out. The fix has two halves, and C is the only option with both: **compact** what matters into a durable summary, then **isolate** the new exploration in a fresh sub-agent context that is seeded with that summary.

**Wrong answers**

- **A. "Spawn a sub-agent to explore physics independently, then manually synthesize its findings with the rendering knowledge accumulated in the main conversation."** — It isolates the physics work but synthesizes against the *degraded* main context. You'd be merging fresh physics findings into rendering knowledge that has already gone fuzzy, which is precisely the thing that's broken.
- **B. "Continue in the current context with more targeted prompts referencing the specific classes by name."** — Naming `VulkanPipeline` in the prompt doesn't restore the detail; it just asks a saturated context to try harder. Prompt phrasing cannot fix a context-window problem.
- **D. "Use /clear to reset context completely, then start fresh with physics exploration using file paths from the project's CLAUDE.md."** — `/clear` throws away 25 minutes of rendering discovery with nothing captured first. The engineer's question is explicitly about how physics *integrates with rendering*, so the rendering knowledge is required, not disposable.

**Takeaway:** Summarize *before* you isolate. Compaction preserves the findings; sub-agents preserve the context budget.

---

### Q2 — `customer_support`

**Correct: A — "Instruct the agent to escalate when the customer requests a human, when the issue requires policy exceptions, or when the agent cannot make meaningful progress."**

These three triggers cover the genuinely distinct reasons a human is needed — explicit customer preference, authority the agent doesn't have, and capability exhaustion — and each is a judgment the model is well-placed to make from the live conversation. Escalation criteria should be stated as intent, not as a proxy signal.

**Wrong answers**

- **B. "Configure the agent to escalate after three consecutive tool calls that fail to resolve the customer's stated issue."** — Tool-call count is a proxy, not a cause. It escalates a customer whose three retries were transient network blips, and it never escalates a policy-exception request that resolves in one clean tool call.
- **C. "Implement sentiment analysis that monitors for frustration indicators (negative language, repeated questions, exclamation marks) and trigger escalation when the frustration score exceeds a configured threshold."** — Frustration and *needing a human* are different variables. A calm customer asking for a policy exception scores low and never escalates; an irritated customer with a one-click fix escalates unnecessarily.
- **D. "Build a rules engine that maps specific issue types, customer segments, and product categories to escalation decisions, removing the need for model judgment calls."** — Deliberately removing model judgment is the flaw. The rules can only enumerate issue types you already anticipated; "the agent cannot make meaningful progress" is inherently a runtime judgment no static table encodes.

**Takeaway:** Escalate on *reasons* (asked for a human, needs authority, stuck), not on *symptoms* (retry counts, tone scores). Contrast with [[#Q41 — `customer_support`]]: a hard compliance limit is exactly where you *do* remove model discretion.

---

### Q3 — `extraction_pipeline`

**Correct: B — "Verify that the line items sum to the extracted total, and on a mismatch retry or flag the record."**

The document carries its own redundancy — the parts must sum to the whole. Cross-checking derived values against each other is a free, deterministic integrity test that catches both OCR damage and extraction mistakes before the record reaches downstream accounting.

**Wrong answers**

- **A. "Trust the total field because it is printed prominently."** — Visual prominence has nothing to do with extraction accuracy. A large, bold total is exactly as easy to misread as a small one.
- **C. "Check only that the total is a number."** — A type check confirms the field is well-formed, not that it's *correct*. `$1,240.00` misread as `$1.240.00` is still a number.
- **D. "Accept the first extraction without checking."** — No validation at all; the failure mode is a silently wrong ledger entry.

**Takeaway:** When the source contains an arithmetic or logical invariant, assert it. Schema validation proves the shape; invariants prove the content.

---

### Q4 — `extraction_pipeline`

**Correct: B — "Resubmit only the 300 failed documents after chunking them into smaller pieces, then combine the partial extractions."**

`custom_id` exists so you can correlate each result back to its source request. Only the 300 oversized documents failed, and their failure cause is real — they exceed the context window — so chunking is the actual fix. Reprocessing only the failures is 3% of the cost of a full re-run.

**Wrong answers**

- **A. "Reprocess the entire batch with prompt caching enabled to reduce the cost of retrying requests with identical system prompts."** — Caching trims the system-prompt cost but does nothing about document length; the 300 would fail again, and you'd have paid to re-run 9,700 already-successful documents.
- **C. "Resubmit the entire 10,000 document batch using a model tier with a larger context window."** — Even if a larger window fits them, you're re-billing 9,700 good extractions to fix 300 — and a bigger window only postpones the ceiling for the next oversized document.
- **D. "Increase the `max_tokens` parameter for the 300 failed documents and resubmit them in a new batch."** — `max_tokens` caps the **output** length. `context_length_exceeded` is an **input** problem; raising `max_tokens` makes it worse, since output tokens count against the same window.

**Takeaway:** `custom_id` is the retry key for the Batch API. And know which knob is input vs. output — `max_tokens` is output-only.

---

### Q5 — `research_pipeline`

**Correct: C — "Merge the reports, collapse duplicate findings, and keep one cited instance of each."**

De-duplication is the coordinator's job precisely because it is the only component that sees all three reports. Collapsing to one *cited* instance keeps the audit trail intact while removing the repetition that would otherwise read as three independent confirmations.

**Wrong answers**

- **A. "Concatenate all three reports verbatim into the final answer."** — Ships the redundancy to the reader and inflates apparent corroboration: the same fact found three times by three overlapping searches is still one fact.
- **B. "Keep the first report and discard the other two unread."** — Discards the non-overlapping findings, which is the entire reason you dispatched three sub-agents.
- **D. "Ask the user to remove the duplicates."** — Pushes the orchestrator's core synthesis responsibility onto the user.

**Takeaway:** Overlapping sub-agent coverage is expected and healthy; reconciling it is the coordinator's work, not the reader's.

---

### Q6 — `customer_support`

**Correct: C — "The order details are added to the conversation and the model reasons about which action to take."**

That *is* the agentic loop: the tool result is appended to the message history, the whole conversation goes back to the model, and the model decides the next step (another tool call, or a final answer). The 45-day fact only influences behaviour because the model reads and reasons over it.

**Wrong answers**

- **A. "The orchestration layer automatically routes to the next tool based on the order's status field."** — Describes a workflow engine, not an agent. Nothing in the loop inspects `status` and dispatches; the harness executes the tool the *model* asked for.
- **B. "The agent follows a pre-configured decision tree mapping order attributes to specific tool calls."** — Same category error. A decision tree is deterministic branching written by you; the agentic loop's branching is the model's reasoning at each turn.
- **D. "The agent executes the remaining steps in a tool sequence planned at the start of the request."** — Agentic loops decide **one step at a time**. There is no upfront plan being replayed — that's what lets the agent react to the 45-day discovery at all.

**Takeaway:** Tool result → appended to conversation → model reasons → next action. If you can draw the flowchart in advance, it isn't an agentic loop.

---

### Q7 — `code_exploration`

**Correct: B — "Read the entry points and project structure, then search for the area the feature touches."**

Progressive, targeted exploration: establish a cheap high-level map, then narrow to the specific region using search. You load only what you need, in the order that makes each next read informed.

**Wrong answers**

- **A. "Load every file into context so nothing is missed."** — Exhausts the context window on a repository where the overwhelming majority of files are irrelevant, and degrades reasoning about the files that do matter.
- **C. "Start editing the first file that looks related."** — Editing before understanding. "Looks related" is a guess, and the cost of a wrong guess is a broken change plus wasted context.
- **D. "Ask the user to explain every file."** — Offloads onto the engineer the exact work the agent has tools to do, and doesn't scale past a handful of files.

**Takeaway:** Orient → narrow → read. Structure first, grep second, full reads last.

---

### Q8 — `customer_support`

**Correct: B — "Return the error message in the tool result content with the isError flag set to true."**

This is the defined tool-result error contract. The error stays *inside* the conversation as a normal tool result, so the model sees what went wrong and can react — retry, ask the customer, or escalate — while `isError`/`is_error` marks it unambiguously as a failure rather than data.

**Wrong answers**

- **A. "Log the error server-side and return an empty result to avoid confusing the model."** — An empty result is indistinguishable from "no matching order." The model will confidently tell the customer their order doesn't exist when the truth is that the database timed out.
- **C. "Throw an exception from the tool handler so the agent framework can catch and log it."** — An uncaught exception breaks the loop rather than informing it. The model never learns the call failed and has no chance to recover.
- **D. "Return a success response with a 'status' field indicating the error type."** — Misreports a failure as a success. It may work if the model happens to read your ad-hoc field, but it defeats every framework-level behaviour keyed on the error flag, and there's no reason to invent a convention when a standard one exists.

**Takeaway:** Errors are information for the model, not just for your logs. Report them *in* the tool result, flagged as errors. See [[#Q15 — `customer_support`]] for what those errors should actually contain.

---

### Q9 — `code_exploration`

**Correct: B — "Confirm in the current code where the auth check actually runs, then make the change there."**

Documentation is a claim about the code; the code is the code. Before a security-relevant edit, verify the claim against the current source — READMEs drift silently as modules get refactored.

**Wrong answers**

- **A. "Trust the README and edit the module it names."** — Acting on an unverified secondary source. If the check moved, you patch a dead path and leave the live one open — the worst possible outcome for an auth change.
- **C. "Search the commit history for the original author and ask them."** — Slow, dependent on someone's availability, and still second-hand. The answer is sitting in the working tree.
- **D. "Assume the check moved and search at random."** — Replaces one unverified assumption with another, minus a strategy.

**Takeaway:** Docs are a hypothesis. Grounding a change means confirming it in the code you're about to edit.

---

### Q10 — `code_exploration`

**Correct: B — "Search the codebase for all references first, then plan the change across the call sites."**

Enumerate the blast radius before you touch anything. The reference list *is* the change plan, and it lets you spot the hard cases (dynamic dispatch, re-exports, tests, string references) while it's still cheap to adjust the approach.

**Wrong answers**

- **A. "Rename it and run the build to see what fails."** — Compile-and-see only finds statically resolved references. Reflection, dynamic imports, string-based lookups, config, and docs all pass the build and break at runtime — and in a dynamically typed codebase there may be no build to fail at all.
- **C. "Rename only the definition and assume callers will adapt."** — Ships a knowingly broken codebase; callers do not adapt on their own.
- **D. "Add a second function and leave the old one untouched."** — Doesn't perform the rename; it creates duplicate implementations that will drift.

**Takeaway:** Grep first, edit second. The set of call sites is the plan, and it catches the references a compiler never will.

---

### Q11 — `code_exploration`

**Correct: B — "Spawn subagents to investigate specific questions (e.g., 'find all test files for payment processing', 'trace refund flow dependencies') while the main agent coordinates findings and preserves high-level understanding."**

Sub-agents each get their own context window, so the expensive file-reading happens *outside* the main context and only compact answers come back. The main agent keeps the high-level model of the module and stays coherent — and the sub-tasks are independent, so they can run in parallel.

**Wrong answers**

- **A. "Document all current findings in a summary report, clear context completely, then use that report as the sole reference for continuing the investigation."** — "Sole reference" is the flaw. Once the context is cleared, anything the summary omitted is unrecoverable without re-reading, and the investigation is only 8 of 45 files in — far too early to freeze what matters.
- **C. "Clear context with /clear, then selectively re-read only the most critical files discovered so far, writing key findings to a scratchpad file that persists between context resets."** — The scratchpad is a genuinely good technique (see [[#Q44 — `code_exploration`]]), but here it's bolted to a reset-and-re-read cycle that pays for the same files twice and still funnels all 45 files through one context. Sub-agents avoid the re-reading entirely.
- **D. "Switch to using Grep to search for specific function names instead of reading full files, reducing the content loaded into context for remaining exploration."** — Grep is cheaper per query, but "identify untested code paths" requires understanding control flow, not locating names. It slows the burn rate without changing the fact that one context must hold everything.

**Takeaway:** When the work exceeds one context window, delegate the reading and keep the synthesis. Sub-agent context isolation is the lever.

---

### Q12 — `code_exploration`

**Correct: D — "Enhance the MCP tool descriptions to explain when each tool is preferable to text manipulation and clarify expected inputs and outputs."**

Tool selection is driven by the tool description — it's the only thing the model sees when choosing. "`extract_function`: extracts a function from code" gives it no reason to prefer that over `Write`, which it already understands well. Fix the input to the decision, and the decision fixes itself.

**Wrong answers**

- **A. "Implement a request classifier that detects refactoring intent and automatically routes those requests to the MCP server before the agent processes them."** — Builds a whole routing layer to work around a one-line documentation gap, and hard-codes intent detection that the model already does natively.
- **B. "Remove the Write tool from the agent's configuration for refactoring sessions so it must use the MCP tools for code modifications."** — Coercion by deprivation. It cripples the agent for every legitimate `Write` need in the session, and the model still doesn't know *which* refactoring tool fits — it just has fewer escape hatches.
- **C. "Accept this as expected behavior since simpler tools like sed are more predictable than specialized refactoring tools."** — Gives up on the investment. `sed`-based refactoring is *less* reliable than AST-aware tooling, not more.

**Takeaway:** A tool the model won't pick is a description problem. Say what the tool does, when to prefer it, and what it returns. Same lesson as [[#Q37 — `code_exploration`]].

---

### Q13 — `extraction_pipeline`

**Correct: C — "Set `tool_choice` to {\"type\": \"tool\", \"name\": \"`extract_metadata`\"} and process the enrichment requests in subsequent turns after receiving the extracted metadata."**

There's a real data dependency: `lookup_citations` needs the DOI that `extract_metadata` produces. Forcing the first tool on the **first turn** guarantees the ordering, and leaving later turns unforced lets Claude run the enrichment tools once the DOI is actually in context.

**Wrong answers**

- **A. "Set `tool_choice` to 'any' so Claude must use a tool, combined with system prompt instructions prioritizing `extract_metadata`."** — `"any"` forces *some* tool, not a *specific* one; Claude can still pick `lookup_citations` first. The prompt instruction is advisory, which is exactly the guarantee you don't have today.
- **B. "Set `tool_choice` to 'auto' and reorder the tool definitions so `extract_metadata` appears first in the tools array, since Claude prioritizes earlier-listed tools."** — The stated premise is false: array position is not a documented priority mechanism. This is superstition dressed as configuration.
- **D. "Set `tool_choice` to {\"type\": \"tool\", \"name\": \"`extract_metadata`\"} for every API call in the pipeline, ensuring Claude always extracts metadata before any enrichment can occur."** — Right mechanism, wrong scope. Forcing it on *every* call means the enrichment tools can never fire; the pipeline extracts metadata forever and never answers "how cited is it."

**Takeaway:** `tool_choice: {"type":"tool","name":...}` is a per-request lever. Force the first turn, then release.

---

### Q14 — `code_exploration`

**Correct: B — "Search within the file for the function and read only that region and its immediate dependencies."**

Targeted reads. You get the function plus the context needed to understand it (imports, helpers it calls) without paying for thousands of unrelated lines.

**Wrong answers**

- **A. "Read the entire file into context to be thorough."** — Thoroughness measured in tokens. A few thousand lines of unrelated code crowds out the reasoning space for the part you actually care about.
- **C. "Read the first few hundred lines and stop."** — Arbitrary truncation. The target function is very likely not in the first few hundred lines, and you'd have no way to know.
- **D. "Reformat the file so it is easier to scan."** — Modifies source you weren't asked to change, produces a noisy diff, and doesn't reduce the amount you have to read.

**Takeaway:** Read regions, not files. Search for the anchor, then expand outward only as far as comprehension requires.

---

### Q15 — `customer_support`

**Correct: A — "Enhance error responses with structured metadata: include errorCategory (transient/validation/permission), isRetryable boolean, and a description of what caused the failure."**

All three misbehaviours share one root cause: `"Operation failed"` carries no information to act on, so the model guesses. Structured metadata makes the right action derivable — `isRetryable: true` → retry, `permission` → escalate, `validation` → ask the customer.

**Wrong answers**

- **B. "Create an `analyze_error` MCP tool the agent calls after any failure to determine the error category and recommended action."** — An extra round-trip to recover information your server already had and deliberately threw away. It also can't classify reliably from `"Operation failed"` — there's nothing to classify.
- **C. "Implement retry logic with exponential backoff in your MCP server for all errors, returning to the agent only after retries are exhausted."** — Fixes only the transient case, and makes the others worse: a nonexistent order ID gets retried with backoff before failing, so the customer waits longer for the same dead end. Permission errors still surface as an unexplained failure.
- **D. "Add few-shot examples to the system prompt demonstrating how to interpret error message patterns and select appropriate responses for each."** — Teaches pattern-matching on a string that has no patterns. Every error is the identical `"Operation failed"`.

**Takeaway:** Design your error payloads as a machine-readable contract — category, retryability, cause. Prompting cannot recover information the tool never returned.

---

### Q16 — `customer_support`

**Correct: A — "Return structured error responses with retryable: false for business errors and a customer-friendly explanation for Claude to use."**

One change solves both stated problems. `retryable: false` stops the 3–4 wasted turns on permanently-failing business errors, and the customer-friendly explanation gives Claude accurate language for *why* the refund can't happen ("this order is past the 30-day return window") instead of paraphrasing a raw error string.

**Wrong answers**

- **B. "Add few-shot examples showing how to distinguish retryable from non-retryable errors by parsing error message text."** — Makes correctness depend on the model parsing free text. Any new error phrasing from the backend silently breaks the classification, and it does nothing for response quality.
- **C. "Add a `check_refund_eligibility` tool that must be called before `process_refund` to prevent business rule violations."** — Adds a mandatory round-trip to every refund and duplicates business rules in two places that will drift. It also doesn't help with the 5% transient technical errors at all.
- **D. "Implement automatic retry logic at the tool level for technical errors only, passing business errors to Claude without retries."** — Handles the transient half well, but business errors still arrive as bare plain text. The agent may not recognise them as terminal, and the customer-facing explanation problem is untouched.

**Takeaway:** Tell the model whether an error is worth retrying *and* give it language for the customer. Structured errors do both.

---

### Q17 — `research_pipeline`

**Correct: B — "Dispatch eight sub-agents in parallel, each returning a short structured summary with citations, then synthesize from the summaries."**

The sources are independent, so fan-out is free latency-wise, and each sub-agent absorbs the raw page in its *own* context. The coordinator only ever sees eight compact cited summaries — fast and context-light at the same time.

**Wrong answers**

- **A. "Read all eight sources into the coordinator context, then write the synthesis in a single pass."** — Floods the coordinator with eight full pages of raw content, which is precisely the failure mode the question asks you to avoid.
- **C. "Process the sources one at a time in a single agent, appending each full page to the running prompt."** — Worst of both: serial latency *and* unbounded context growth, with earlier sources degrading as later ones pile on.
- **D. "Pick the two sources that look most promising and ignore the rest to save tokens."** — Buys efficiency by discarding six of eight sources. That's not an optimisation, it's an incomplete answer.

**Takeaway:** Parallel sub-agents + compact cited returns is the canonical orchestrator–worker research pattern. Isolation buys context; independence buys speed.

---

### Q18 — `extraction_pipeline`

**Correct: C — "Submit batches every 4 hours containing documents from that window"**

Do the arithmetic against the SLA. A document arriving at the *start* of a window waits the full window before submission, then up to 24 hours for the batch. 4-hour windows: 4 + 24 = **28 hours**, inside the 30-hour SLA with 2 hours of headroom for the 99.9% reliability requirement.

**Wrong answers**

- **A. "Submit batches every 6 hours containing documents from that window"** — 6 + 24 = **30 hours** exactly. Zero margin: any document that arrives at the top of a window and hits the full 24-hour batch window lands precisely on the deadline, so a 99.9% guarantee is impossible.
- **B. "Submit a single batch at end of day containing all documents from that day"** — A document arriving just after the previous cutoff waits up to 24 hours to be *submitted*, then up to 24 hours to process: **~48 hours** worst case, far past the SLA.
- **D. "Use the real-time API for all documents instead of batch processing"** — Meets the SLA trivially but forfeits the 50% discount, which is the entire stated reason for batching.

**Takeaway:** Batch latency = *your* accumulation window + the batch API's up-to-24-hour window. Budget both, and leave margin — an SLA met exactly is an SLA missed at scale.

---

### Q19 — `extraction_pipeline`

**Correct: D — "Route standard reports to the `Batch API` for 50% cost savings, and route urgent exception reports to the real-time Messages API."**

Segment traffic by latency requirement. The monthly reports are archived and have no latency need, so they take the 50% discount; the exception reports have a hard 30-minute alert window, so they take the synchronous path. Same schema, two routes.

**Wrong answers**

- **A. "Submit all documents to the real-time Messages API to ensure consistent processing latency across document types."** — Consistent and needlessly expensive. You pay full price for the bulk of the volume that had no latency requirement at all.
- **B. "Submit all documents to the `Batch API` with `custom_ids` for tracking. When results arrive, immediately process urgent documents and trigger delayed alerts for exceptions."** — The phrase "delayed alerts" concedes the failure. The batch window runs up to 24 hours; no amount of promptness *after* results arrive can meet a 30-minute SLA.
- **C. "Queue all documents and submit hourly batches, flagging urgent documents for expedited handling when batch results return."** — Hourly submission doesn't shorten the batch's own up-to-24-hour completion window, and there is no priority lane within a batch. A flagged urgent document can still miss 30 minutes by a wide margin.

**Takeaway:** Batch is a *throughput* discount, not a latency tier. Split the traffic; never try to make batch behave synchronously.

---

### Q20 — `code_exploration`

**Correct: C — "Use Grep to find authentication entry points, read those files, then follow imports and function calls to map the auth flow incrementally."**

Grounded, incremental tracing. Each read is chosen by what the previous read revealed, so context is spent only on files that are provably on the auth path — the right approach for 800+ files with a finite window.

**Wrong answers**

- **A. "Read any CLAUDE.md and README files first, then ask the engineer to specify which 10-15 files are most important for understanding the auth system."** — The engineer *just joined* and is asking the agent precisely because they don't know which files matter. Reading `CLAUDE.md` first is fine; making the newcomer pick the file list inverts the request.
- **B. "Launch parallel subagents to explore different services simultaneously, then synthesize their findings into an architectural overview."** — Superficially attractive, but auth flow is *cross-cutting*: it threads through services via shared middleware and token passing. Partitioning by service cuts the flow at every boundary, and each sub-agent sees only its fragment.
- **D. "Read all files containing 'auth', 'login', 'permission', or 'token' in their content or filename."** — Keyword-matched bulk reading in an 800-file codebase pulls in an enormous, mostly irrelevant set (every file that logs a token, every test fixture) while missing auth logic that doesn't use those words.

**Takeaway:** Grep to find the door, read to understand the room, follow the imports to map the building.

---

### Q21 — `extraction_pipeline`

**Correct: B — "Add an 'other' value to your enum with a separate `property_type_detail` string field for specifics when 'other' is selected."**

The escape-hatch pattern. The enum keeps its clean, queryable values for the common cases, `"other"` gives the model a valid choice when nothing fits — so validation stops failing — and `property_type_detail` preserves the actual text so you don't lose information and can mine it for the next enum value.

**Wrong answers**

- **A. "Continuously expand the enum to include newly observed property types and add monitoring for additional edge cases."** — Perpetual chase. The question states new types "continue appearing regularly," so you are permanently one deploy behind, and validation keeps failing in the gap.
- **C. "Change `property_type` from an enum to a free-form string and implement a normalization step in post-processing."** — Throws away the constraint entirely. You trade an 8% validation-failure rate for 100% unconstrained output and an open-ended normalization mapping to maintain — with no signal at all about which values were unexpected.
- **D. "Add few-shot examples to your prompt demonstrating how to map unexpected property types to the closest existing enum value."** — Forces lossy coercion: a "converted warehouse" becomes "house" and the data is now silently wrong. Validation passes; correctness doesn't.

**Takeaway:** Constrained vocabularies need an escape hatch plus a detail field. Never make the model choose between failing validation and lying.

---

### Q22 — `extraction_pipeline`

**Correct: B — "Chunk the document with slight overlap, extract per chunk, then merge and reconcile the fields."**

You need fields from across the whole contract, so every part must be read. Chunking covers all of it, the overlap prevents fields from being lost at chunk boundaries, and the merge-and-reconcile step resolves the duplicates the overlap creates.

**Wrong answers**

- **A. "Truncate the document to what fits and extract from the first part."** — Guarantees you miss every field in the discarded remainder — and contracts put the important terms at the end as often as the beginning.
- **C. "Summarize the document first, then extract from the summary."** — Extracts from a lossy derivative. Summarisation drops exactly the specific values (dates, amounts, party names) that extraction targets.
- **D. "Raise the temperature so the model fills in the missing parts."** — Asks the model to invent content it never saw. Higher temperature increases variance, not knowledge.

**Takeaway:** Overlapping chunks + reconciliation is the standard long-document extraction pattern. Overlap is insurance against boundary loss; reconciliation is the premium you pay for it.

---

### Q23 — `customer_support`

**Correct: B — "Answer the question directly and clearly, and offer escalation only if the customer needs more."**

The agent has the answer in the knowledge base. Answer it, and keep the escalation path visible without forcing it. That's first-contact resolution.

**Wrong answers**

- **A. "Escalate every question to a human to be safe."** — Defeats the purpose of the agent, burns human capacity on questions it can answer, and makes the customer wait for nothing.
- **C. "Ask the customer to confirm three times before answering."** — Manufactured friction with no risk-reduction benefit for a simple informational question.
- **D. "Give a long disclaimer and avoid answering."** — Hedging instead of helping. The customer leaves without the answer the agent was holding.

**Takeaway:** Escalation is for cases that need a human ([[#Q2 — `customer_support`]]), not a default. Contrast [[#Q58 — `customer_support`]], where declining *is* correct because the topic is out of scope.

---

### Q24 — `research_pipeline`

**Correct: B — "The claim plus a reference to its source (URL or document id and location)."**

Auditability means a reader can trace any claim back to where it came from. Pairing each claim with a source pointer does that at negligible token cost, and it survives every downstream summarization step because the pointer travels *with* the claim.

**Wrong answers**

- **A. "Only the claim text, to keep messages short."** — Saves a handful of tokens and destroys auditability. Once the claim is detached from its source, the attribution cannot be reconstructed — see [[#Q28 — `research_pipeline`]] for exactly this failure in production.
- **C. "The full raw page the claim came from, inline in every message."** — Auditable but ruinous for context: the coordinator drowns in raw pages, which is the problem sub-agents exist to prevent. A pointer gives the same traceability for a fraction of the cost.
- **D. "A confidence score and nothing else."** — A number with no provenance. You can't verify a claim you can't locate, and an uncited confidence score is unfalsifiable.

**Takeaway:** Claim + source pointer is the atomic unit of an auditable research pipeline. Not the raw page, not the bare claim.

---

### Q25 — `research_pipeline`

**Correct: C — "Update the synthesis agent to render each content type appropriately—financial data as tables, news as prose."**

Different content has different natural form. Financial comparisons need tabular alignment; news needs narrative. Fix the *rendering* stage — the one place that knows it's producing an executive briefing — rather than flattening everything upstream.

**Wrong answers**

- **A. "Standardize all subagent outputs to prose summaries with inline citations."** — Destroys the structure in the financial and patent data at the source. Revenue, margins, and growth rates in prose are strictly harder to compare than in a table.
- **B. "Add a format conversion layer between subagents and synthesis that transforms all outputs to a common intermediate representation."** — Adds a component whose job is to erase the type distinctions the briefing needs. It relocates the flattening rather than removing it.
- **D. "Standardize all subagent outputs to JSON with fields for claim, evidence, source, and confidence."** — A good schema for *auditability* (see [[#Q24 — `research_pipeline`]]) but the wrong fix here: forcing news summaries into claim/evidence tuples shreds the narrative flow the briefing wants, and the synthesis agent still has to decide how to render.

**Takeaway:** Standardize *metadata* (sources, dates, confidence); preserve *content shape*. Presentation decisions belong at the presentation layer.

---

### Q26 — `customer_support`

**Correct: B — "Start a new session, inject a structured summary of the previous interaction (issue type, actions taken, resolution status), then make fresh tool calls before engaging."**

The stale `Status: PENDING` result stops being a problem once it isn't in context at all. A clean session carries forward the *durable* facts (what the dispute is, what was tried, where it stood) as a summary, and re-fetches everything volatile — no stale/fresh conflict is possible because there is nothing stale to prefer.

**Wrong answers**

- **A. "Resume with full history but filter out previous `tool_result` messages before resuming, keeping only the human/assistant turns."** — Closer than the rest, but it leaves dangling `tool_use` blocks with no matching results, and the assistant's earlier *prose* ("your refund is being processed") still asserts the stale status. You've removed the evidence and kept the conclusion.
- **C. "Resume with full history and add a system prompt instruction telling the agent to always prefer the most recent tool results when multiple calls to the same tool exist in context."** — The testing described in the question already shows the model referencing outdated data *despite* fresher results being present. A prompt instruction is the weakest possible guard against a problem you can eliminate structurally.
- **D. "Resume with full history and configure the agent to automatically re-call all previously-used tools at session start to ensure data freshness."** — Adds fresh results *next to* the stale ones, doubling the contradictory material in a 32-turn context, and wastes calls on tools irrelevant to today's question.

**Takeaway:** For returning users, carry a summary forward, not a transcript. Don't ask a prompt to out-argue stale data you could simply have dropped.

---

### Q27 — `extraction_pipeline`

**Correct: B — "Add prompt instructions to return null for any field where information is not directly stated in the source."**

The schema already permits `null`; the model just wasn't told that `null` is the *preferred* answer over a plausible guess. Making "don't know" an explicitly sanctioned output is the direct fix for grounding failures.

**Wrong answers**

- **A. "Add a post-processing step using a second LLM call to verify each extracted value exists in the source document."** — Doubles cost and latency to catch an error you can prevent at extraction time, and the verifier inherits the same hallucination risk it's meant to police.
- **C. "Make all schema fields required (non-nullable) with strict validation rules to ensure the model only outputs verifiable data."** — Exactly backwards. Removing `null` *forces* the model to invent a value for every absent field; you'd take the fabrication rate up, not down.
- **D. "Upgrade to a more capable model tier with improved instruction-following to reduce hallucination tendencies."** — There is no instruction to follow better. Fix the prompt before you pay for a bigger model.

**Takeaway:** Explicitly license "not found." An unstated permission to say nothing reads as a requirement to say something.

---

### Q28 — `research_pipeline`

**Correct: B — "Require all subagents to output structured claim-source mappings that the synthesis agent must preserve and merge when combining findings."**

Attribution is lost at the merge step, so bind claims to sources in a structure that *survives* merging. When each finding is a claim-source pair, combining findings is a set operation that carries provenance along by construction — nothing has to be reconstructed later.

**Wrong answers**

- **A. "Maintain complete transcripts of all subagent interactions and add a citation-resolution agent to analyze logs and determine attributions before report generation."** — Expensive forensic reconstruction of information you had for free upstream. Storing full transcripts is costly, and re-deriving attribution from logs is guesswork.
- **C. "Add a verification step where the report generator uses semantic similarity matching against original sources to reconstruct which claims came from which documents."** — Similarity matching *guesses* attribution. Two sources making the same point are indistinguishable, and a paraphrased claim may match the wrong document — producing confident but wrong citations, the worst outcome for an auditable report.
- **D. "Have the coordinator inject source identifier prefixes into text before each handoff, then parse these prefixes at report generation to reconstruct citations."** — String-tagging inside free text is fragile: the synthesis agent rewrites, merges, and paraphrases, and the prefixes get dropped or misattached in the process. That's the same loss you started with, now with a parser.

**Takeaway:** Provenance must be *structural*, not textual. If attribution can be lost by rewording, it will be.

---

### Q29 — `research_pipeline`

**Correct: D — "Specify research goals and quality criteria (coverage breadth, source diversity, recency) rather than procedural steps, letting the subagent determine its search strategy."**

All three symptoms trace to over-specification: the sub-agent executes your queries rather than pursuing your objective, so it has no mandate to adapt when the queries miss. Delegate the *goal* plus measurable quality criteria and the sub-agent can reformulate, handle emerging topics, and follow promising tangents — because those now serve the brief instead of violating it.

**Wrong answers**

- **A. "Remove procedural details entirely, delegating with simple goals like 'research X thoroughly' and relying on the subagent's general capabilities."** — Over-corrects. "Thoroughly" is unmeasurable, so you lose the ability to steer coverage, source diversity, or recency at all. The fix is replacing procedure with *criteria*, not with vagueness.
- **B. "Add explicit fallback directives to the detailed instructions: 'If specified searches yield fewer than N results, attempt alternative query formulations before reporting failure.'"** — Patches symptom (1) only, by adding a second layer of procedure. Emerging topics and tangential sources are untouched, and you'll be adding a new directive for each new failure mode forever.
- **C. "Implement a topic classification step where the coordinator categorizes requests as 'well-defined' or 'exploratory' and uses different instruction styles for each category."** — Adds a classifier and keeps the brittle procedural style for everything it labels "well-defined" — including topics that only *look* well-defined until the searches fail.

**Takeaway:** Delegate goals and quality bars, not step-by-step procedure. Procedural delegation caps a sub-agent at your foresight.

---

### Q30 — `customer_support`

**Correct: C — "The conversation history isn't being passed in subsequent API requests."**

The API is **stateless**. Every request must carry the full prior message list; the model has no server-side memory of the earlier turns. An agent that forgets the answer to question one by question three is the textbook signature of history not being resent.

**Wrong answers**

- **A. "The verification tool is clearing the agent's internal state after each successful validation step."** — There is no "internal state" for a tool to clear. State lives entirely in the message array you send.
- **B. "The prompt lacks instructions telling Claude to remember information across multiple exchanges."** — You cannot instruct a model to recall text that was never in its input. No prompt fixes missing history.
- **D. "Claude's memory retention is limited to two conversational turns by default, requiring explicit configuration to extend it."** — Invented mechanism. There is no turn-retention setting; the limit is the context window, and you fill it by sending the history yourself.

**Takeaway:** Statelessness is a first-principles exam fact. "The agent forgot" almost always means "the history wasn't resent."

---

### Q31 — `code_exploration`

**Correct: D — "Use `fork_session` to create two branches from yesterday's analysis, exploring one approach in each fork."**

Two explorations need the *same* starting context and must not contaminate each other. Forking gives each branch a full copy of yesterday's analysis, and they proceed independently — which is exactly what "explore both in depth before deciding" requires.

**Wrong answers**

- **A. "Resume yesterday's session to explore the first approach, then start a new session for the second, manually recreating the original context."** — Asymmetric and lossy. The second approach is evaluated against a hand-rebuilt approximation of the context the first one got for free, so the comparison isn't fair.
- **B. "Start two fresh sessions, manually providing a summary of yesterday's analysis findings to establish context."** — Discards an hour of accumulated detail in favour of a summary, when the full context is still available. The comparison is fair but both sides are impoverished.
- **C. "Resume yesterday's session and explore both approaches sequentially within the same conversation thread."** — Cross-contamination: the microservice exploration sits in context while the in-place refactor is evaluated, biasing the second analysis and inflating a single context with two full explorations.

**Takeaway:** `fork_session` = shared history, divergent futures. Reach for it whenever you want an apples-to-apples comparison from a common baseline.

---

### Q32 — `research_pipeline`

**Correct: C — "Have the coordinator analyze each query and dynamically decide which subagents to invoke based on its assessment of query requirements."**

The query distribution is "diverse and evolving," which rules out anything that has to be enumerated or trained in advance. Routing is a judgment call, and the coordinator is already reading every query — let it decide how much pipeline each one needs.

**Wrong answers**

- **A. "Implement pattern-based routing that categorizes queries by structure (single-fact vs. comparative vs. analytical) and maps each category to a predefined subagent combination."** — Static categories against an evolving distribution. Every genuinely new application lands in the wrong bucket or none, and you maintain the mapping forever.
- **B. "Create a fast-path for factual questions that bypasses subagents entirely, routing all other queries through the complete pipeline to ensure research thoroughness."** — A two-speed system for a spectrum of complexity. Everything that isn't a bare fact still pays the full 40-second pipeline, including the many mid-complexity queries that need two sub-agents rather than four.
- **D. "Train a query complexity classifier on labeled historical data to predict optimal subagent combinations, retraining periodically as query patterns evolve."** — Heavy ML machinery — labels, training, retraining cadence — to approximate a judgment the coordinator model already makes natively, and it lags the distribution by one retraining cycle by construction.

**Takeaway:** When routing depends on judgment over an open-ended input space, let the model route. Save static rules for closed, stable sets.

---

### Q33 — `extraction_pipeline`

**Correct: D — "Define a strict output schema and include format normalization rules in your prompt."**

The two levers work together: the schema fixes the output *shape* (which fields, which types), and the prompt's normalization rules fix the *conventions* ("prices as decimal numbers without currency symbols; dietary tags from this list, including icon equivalents"). Normalizing at extraction time means the model resolves the ambiguity while it can still see the source.

**Wrong answers**

- **A. "Use separate extraction calls for each field to ensure consistent handling of each type."** — Multiplies cost by the field count and destroys cross-field context: an item's price and dietary tags are understood together, from the same menu line.
- **B. "Extract data as-is and normalize formats in post-processing code after Claude returns."** — Workable for prices; fails for the icons. Once the icon is flattened to raw text the semantic information is gone, and post-processing can't recover what the model didn't interpret. You end up maintaining a growing normalization codebase per menu variant.
- **C. "Request multiple extraction attempts per document and select the most common format."** — Pays N× for a vote among outputs that are all *validly* formatted in different ways. Consensus on an arbitrary convention isn't the same as conforming to your convention.

**Takeaway:** Schema constrains structure; the prompt constrains convention. Normalize where the source is still visible.

---

### Q34 — `research_pipeline`

**Correct: B — "Give the task an explicit budget and a coverage check, and stop once the questions are answered or the budget is spent."**

Two independent stopping conditions: a *semantic* one (the research questions are answered) that ends the run when the work is genuinely done, and a *resource* one (the budget) that guarantees termination even when it isn't. That combination is what makes non-convergence impossible rather than merely unlikely.

**Wrong answers**

- **A. "Let it continue until it naturally stops."** — This is the current behaviour, and the premise says it isn't converging.
- **C. "Cut the run off at a random time."** — Bounds the cost but at an arbitrary point, so you get a truncated answer with no signal about what's missing. A budget with a coverage check tells you *why* it stopped.
- **D. "Add more sub-agents so it finishes sooner."** — More agents spawn more follow-up searches. You scale the divergence rather than bounding it.

**Takeaway:** Every agentic loop needs a termination condition it cannot argue with. Pair "am I done?" with "have I spent enough?"

---

### Q35 — `extraction_pipeline`

**Correct: D — "Add few-shot examples demonstrating extractions from documents with varied structures—showing how to identify citations in different formats and locate methodology details across section types."**

The information is present; the model just doesn't recognise it in unfamiliar arrangements. That's a *recognition* gap, and diverse few-shot examples are the direct remedy — show an inline-citation document and a bibliography document, a methodology section and a methodology buried in the introduction.

**Wrong answers**

- **A. "Implement retry logic that re-sends requests when validation detects empty required fields."** — Retrying an identical prompt on an identical document reproduces the identical failure. Nothing about the second attempt makes the bibliography easier to recognise. (Compare [[#Q49 — `extraction_pipeline`]]: retries only help when the error feedback itself adds information.)
- **B. "Build a regex-based post-processing layer that scans source documents for citation patterns and methodology keywords, populating empty fields when the model fails to extract."** — Rebuilds the hard part of the extractor in regex, and the varied-format problem that defeats the model defeats the patterns too — plus you now maintain two extraction systems that disagree.
- **C. "Modify your schema to make citations and methodology optional, and flag incomplete records for manual review rather than failing validation."** — Reclassifies the failure as acceptable. The data *is* in the document; routing 5% to humans is a workaround, not a fix.

**Takeaway:** Few-shot examples should be chosen for *variety*, not quantity — they teach recognition across the formats you actually receive.

---

### Q36 — `extraction_pipeline`

**Correct: B — "Require an ISO date in the output schema, and when the input is ambiguous, flag the field for review instead of guessing."**

`03/04/2025` genuinely is undecidable without knowing the vendor's locale. The schema forces an unambiguous output format, and the flag makes the model's uncertainty *visible* rather than silently resolved — which is the whole point when a wrong date means a wrong payment due date.

**Wrong answers**

- **A. "Assume the United States month-first format everywhere."** — Silently wrong for every non-US vendor, and wrong in a way that produces a perfectly valid-looking date. This is the exact silent error the question asks you to avoid.
- **C. "Store the date as the raw string and sort it out later."** — Defers the decision to a point where you have *less* information, and pushes unnormalized data into downstream systems that expect a date.
- **D. "Drop any date that is ambiguous."** — Destroys real data. Many ambiguous dates are resolvable later from vendor context; discarding them makes recovery impossible.

**Takeaway:** Ambiguity should surface as a flag, never as a guess. Silent plausible errors cost more than loud uncertain ones.

---

### Q37 — `code_exploration`

**Correct: D — "Expand MCP tool descriptions to detail capabilities and outputs—e.g., 'Builds dependency graph showing direct imports, transitive dependencies, and cycles.'"**

Compare the two descriptions in the question: Grep's spells out behaviour *and* return shape, while `analyze_dependencies` says "Analyzes dependency graph" and stops. The model is picking rationally on the information it has. Describe what the tool builds and returns, and it becomes the obviously better choice for dependency questions.

**Wrong answers**

- **A. "Remove Grep from available tools when the MCP server is connected to eliminate functional overlap."** — Removes an excellent general-purpose tool to force use of a poorly-described one. Grep is needed constantly for work that has nothing to do with dependencies.
- **B. "Add routing instructions to the system prompt specifying that dependency-related questions should use MCP tools rather than Grep."** — Puts tool documentation in the wrong place. It's advisory, it doesn't scale as tools are added, and it leaves the description — the thing the model actually consults when choosing — still uninformative.
- **C. "Split `analyze_dependencies` into granular tools (`list_imports`, `resolve_transitive_deps`, `detect_circular_deps`) so each has a focused purpose less likely to overlap with Grep."** — Three vague descriptions instead of one. Granularity can help, but the defect here is description quality; splitting without fixing the text just multiplies the problem.

**Takeaway:** Tool descriptions are the model's only selection input. Write them like API docs: capability, when to use, what comes back.

---

### Q38 — `code_exploration`

**Correct: B — "Analyze imports and class hierarchies to identify the base cache class, Read that file to understand the interface, then trace specific invalidation implementations."**

Structure-first. Find the abstraction that the decorators, middleware, and service classes all depend on, understand *that* interface, and the 15 files become comprehensible as implementations of it. Then follow only the invalidation path — which is what the engineer actually needs to extend.

**Wrong answers**

- **A. "Use the Read tool to sequentially load all 15 files, building complete understanding across the full caching implementation."** — ~8,000 lines with no prioritisation. It exhausts context on decorator boilerplate and configuration before reaching the invalidation logic that matters.
- **C. "Use Grep to search for 'invalidate' and 'expire' patterns across all files, then Read only those specific line ranges with minimal surrounding context."** — Cheap but ungrounded: isolated line ranges show *where* invalidation happens without the interface contract that explains *how* it's supposed to work, and any invalidation path not using those two words is missed entirely. That's a poor foundation for adding a new trigger.
- **D. "Use Glob to find files matching common caching patterns (cache.py, caching/), prioritize the largest files by reading them first, then check smaller files for gaps."** — File size is not relevance. The base class defining the interface is often small; the largest file is often generated config or an exhaustive test suite.

**Takeaway:** Find the abstraction before the instances. Interface first, implementations second, and only the ones on your path.

---

### Q39 — `code_exploration`

**Correct: D — "Use Read to load the file, add the function at the appropriate location, then Write the updated file."**

At 150 lines the whole file fits comfortably in context. Read it, place the function exactly where it belongs, and write the complete file back — no uniqueness constraint to satisfy, and full control over the insertion point.

**Wrong answers**

- **A. "Use Edit with an extremely long `old_string` capturing 30+ lines of context to guarantee uniqueness"** — Fights the tool's constraint instead of stepping around it. With repetitive docstrings and structural patterns, even 30 lines may not be unique, and a single whitespace mismatch across 30 lines fails the match.
- **B. "Use Edit's `replace_all` parameter to target a common pattern and embed the new function in the replacement text"** — Catastrophic: `replace_all` hits *every* occurrence of a pattern the question describes as repetitive. You'd insert the helper function many times over.
- **C. "Use Bash to append the function definition to the end of the file using heredoc syntax"** — Puts the function in the wrong place. The requirement is insertion *between two existing functions*, and appending also bypasses the edit-tracking path for no benefit.

**Takeaway:** When `old_string` can't be made unique, stop tuning it. Read → modify → Write is the reliable route for a small file.

---

### Q40 — `customer_support`

**Correct: B — "Tell the customer the system shows a possibly outdated status, and verify or escalate before committing to it."**

Two sources disagree and the agent can't yet tell which is right. Being transparent about the discrepancy, then verifying or escalating, is honest and gets the customer to a real answer without asserting something that may be false.

**Wrong answers**

- **A. "Report the tool value confidently as the truth."** — Overstates certainty the agent doesn't have, and directly contradicts what the customer can see on their own screen — which destroys trust fast.
- **C. "Side with whatever the customer says without checking."** — Swaps one unverified source for another and abandons the system of record entirely.
- **D. "Keep retrying the tool silently until it agrees with the customer."** — Retrying until the data says what you want is not verification, and the silence hides a real discrepancy from both the customer and your logs.

**Takeaway:** When sources conflict, say so and verify. Confident wrong answers cost more than acknowledged uncertainty.

---

### Q41 — `customer_support`

**Correct: C — "Implement a hook to intercept tool calls; when the refund process amount exceeds $500, block it and invoke human escalation."**

The requirement says the rule "cannot be left to model discretion," and a hook is the only option that removes discretion. It runs deterministically *before* the tool executes, so the 3% failure rate goes to 0% by construction — this is a control-plane problem, not a prompting problem.

**Wrong answers**

- **A. "Modify the refund tool to return an error with message 'Amount exceeds policy limit—please escalate' when threshold is exceeded."** — The runner-up, and genuinely deterministic at the *tool* layer: the refund can't be processed. But it still relies on the model to then choose to escalate rather than retry, apologise, or try a workaround — so the escalation half remains discretionary. A hook enforces both halves.
- **B. "Add few-shot examples to the prompt showing correct escalation behavior at various refund amounts ($400, $500, $600)."** — Examples shift probabilities; they don't create guarantees. You'd be trying to close a 3% compliance gap with the same class of mechanism that produced it.
- **D. "Strengthen the system prompt with emphatic language: 'CRITICAL POLICY: Refunds over $500 MUST trigger human escalation. NEVER process these directly.'"** — Capital letters are not a control. The prompt is already clear; making it louder doesn't make it deterministic.

**Takeaway:** Compliance rules belong in deterministic code — hooks, tool-layer guards, validators. Prompts express intent; hooks enforce it.

---

### Q42 — `code_exploration`

**Correct: C — "Resume the session and inform the agent which specific files changed for targeted re-analysis."**

Keeps the 9 files' worth of still-valid understanding and corrects only the 3 that actually moved. You get accuracy where it's needed and efficiency everywhere else — and naming the files explicitly means the agent knows which of its prior conclusions to distrust.

**Wrong answers**

- **A. "Resume the session without informing the agent about the changed files"** — Silent staleness. The agent keeps reasoning from a version of 3 files that no longer exists, and neither it nor the engineer will notice until a conclusion turns out to be wrong.
- **B. "Start a fresh session to ensure the agent works with current codebase state without stale assumptions"** — Correct but wasteful: it discards an hour of accurate analysis of 9 unchanged files to fix a problem confined to 3.
- **D. "Resume the session and immediately have the agent re-read all 12 previously analyzed files"** — Re-reads 9 files that didn't change, spending context to confirm what the session already knows.

**Takeaway:** On resume, reconcile the *delta*. Name what changed; keep what didn't. Same principle as [[#Q50 — `code_exploration`]].

---

### Q43 — `research_pipeline`

**Correct: A — "Require subagents to include publication or data collection dates in their structured outputs."**

The 35% and 18% figures aren't contradictory — they're a time series, and the synthesis agent can only see that if the dates travel with the numbers as structured metadata. Add the date field and the "conflict" resolves itself into growth.

**Wrong answers**

- **B. "Add a conflict resolution agent that automatically discards older data when newer data exists for the same metric."** — Throws away the 2022 baseline, which is the data point that makes the trend meaningful. It also treats a temporal series as a conflict to be settled rather than a story to be told.
- **C. "Configure the web search agent to only return results from the past 6 months."** — Cripples the pipeline for every legitimately historical research question, and doesn't even fix this case: the internal 2022 report still arrives from the document analysis agent.
- **D. "Instruct the synthesis agent to always treat the most recent data as authoritative and place older findings in a separate historical appendix."** — Structurally separates the two figures, which prevents the synthesis agent from ever connecting them into a growth narrative. It also enshrines "newest wins," which is wrong when the older source is more rigorous.

**Takeaway:** Temporal metadata converts apparent contradictions into trends. Dates are part of a finding, not decoration on it.

---

### Q44 — `code_exploration`

**Correct: A — "Have the agent maintain a scratchpad file that records key findings, referencing it for subsequent questions."**

An external file is memory that doesn't decay with the context window. Findings written down during exploration stay retrievable at minute 45 exactly as they were at minute 5, and re-reading a compact scratchpad costs a fraction of re-reading the source files.

**Wrong answers**

- **B. "Switch to a higher-capacity model tier to provide more context window space for accumulated exploration data."** — Buys a bigger bucket for the same leak. A longer session refills any window, and quality degrades as a window fills regardless of its size.
- **C. "Implement automatic context clearing every 15 minutes to ensure the agent starts with fresh, uncontaminated context."** — Guarantees the exact failure being reported. Clearing without persisting anything means the engineers re-supply context every 15 minutes by design.
- **D. "Create summaries of all source files before exploration begins, loading only these compressed representations into context."** — Summarising the whole codebase up front is expensive and premature — you don't yet know what matters — and the summaries omit precisely the details exploration turns out to need.

**Takeaway:** Persist findings outside the context window. A scratchpad is durable memory; a bigger window is only a slower leak.

---

### Q45 — `customer_support`

**Correct: B — "Acknowledge the frustration, state the policy plainly, and offer the options that do exist."**

The complete response: recognise the emotion, be honest about the constraint, and redirect to what's actually available. The customer leaves informed and with a path forward, even though the answer is no.

**Wrong answers**

- **A. "Grant the refund anyway to calm them down."** — Violates policy the agent has no authority to override, and rewards escalation in a way that doesn't survive contact with the next customer.
- **C. "Restate the policy firmly and end the conversation."** — Accurate and useless. It skips the acknowledgement and offers no alternatives, converting a hard answer into a bad experience.
- **D. "Promise to escalate without intending to."** — Deception. It defers the conflict and guarantees a worse one when nothing happens.

**Takeaway:** Empathy, honesty, alternatives. Declining well is a support skill; a firm "no" with a real option beats a soft "yes" you can't keep.

---

### Q46 — `extraction_pipeline`

**Correct: B — "Return null for that field and mark it as not found, leaving the rest of the extraction intact."**

`null` is the accurate representation of absent information, and marking it as not-found distinguishes "the document didn't say" from "the extractor missed it." The other fields were extracted correctly, so there's no reason to lose them.

**Wrong answers**

- **A. "Fill the field with a plausible value inferred from the rest of the document."** — Fabrication that passes validation. Downstream consumers can't tell an inferred value from an observed one, which is the most dangerous possible failure. (See [[#Q27 — `extraction_pipeline`]].)
- **C. "Fail the entire extraction because one field is missing."** — Discards correct data over one absent field. Absence is normal in real documents, not an exception.
- **D. "Repeat the previous record value for that field."** — Contaminates this record with an unrelated document's data — silently wrong, and the error propagates through every subsequent record.

**Takeaway:** Null is a valid answer. Partial-with-honest-gaps beats complete-with-invented-values.

---

### Q47 — `research_pipeline`

**Correct: B — "The coordinator did not include the outputs from the previous agents in the synthesis agent's prompt."**

Sub-agents don't share context. Each invocation sees only what the coordinator puts in its prompt, so if the findings aren't in there, the synthesis agent genuinely has nothing — and reports exactly that.

**Wrong answers**

- **A. "The synthesis agent's context window is not large enough to hold the combined outputs from both previous agents."** — An overflow produces a truncation or size error, not "no research findings were provided." That message means zero input, not too much.
- **C. "The subagents need to share a single API connection to enable automatic context sharing between invocations."** — No such mechanism exists. Context is passed explicitly in prompts; connections don't share state.
- **D. "The synthesis agent needs tools that can fetch results directly from the other agents' conversation histories."** — Describes an architecture that isn't how the orchestrator–worker pattern works. Workers report to the coordinator; they don't read each other's transcripts.

**Takeaway:** The coordinator is the sole communication hub. If a sub-agent lacks information, ask what the coordinator put in its prompt. Same fact as [[#Q60 — `research_pipeline`]].

---

### Q48 — `research_pipeline`

**Correct: C — "Instruct the synthesis agent to structure reports with explicit sections distinguishing well-established findings from contested ones, preserving original source characterizations and methodological context."**

The two failure modes — false confidence and mush — are both consequences of *flattening* uncertainty. Preserving each source's own characterization ("analyst estimate, methodology varies" vs. "peer-reviewed, ±$7B, 95% CI") and separating settled from contested lets the reader weigh the evidence, which is what an honest research report does.

**Wrong answers**

- **A. "Configure subagents to only report findings meeting a high-confidence threshold, filtering uncertain information before it reaches the coordinator."** — Solves inconsistency by deleting information. Genuinely contested findings — often the most decision-relevant ones — never reach the report at all.
- **B. "Implement a confidence calibration layer that normalizes subagent uncertainty expressions to standardized probability scores (0.0-1.0), then weight-average findings by their calibrated confidence."** — Fabricates false precision: "methodology varies" doesn't have a defensible numeric score. Weight-averaging $50B and $35B produces a number no source supports and destroys the methodological distinction that actually matters.
- **D. "Add a verification subagent that cross-references findings across sources, only passing claims to synthesis that are corroborated by at least two independent sources."** — Discards single-source findings, including a rigorous peer-reviewed study with no second source. Corroboration count is a poor proxy for evidential quality.

**Takeaway:** Preserve uncertainty; don't collapse or average it. Structure the report so the reader sees which claims are settled and which are contested.

---

### Q49 — `extraction_pipeline`

**Correct: D — "The model extracts 'et al.' for co-authors when the full list exists only in an external document not in the input"**

Retry-with-error-feedback works when the model *has* the information and formatted it wrongly. Here the full author list is not in the input at all, so no amount of error feedback can produce it — retrying just re-derives "et al." and burns tokens. The fix is upstream: supply the missing document.

**Wrong answers** (all three are *format* errors, which retry feedback fixes readily)

- **A. "The model extracts keywords as a nested object organized by category when the schema requires a flat array of strings"** — The keywords are all present; it's a restructuring task. A validation error naming the expected shape is enough to flatten it.
- **B. "The model extracts citation counts as locale-formatted strings ('1,234') when the schema requires integers"** — Pure type coercion of data already extracted correctly. `"1,234"` → `1234` on the next attempt.
- **C. "The model extracts dates as ISO 8601 datetime strings ('2023-03-15T00:00:00Z') when the schema requires only the date portion (YYYY-MM-DD)"** — Truncation of an already-correct value. The error message states the required format precisely.

**Takeaway:** Retries fix *representation*, never *absence*. Ask first whether the information was ever in the input.

---

### Q50 — `code_exploration`

**Correct: D — "Resume the subagent from its previous transcript and inform it about the renamed functions."**

Preserve the 47 files of accumulated analysis and patch the one thing that changed. Telling the sub-agent about the renames lets it update the affected references without re-deriving the data flows it already documented.

**Wrong answers**

- **A. "Resume the subagent from its previous transcript without mentioning the changes—the architecture understanding remains valid."** — The architecture *is* mostly still valid, but the sub-agent will keep referring to function names that no longer exist, producing recommendations that don't apply to the current tree.
- **B. "Launch a fresh subagent and include the prior transcript in the initial prompt for context."** — Pays the full token cost of the transcript to rebuild a session that already exists, and a raw 30-minute transcript is a poor prompt — verbose, and still describing the pre-rename names.
- **C. "Launch a fresh subagent with a summary of prior findings."** — Discards detail from 47 files that resuming would have kept for free. Summarising is the right move when the old context is stale or unavailable; here it's neither.

**Takeaway:** Resume + delta notification. Reserve fresh starts for when the prior context is genuinely unusable.

---

### Q51 — `extraction_pipeline`

**Correct: A — "Have the model output field-level confidence scores, then calibrate review thresholds using a labeled validation set."**

Reviewer capacity is the scarce resource, so it should go to the extractions most likely to be wrong. Field-level confidence gives a per-extraction risk signal, and calibrating it against labelled data is what turns a raw score into a defensible 20% cutoff rather than a guess.

**Wrong answers**

- **B. "Randomly sample 20% of extractions for review, using corrections to track accuracy and identify error patterns."** — Random sampling estimates the error *rate* well but catches only ~20% of actual errors, since it's blind to which records are risky. Good for measurement, poor for allocation.
- **C. "Prioritize review of all extractions where required fields are empty or explicitly marked as not found."** — Targets the wrong population. The stated failure is *semantic* errors that pass validation — "30 minutes" sitting in a quantity field. Those records are fully populated, so this filter systematically misses them.
- **D. "Review all extractions from documents with formatting anomalies such as unusual layouts or mixed content types."** — A plausible heuristic, but unvalidated: nothing establishes that layout anomalies correlate with the semantic errors observed. It also can't be tuned to fit exactly 20% capacity.

**Takeaway:** Route scarce human review by calibrated risk. Confidence scores are only useful once you've checked what they predict.

---

### Q52 — `research_pipeline`

**Correct: C — "Run a focused check that re-fetches the metric from the primary source and resolves the conflict before synthesizing."**

Two moderate-confidence figures for one metric means at least one is wrong, and the answer is knowable — go back to the primary source. A single targeted fetch is cheap relative to publishing a wrong number.

**Wrong answers**

- **A. "Average the two numbers and move on."** — Produces a figure neither source supports, and if one value is simply an error the average is guaranteed wrong.
- **B. "Take whichever sub-agent answered first."** — Response order carries no information about accuracy.
- **D. "Include both numbers in the final answer and let the reader decide."** — Defensible when a conflict is genuinely unresolvable ([[#Q48 — `research_pipeline`]]), but here it isn't — the primary source can settle it. Passing a resolvable conflict to the reader is abdication.

**Takeaway:** Resolve conflicts against the primary source when you can; surface them with full context only when you can't.

---

### Q53 — `research_pipeline`

**Correct: B — "Have the coordinator handle straightforward summarization requests directly using its existing context, reserving subagent spawning for complex analysis."**

The coordinator already holds the findings. Spawning a sub-agent means re-transferring 80K+ tokens it doesn't need to move, so the 40 seconds is pure overhead. Summarising in place removes the transfer entirely — and sub-agents stay available for work that genuinely needs isolation.

**Wrong answers**

- **A. "Pre-generate and cache summaries at multiple granularities whenever new findings accumulate."** — Speculative work on every finding update, most of it never read, and the cache goes stale the moment new findings land. Complexity for a problem that disappears if you just don't spawn.
- **C. "Enable prompt caching on the synthesis subagent to reduce the overhead of repeatedly transferring the same research findings."** — Reduces cost and time-to-first-token but keeps the spawn and the transfer. And "accumulated findings" change between requests, so the cache prefix keeps invalidating.
- **D. "Spawn the synthesis subagent with reduced context and have it request specific findings from the coordinator on-demand."** — Trades one big transfer for several round-trips, adding latency in a question about latency, and builds a request protocol to fetch data the coordinator could have used directly.

**Takeaway:** Delegate for *context isolation*, not out of habit. If the coordinator already has the data and the task is simple, doing it inline is the optimisation.

---

### Q54 — `customer_support`

**Correct: A — "Extract only return-relevant fields (items, purchase date, return window, status) from each existing order response, removing verbose details"**

Prune before you add. The task is a return request, so shipping details, payment info, and status history are dead weight — dropping them reclaims most of the context while keeping everything the conversation actually uses, making room for the two new lookups.

**Wrong answers**

- **B. "Have the model generate a natural language summary of each order's key details, replacing structured responses with prose descriptions"** — Costs an extra model call and converts precise structured values into prose that's harder to reason over and easy to garble. Field selection is lossless for the fields you keep; summarisation isn't.
- **C. "Move all tool responses to a vector database with semantic indexing, retrieving relevant portions as the conversation continues"** — Enormous infrastructure for a handful of order records in a single conversation, and semantic retrieval over structured data is unreliable — you may not get the field you need.
- **D. "Proceed with additional lookups without modifying the existing tool output context"** — Two more 40-field payloads on a context already dominated by tool output. That's the trajectory toward degradation, not away from it.

**Takeaway:** Prune stale and irrelevant tool output as the conversation moves on. Selecting fields is the cheapest, most faithful compaction available.

---

### Q55 — `research_pipeline`

**Correct: C — "Have the coordinator spawn parallel document analysis subagents, each handling a subset of precedents, then aggregate results before synthesis."**

The 12 precedents are independent, so parallel fan-out attacks the latency directly. Crucially, the coordinator stays the single hub — it spawned every sub-agent and receives every result, which is what preserves the monitoring and debugging the question explicitly requires.

**Wrong answers**

- **A. "Implement a message queue where precedent analysis tasks are processed asynchronously by a pool of worker agents."** — Achieves parallelism but interposes infrastructure between the coordinator and the workers. Tracing which task produced which finding now means correlating across queue logs — the observability cost the question warns about.
- **B. "Create a recursive agent hierarchy where analysis agents subdivide work among child agents until reaching single-precedent granularity."** — Deep nesting for a flat, already-known work list of 12 items. Each level adds hand-off overhead and distance from the coordinator, and the recursion buys nothing over a single fan-out.
- **D. "Enable the document analysis subagent to spawn its own specialized subagents dynamically when it encounters cases with many citations."** — Moves orchestration below the coordinator, which then can't see or debug the sub-sub-agents it never spawned. Keep fan-out at the level that owns observability.

**Takeaway:** Parallelize from the coordinator. Flat fan-out preserves the audit trail; nesting and queues erode it.

---

### Q56 — `extraction_pipeline`

**Correct: A — "Analyze accuracy by document type and field to verify high-confidence extractions perform consistently across all segments, not just in aggregate."**

97% is an average, and averages hide segments. If one document type or one field sits at 80% inside that high-confidence bucket, automating the bucket ships those errors straight through. Segment analysis is the check that must happen *before* you rely on the aggregate at all.

**Wrong answers**

- **B. "Compare accuracy at different confidence thresholds (85%, 90%, 95%) to find the optimal cutoff that maximizes automation while minimizing errors."** — Useful tuning, but it optimises the same aggregate metric. A better-chosen threshold on an average that conceals a weak segment still automates that segment's errors.
- **C. "Run a two-week pilot routing 25% of high-confidence extractions directly to downstream systems and monitor error reports."** — Detects the problem by shipping it. A pilot is a reasonable *later* step; as the pre-deployment check it means real bad data reaches production first.
- **D. "Verify that 97% accuracy meets requirements for all downstream systems that consume the extracted data."** — A necessary business question, but it takes the 97% at face value — which is exactly the number whose validity is in doubt.

**Takeaway:** Never automate on an aggregate you haven't decomposed. Segment by document type and field before trusting a headline accuracy figure.

---

### Q57 — `extraction_pipeline`

**Correct: D — "Add few-shot examples showing 2-3 complete input-output pairs with standardized material description formats"**

Two symptoms, one cause: the model has no reference for what a "good" materials value looks like. Worked examples define the target format *and* demonstrate that the field should be populated whenever material information appears — fixing the inconsistent phrasing and the omissions together.

**Wrong answers**

- **A. "Make the 'materials' field required instead of optional in the schema to force the model to always extract a value"** — Stops omissions by forcing fabrication when materials genuinely aren't listed, and does nothing about "cotton blend" vs. "Cotton/Polyester mix" — both satisfy a required string.
- **B. "Switch to a more capable model tier since inconsistent extraction indicates insufficient model capability"** — Diagnoses an underspecified prompt as a capability ceiling. A stronger model still has to guess your preferred format, because you haven't specified one.
- **C. "Set temperature to 0 to eliminate randomness and ensure deterministic outputs"** — Makes outputs *repeatable*, not *correct*. Temperature 0 will consistently produce whichever format the model favours per input, and different inputs still yield different conventions.

**Takeaway:** "Inconsistent output format" is almost always a missing-specification problem. Show the target with examples before reaching for model tier or temperature.

---

### Q58 — `customer_support`

**Correct: B — "Say plainly this is outside what support can advise on, and point the user to the right resource or a human."**

Legal advice on a contract dispute is outside a support agent's scope and carries real liability. Naming the boundary clearly and redirecting to somewhere that *can* help is honest and still useful.

**Wrong answers**

- **A. "Give the best legal opinion the agent can produce."** — Practising law from a support bot. Wrong or partial advice on a live dispute can cause real harm and real liability.
- **C. "Answer vaguely so the agent does not commit to anything."** — Hedged legal advice is still legal advice, now with the added risk that the user acts on a misreading of it.
- **D. "Ignore the legal part and answer something easier."** — Silently substitutes a different question. The user leaves thinking they were helped and still has an unaddressed dispute.

**Takeaway:** State scope boundaries explicitly and redirect. Compare [[#Q23 — `customer_support`]]: decline when it's genuinely out of scope, not as a default.

---

### Q59 — `customer_support`

**Correct: B — "Escalate to a human with the full history and what has been tried, so the customer does not start over."**

Three failed attempts is the "cannot make meaningful progress" trigger. The part that matters is the *handoff quality*: passing the history and the attempted fixes means the human starts where the agent stopped, and the customer doesn't re-explain everything.

**Wrong answers**

- **A. "Try the same resolution a fourth time."** — Repeating an approach that failed three times with nothing changed. This is the wasted-retry pattern from [[#Q16 — `customer_support`]] in conversational form.
- **C. "Tell the customer to open a new ticket."** — Escalation with the context deliberately discarded. The customer restarts from zero, which is the exact outcome a good handoff prevents.
- **D. "Close the conversation as resolved."** — Falsely marks an unresolved issue as done, hiding the failure from the customer and from your metrics.

**Takeaway:** Escalate with a structured handoff — what the issue is, what was tried, what's recommended. An escalation that loses context is barely better than none.

---

### Q60 — `research_pipeline`

**Correct: C — "The coordinator agent receives the web search agent's output and includes relevant findings in the prompt when invoking the document analysis agent."**

Hub-and-spoke. Sub-agents are context-isolated and never talk to each other; the coordinator collects each worker's output and decides what to include in the next worker's prompt. That single hub is what makes the system auditable and debuggable.

**Wrong answers**

- **A. "The agents communicate through an event-driven message queue, with the document analysis agent subscribing to web search completion events."** — Describes a distributed microservice architecture, not the orchestrator–worker pattern. No pub/sub channel exists between sub-agents.
- **B. "The web search agent directly invokes the document analysis agent, passing the discovered sources as parameters."** — Peer-to-peer invocation bypasses the coordinator, which then can't see or control the hand-off. (Same anti-pattern as option D in [[#Q55 — `research_pipeline`]].)
- **D. "Both agents access a shared memory store where the web search agent writes findings and the document analysis agent reads them."** — Shared mutable state between sub-agents isn't the model. Context isolation is a feature, and implicit sharing would undermine both the isolation and the audit trail.

**Takeaway:** All inter-agent information flows *through* the coordinator's prompts. If you catch yourself drawing an arrow between two workers, it's the wrong diagram.

---

## Related notes

- [[Questions]] — the question set this key answers
- [[Weak Areas Deep Dive]] — log the ones you missed here
- [[D1 - Agentic Architecture & Orchestration]] · [[D2 - Tool Design & MCP Integration]] · [[D3 - Claude Code Configuration & Workflows]] · [[D4 - Prompt Engineering & Structured Output]]
- [[Flashcards]] · [[Critical Terms Glossary]]
