---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - customer-support
date: 2026-08-28
status: done
---

# Customer Support — Unified Bank Answer Key

[← Unified Bank index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **20** `customer_support` questions in the deduplicated bank — `U2` · `U6` · `U8` · `U15` · `U16` · `U23` · `U26` · `U30` · `U40` · `U41` · `U45` · `U54` · `U58` · `U59` · `U65` · `U67` · `U68` · `U76` · `U77` · `U78`. Each entry reproduces its stem, names every sitting the item appeared in, and states how strongly its answer is verified. Numbers are **`U#`, this folder's own** — never carry them to another set.

**Answers:** **U2** A · **U6** C · **U8** B · **U15** A · **U16** A · **U23** B · **U26** B · **U30** C · **U40** B · **U41** C · **U45** B · **U54** A · **U58** B · **U59** B · **U65** B · **U67** A · **U68** C · **U76** C · **U77** A · **U78** B

**Authority:** U2 🥇 · U6 🥇 · U8 🥇 · U15 🥇 · U16 📘 · U23 🥇 · U26 🥇 · U30 🥇 · U40 🥇 · U41 🥈 · U45 🤔 · U54 🥇 · U58 🥇 · U59 🥇 · U65 🥇 · U67 🥇 · U68 🥇 · U76 📘 · U77 📘 · U78 📘
_🥇 the site's own `correct_key` · 🥈 confirmed by the site's grader · 📘 doc-verified, no grader · 🤔 reasoned only_

---

### U2 — customer_support

🅰 full MCQ · **Seen as:** `N-Q2` · `O-Q32` · `T-Q38` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> You're implementing the escalation logic for when the agent should call `escalate_to_human`. Your team proposes four different approaches for triggering escalation. Which approach will most reliably identify cases that genuinely require human intervention?

**Correct: A — "Instruct the agent to escalate when the customer requests a human, when the issue requires policy exceptions, or when the agent cannot make meaningful progress."**

These three triggers cover the genuinely distinct reasons a human is needed — explicit customer preference, authority the agent doesn't have, and capability exhaustion — and each is a judgment the model is well-placed to make from the live conversation. Escalation criteria should be stated as intent, not as a proxy signal.

**Wrong answers**

- **B. "Configure the agent to escalate after three consecutive tool calls that fail to resolve the customer's stated issue."** — Tool-call count is a proxy, not a cause. It escalates a customer whose three retries were transient network blips, and it never escalates a policy-exception request that resolves in one clean tool call.
- **C. "Implement sentiment analysis that monitors for frustration indicators (negative language, repeated questions, exclamation marks) and trigger escalation when the frustration score exceeds a configured threshold."** — Frustration and *needing a human* are different variables. A calm customer asking for a policy exception scores low and never escalates; an irritated customer with a one-click fix escalates unnecessarily.
- **D. "Build a rules engine that maps specific issue types, customer segments, and product categories to escalation decisions, removing the need for model judgment calls."** — Deliberately removing model judgment is the flaw. The rules can only enumerate issue types you already anticipated; "the agent cannot make meaningful progress" is inherently a runtime judgment no static table encodes.

**Takeaway:** Escalate on *reasons* (asked for a human, needs authority, stuck), not on *symptoms* (retry counts, tone scores). Contrast with [[#U41 — customer_support]]: a hard compliance limit is exactly where you *do* remove model discretion.

---

### U6 — customer_support

🅰 full MCQ · **Seen as:** `N-Q6` · `O-Q39` · `T-Q18` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> When the agent calls `lookup_order` and receives order details showing the item was purchased 45 days ago, how does the agentic loop determine whether to call `process_refund` or `escalate_to_human` next?

**Correct: C — "The order details are added to the conversation and the model reasons about which action to take."**

That *is* the agentic loop: the tool result is appended to the message history, the whole conversation goes back to the model, and the model decides the next step (another tool call, or a final answer). The 45-day fact only influences behaviour because the model reads and reasons over it.

**Wrong answers**

- **A. "The orchestration layer automatically routes to the next tool based on the order's status field."** — Describes a workflow engine, not an agent. Nothing in the loop inspects `status` and dispatches; the harness executes the tool the *model* asked for.
- **B. "The agent follows a pre-configured decision tree mapping order attributes to specific tool calls."** — Same category error. A decision tree is deterministic branching written by you; the agentic loop's branching is the model's reasoning at each turn.
- **D. "The agent executes the remaining steps in a tool sequence planned at the start of the request."** — Agentic loops decide **one step at a time**. There is no upfront plan being replayed — that's what lets the agent react to the 45-day discovery at all.

**Takeaway:** Tool result → appended to conversation → model reasons → next action. If you can draw the flowchart in advance, it isn't an agentic loop.

> [!WARNING] The trap you fell for
> "Pre-configured decision tree" sounds like the responsible, deterministic engineering answer, and D2/D5 items *do* often reward determinism. Distinguish the two families: deterministic **guardrails** (schemas, `isError` metadata, hooks, authorization limits) are rewarded; deterministic **replacements for model reasoning** are not. The loop reasons; guardrails constrain what the reasoning is allowed to do.

---

### U8 — customer_support

🅰 full MCQ · **Seen as:** `N-Q8` · `O-Q43` · `T-Q60` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> When implementing your `lookup_order` MCP tool, the backend sometimes returns errors (e.g., "Order not found" or temporary database failures). What is the correct pattern for communicating these errors back to the agent?

**Correct: B — "Return the error message in the tool result content with the `isError` flag set to `true`."**

This is the defined tool-result error contract. The error stays *inside* the conversation as a normal tool result, so the model sees what went wrong and can react — retry, ask the customer, or escalate — while `isError`/`is_error` marks it unambiguously as a failure rather than data.

**Wrong answers**

- **A. "Log the error server-side and return an empty result to avoid confusing the model."** — An empty result is indistinguishable from "no matching order." The model will confidently tell the customer their order doesn't exist when the truth is that the database timed out.
- **C. "Throw an exception from the tool handler so the agent framework can catch and log it."** — An uncaught exception breaks the loop rather than informing it. The model never learns the call failed and has no chance to recover.
- **D. "Return a success response with a 'status' field indicating the error type."** — Misreports a failure as a success. It may work if the model happens to read your ad-hoc field, but it defeats every framework-level behaviour keyed on the error flag, and there's no reason to invent a convention when a standard one exists.

**Takeaway:** Errors are information for the model, not just for your logs. Report them *in* the tool result, flagged as errors. See [[#U15 — customer_support]] for what those errors should actually contain.

---

### U15 — customer_support

🅰 full MCQ · **Seen as:** `N-Q15` · `O-Q38` · `T-Q20` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Production logs reveal inconsistent error handling: when `lookup_order` fails, the agent sometimes retries 5+ times (wasteful when the order ID doesn't exist), sometimes escalates immediately (premature for temporary network issues), and sometimes asks users for clarification (inappropriate when the issue is a backend permission error). Investigation shows your MCP tool returns uniform error responses: {"isError": true, "content": [{"type": "text", "text": "Operation failed"}]}. The agent cannot distinguish between error types. What's the most effective improvement?

**Correct: A — "Enhance error responses with structured metadata: `errorCategory` (transient/validation/permission), `isRetryable` boolean, and a description of what caused the failure."**

All three misbehaviours share one root cause: `"Operation failed"` carries no information to act on, so the model guesses. Structured metadata makes the right action derivable — `isRetryable: true` → retry, `permission` → escalate, `validation` → ask the customer.

**Wrong answers**

- **B. "Create an `analyze_error` MCP tool the agent calls after any failure to determine the error category and recommended action."** — An extra round-trip to recover information your server already had and deliberately threw away. It also can't classify reliably from `"Operation failed"` — there's nothing to classify.
- **C. "Implement retry logic with exponential backoff in your MCP server for all errors, returning to the agent only after retries are exhausted."** — Fixes only the transient case, and makes the others worse: a nonexistent order ID gets retried with backoff before failing, so the customer waits longer for the same dead end. Permission errors still surface as an unexplained failure.
- **D. "Add few-shot examples to the system prompt demonstrating how to interpret error message patterns and select appropriate responses for each."** — Teaches pattern-matching on a string that has no patterns. Every error is the identical `"Operation failed"`.

**Takeaway:** Design your error payloads as a machine-readable contract — category, retryability, cause. Prompting cannot recover information the tool never returned.

---

### U16 — customer_support

🅰 full MCQ · **Seen as:** `N-Q16` · `O-Q44` — **2 sittings, all agreeing**  
**Authority:** 📘 doc-verified — no grader ever saw it; two sittings agree and the reasoning is doc-cited

> Your `process_refund` tool returns two types of errors: technical errors ("503 Service Unavailable", "Connection timeout") that are transient (5% of calls), and business errors ("Order exceeds 30-day return window", "Item already refunded") that are permanent (12% of calls). Monitoring shows the agent wastes 3-4 turns retrying business errors that can never succeed. Currently, both error types return only a plain text message to Claude. What's the most effective way to reduce wasted retries while improving customer-facing response quality?

**Correct: A — "Return structured error responses with retryable: false for business errors and a customer-friendly explanation for Claude to use."**

One change solves both stated problems. `retryable: false` stops the 3–4 wasted turns on permanently-failing business errors, and the customer-friendly explanation gives Claude accurate language for *why* the refund can't happen ("this order is past the 30-day return window") instead of paraphrasing a raw error string.

**Wrong answers**

- **B. "Add few-shot examples showing how to distinguish retryable from non-retryable errors by parsing error message text."** — Makes correctness depend on the model parsing free text. Any new error phrasing from the backend silently breaks the classification, and it does nothing for response quality.
- **C. "Add a `check_refund_eligibility` tool that must be called before `process_refund` to prevent business rule violations."** — Adds a mandatory round-trip to every refund and duplicates business rules in two places that will drift. It also doesn't help with the 5% transient technical errors at all.
- **D. "Implement automatic retry logic at the tool level for technical errors only, passing business errors to Claude without retries."** — Handles the transient half well, but business errors still arrive as bare plain text. The agent may not recognise them as terminal, and the customer-facing explanation problem is untouched.

**Takeaway:** Tell the model whether an error is worth retrying *and* give it language for the customer. Structured errors do both.

---

### U23 — customer_support

🅰 full MCQ · **Seen as:** `N-Q23` · `T-Q3` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A customer asks a simple question that the agent can answer directly from the knowledge base. The agent should:

**Correct: B — "Answer the question directly and clearly, and offer escalation only if the customer needs more."**

The agent has the answer in the knowledge base. Answer it, and keep the escalation path visible without forcing it. That's first-contact resolution.

**Wrong answers**

- **A. "Escalate every question to a human to be safe."** — Defeats the purpose of the agent, burns human capacity on questions it can answer, and makes the customer wait for nothing.
- **C. "Ask the customer to confirm three times before answering."** — Manufactured friction with no risk-reduction benefit for a simple informational question.
- **D. "Give a long disclaimer and avoid answering."** — Hedging instead of helping. The customer leaves without the answer the agent was holding.

**Takeaway:** Escalation is for cases that need a human ([[#U2 — customer_support]]), not a default. Contrast [[#U58 — customer_support]], where declining *is* correct because the topic is out of scope.

---

### U26 — customer_support

🅰 full MCQ · **Seen as:** `N-Q26` · `O-Q31` · `T-Q34` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A customer returns 4 hours after their initial session about the same billing dispute. The previous 32-turn session contains `lookup_order` results showing "Status: PENDING, Expected resolution: 24-48 hours." In testing, you observe that when resuming sessions with stale tool results, the agent often references the outdated data in responses (e.g., "I see your refund is still being processed") even after subsequent fresh tool calls return different information. What approach most reliably handles returning customers?

**Correct: B — "Start a new session, inject a structured summary of the previous interaction (issue type, actions taken, resolution status), then make fresh tool calls before engaging."**

The stale `Status: PENDING` result stops being a problem once it isn't in context at all. A clean session carries forward the *durable* facts (what the dispute is, what was tried, where it stood) as a summary, and re-fetches everything volatile — no stale/fresh conflict is possible because there is nothing stale to prefer.

**Wrong answers**

- **A. "Resume with full history but filter out previous `tool_result` messages before resuming, keeping only the human/assistant turns."** — Closer than the rest, but it leaves dangling `tool_use` blocks with no matching results, and the assistant's earlier *prose* ("your refund is being processed") still asserts the stale status. You've removed the evidence and kept the conclusion.
- **C. "Resume with full history and add a system prompt instruction telling the agent to always prefer the most recent tool results when multiple calls to the same tool exist in context."** — The testing described in the question already shows the model referencing outdated data *despite* fresher results being present. A prompt instruction is the weakest possible guard against a problem you can eliminate structurally.
- **D. "Resume with full history and configure the agent to automatically re-call all previously-used tools at session start to ensure data freshness."** — Adds fresh results *next to* the stale ones, doubling the contradictory material in a 32-turn context, and wastes calls on tools irrelevant to today's question.

**Takeaway:** For returning users, carry a summary forward, not a transcript. Don't ask a prompt to out-argue stale data you could simply have dropped.

---

### U30 — customer_support

🅰 full MCQ · **Seen as:** `N-Q30` · `O-Q37` · `T-Q26` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> The agent verifies customer identity through a multi-step process before resetting passwords. During testing, you notice that after the customer answers the third verification question, the agent asks them to provide their name again, as if the earlier exchange never happened. What's the most likely cause of this behavior?

**Correct: C — "The conversation history isn't being passed in subsequent API requests."**

The API is **stateless**. Every request must carry the full prior message list; the model has no server-side memory of the earlier turns. An agent that forgets the answer to question one by question three is the textbook signature of history not being resent.

**Wrong answers**

- **A. "The verification tool is clearing the agent's internal state after each successful validation step."** — There is no "internal state" for a tool to clear. State lives entirely in the message array you send.
- **B. "The prompt lacks instructions telling Claude to remember information across multiple exchanges."** — You cannot instruct a model to recall text that was never in its input. No prompt fixes missing history.
- **D. "Claude's memory retention is limited to two conversational turns by default, requiring explicit configuration to extend it."** — Invented mechanism. There is no turn-retention setting; the limit is the context window, and you fill it by sending the history yourself.

**Takeaway:** Statelessness is a first-principles exam fact. "The agent forgot" almost always means "the history wasn't resent."

---

### U40 — customer_support

🅰 full MCQ · **Seen as:** `N-Q40` · `T-Q1` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A support agent order-status tool returns data that looks stale and contradicts what the customer sees. The agent should:

**Correct: B — "Tell the customer the system shows a possibly outdated status, and verify or escalate before committing to it."**

Two sources disagree and the agent can't yet tell which is right. Being transparent about the discrepancy, then verifying or escalating, is honest and gets the customer to a real answer without asserting something that may be false.

**Wrong answers**

- **A. "Report the tool value confidently as the truth."** — Overstates certainty the agent doesn't have, and directly contradicts what the customer can see on their own screen — which destroys trust fast.
- **C. "Side with whatever the customer says without checking."** — Swaps one unverified source for another and abandons the system of record entirely.
- **D. "Keep retrying the tool silently until it agrees with the customer."** — Retrying until the data says what you want is not verification, and the silence hides a real discrepancy from both the customer and your logs.

**Takeaway:** When sources conflict, say so and verify. Confident wrong answers cost more than acknowledged uncertainty.

---

### U41 — customer_support

🅰 full MCQ · **Seen as:** `N-Q41` · `O-Q34` — **2 sittings, all agreeing**  
**Authority:** 🥈 grader-confirmed — marked correct by the practice site's grader on the 2026-08-23 sitting

> Compliance requires that refunds exceeding $500 must automatically escalate to a human agent—this rule cannot be left to model discretion. Despite clear system prompt instructions, production logs show the agent occasionally processes high-value refunds directly (3% failure rate). How should you achieve guaranteed compliance?

**Correct: C — "Implement a hook to intercept tool calls; when the refund process amount exceeds $500, block it and invoke human escalation."**

The requirement says the rule "cannot be left to model discretion," and a hook is the only option that removes discretion. It runs deterministically *before* the tool executes, so the 3% failure rate goes to 0% by construction — this is a control-plane problem, not a prompting problem.

**Wrong answers**

- **A. "Modify the refund tool to return an error with message 'Amount exceeds policy limit—please escalate' when threshold is exceeded."** — The runner-up, and genuinely deterministic at the *tool* layer: the refund can't be processed. But it still relies on the model to then choose to escalate rather than retry, apologise, or try a workaround — so the escalation half remains discretionary. A hook enforces both halves.
- **B. "Add few-shot examples to the prompt showing correct escalation behavior at various refund amounts ($400, $500, $600)."** — Examples shift probabilities; they don't create guarantees. You'd be trying to close a 3% compliance gap with the same class of mechanism that produced it.
- **D. "Strengthen the system prompt with emphatic language: 'CRITICAL POLICY: Refunds over $500 MUST trigger human escalation. NEVER process these directly.'"** — Capital letters are not a control. The prompt is already clear; making it louder doesn't make it deterministic.

**Takeaway:** Compliance rules belong in deterministic code — hooks, tool-layer guards, validators. Prompts express intent; hooks enforce it.

---

### U45 — customer_support

🅰 full MCQ · **Seen as:** `N-Q45` — **one sitting only**  
**Authority:** 🤔 reasoned only — **the single weakest item in the bank** — one sitting, no grader, no second opinion

> A frustrated customer demands a refund that the policy does not allow. The best response is to:

**Correct: B — "Acknowledge the frustration, state the policy plainly, and offer the options that do exist."**

The complete response: recognise the emotion, be honest about the constraint, and redirect to what's actually available. The customer leaves informed and with a path forward, even though the answer is no.

**Wrong answers**

- **A. "Grant the refund anyway to calm them down."** — Violates policy the agent has no authority to override, and rewards escalation in a way that doesn't survive contact with the next customer.
- **C. "Restate the policy firmly and end the conversation."** — Accurate and useless. It skips the acknowledgement and offers no alternatives, converting a hard answer into a bad experience.
- **D. "Promise to escalate without intending to."** — Deception. It defers the conflict and guarantees a worse one when nothing happens.

**Takeaway:** Empathy, honesty, alternatives. Declining well is a support skill; a firm "no" with a real option beats a soft "yes" you can't keep.

---

### U54 — customer_support

🅰 full MCQ · **Seen as:** `N-Q54` · `O-Q45` · `T-Q33` — **3 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> Your agent has called `lookup_order` multiple times while investigating a customer's return requests. Each response includes 40+ fields (items, shipping details, payment info, status history). Tool outputs now represent the majority of the conversation's context. The customer mentions two more orders they want to discuss. What's the most effective approach before making additional lookups?

**Correct: A — "Extract only return-relevant fields (items, purchase date, return window, status) from each existing order response, removing verbose details."**

Prune before you add. The task is a return request, so shipping details, payment info, and status history are dead weight — dropping them reclaims most of the context while keeping everything the conversation actually uses, making room for the two new lookups.

**Wrong answers**

- **B. "Have the model generate a natural language summary of each order's key details, replacing structured responses with prose descriptions"** — Costs an extra model call and converts precise structured values into prose that's harder to reason over and easy to garble. Field selection is lossless for the fields you keep; summarisation isn't.
- **C. "Move all tool responses to a vector database with semantic indexing, retrieving relevant portions as the conversation continues"** — Enormous infrastructure for a handful of order records in a single conversation, and semantic retrieval over structured data is unreliable — you may not get the field you need.
- **D. "Proceed with additional lookups without modifying the existing tool output context"** — Two more 40-field payloads on a context already dominated by tool output. That's the trajectory toward degradation, not away from it.

**Takeaway:** Prune stale and irrelevant tool output as the conversation moves on. Selecting fields is the cheapest, most faithful compaction available.

---

### U58 — customer_support

🅰 full MCQ · **Seen as:** `N-Q58` · `T-Q19` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A user asks a support agent for specific legal advice about a contract dispute. The right behavior is to:

**Correct: B — "Say plainly this is outside what support can advise on, and point the user to the right resource or a human."**

Legal advice on a contract dispute is outside a support agent's scope and carries real liability. Naming the boundary clearly and redirecting to somewhere that *can* help is honest and still useful.

**Wrong answers**

- **A. "Give the best legal opinion the agent can produce."** — Practising law from a support bot. Wrong or partial advice on a live dispute can cause real harm and real liability.
- **C. "Answer vaguely so the agent does not commit to anything."** — Hedged legal advice is still legal advice, now with the added risk that the user acts on a misreading of it.
- **D. "Ignore the legal part and answer something easier."** — Silently substitutes a different question. The user leaves thinking they were helped and still has an unaddressed dispute.

**Takeaway:** State scope boundaries explicitly and redirect. Compare [[#U23 — customer_support]]: decline when it's genuinely out of scope, not as a default.

---

### U59 — customer_support

🅰 full MCQ · **Seen as:** `N-Q59` · `T-Q36` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> An agent has tried three times to resolve a billing issue and the customer is still stuck. The right next step is to:

**Correct: B — "Escalate to a human with the full history and what has been tried, so the customer does not start over."**

Three failed attempts is the "cannot make meaningful progress" trigger. The part that matters is the *handoff quality*: passing the history and the attempted fixes means the human starts where the agent stopped, and the customer doesn't re-explain everything.

**Wrong answers**

- **A. "Try the same resolution a fourth time."** — Repeating an approach that failed three times with nothing changed. This is the wasted-retry pattern from [[#U16 — customer_support]] in conversational form.
- **C. "Tell the customer to open a new ticket."** — Escalation with the context deliberately discarded. The customer restarts from zero, which is the exact outcome a good handoff prevents.
- **D. "Close the conversation as resolved."** — Falsely marks an unresolved issue as done, hiding the failure from the customer and from your metrics.

**Takeaway:** Escalate with a structured handoff — what the issue is, what was tried, what's recommended. An escalation that loses context is barely better than none.

---

### U65 — customer_support

✍️ open-response · **Seen as:** `O-Q33` · `T-Q16` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> After investigating a billing dispute over 25+ turns, you've identified that duplicate charges occurred due to a payment gateway timeout triggering retry logic. The required refund ($847) exceeds your $500 authorization limit. You need to call `escalate_to_human`, and the human agent won't have access to your conversation transcript. What context should you pass to enable effective resolution?

**Correct: B — "A structured summary: customer ID, root cause, refund amount, and recommended action."**

**Why it's correct:** Effective agent handoffs forward synthesized findings, not raw history—Anthropic's orchestrator patterns emphasize passing each stage a clear objective and the relevant distilled output rather than replaying everything [1][5]. A structured brief (identifiers + diagnosis + amount + recommendation) lets the human act immediately without re-running the 25-turn investigation, and the explicit customer ID/recommended action make it actionable.

**Why the others are wrong:**
- **A:** Verbatim complaint plus raw `tool_result` excerpts hands over artifacts without synthesis, forcing the human to redo the diagnosis you already completed.
- **C:** The full transcript buries the answer; the human must wade through 25 turns instead of reading a one-screen brief.
- **D:** Diagnosis + amount only omits the customer identifiers and the recommended action, so the human literally cannot locate or action the case.

**Key takeaway:** Escalation context should be a structured, self-contained brief (who / root cause / amount / recommended action)—synthesis over raw replay.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [5] https://www.anthropic.com/engineering/multi-agent-research-system

---

### U67 — customer_support

✍️ open-response · **Seen as:** `O-Q40` · `T-Q23` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A customer sends: "This is frustrating. I've explained my issue twice and nothing is being resolved. I want to talk to a real person NOW." The agent has not yet called any tools to investigate their account. What should the agent do?

**Correct: A — "Acknowledge the frustration and ask one targeted question to understand the specific issue before escalating."**

**Why it's correct:** The agent has zero account context yet, so a single empathetic, focused question gives a genuine shot at first-contact resolution without dismissing the customer or stalling a possible handoff [4]. This balances customer respect with the agent's resolution capability—the recommended support posture when investigation hasn't started.

**Why the others are wrong:**
- **B:** Listing capabilities at an already-frustrated customer who asked for a human reads as dismissive.
- **C:** Escalating with zero tool context produces a cold handoff where the human also starts from scratch.
- **D:** Silently running `get_customer`/`lookup_order` first adds latency and ignores the customer's request; one acknowledging question is faster and more respectful.

**Key takeaway:** With no context yet, one empathetic, targeted question beats both immediate escalation and silent investigation—respect the feeling while attempting fast resolution.

**Sources:** [4] https://platform.claude.com/docs/en/about-claude/use-case-guides/customer-support-chat

> [!WARNING] Two traps stacked in one item
> D is tempting because U65 rewards escalating *with structured context* — so "gather context, then escalate" pattern-matches to a rule you actually know. The difference is **sequencing**: U65 has 25 turns of investigation already done. Here you have zero. Context you haven't scoped isn't context, it's noise.
>
> The second trap is that A doesn't escalate at all, which feels like ignoring an explicit request for a human. It isn't — asking one question then escalating still honours it. Compare **U2**: "customer requests a human" *is* a valid escalation trigger. Both are true. One clarifying question does not override the request; skipping straight to a blind handoff wastes the human's time too.

---

### U68 — customer_support

✍️ open-response · **Seen as:** `O-Q42` · `T-Q41` — **2 sittings, all agreeing**  
**Authority:** 🥇 grader-authoritative — the site's own `correct_key`, read off the 2026-08-24 review page

> A customer raises three separate issues during one session: a refund inquiry (turns 1-15), a subscription question (turns 16-30), and a payment method update (turns 31-45). At turn 48, the customer asks "What happened with my refund?" The conversation is approaching context limits. What strategy best maintains the agent's ability to address all issues throughout the session?

**Correct: C — "Summarize earlier turns into a narrative description, preserving full message history only for the active issue."**

**Why it's correct:** Progressive/server-side compaction is Anthropic's primary strategy for long-running conversations: condense stable, resolved topics into a summary while preserving the active thread verbatim [2]. This keeps the refund details (turns 1–15) available in compressed form while staying under the context limit—the canonical multi-issue long-conversation pattern.

**Why the others are wrong:**
- **A:** A separate structured "context layer" adds engineering and doesn't preserve the conversational narrative the customer expects.
- **B:** On-demand re-fetching addresses data freshness, not the context-length problem, and loses what was actually said between customer and agent.
- **D:** A fixed 30-turn sliding window silently drops turns 1–15—exactly the refund the customer is asking about.

**Key takeaway:** For long multi-topic sessions, compact resolved threads into summaries and keep the active thread verbatim—don't blindly slide a fixed window.

**Sources:** [2] https://platform.claude.com/docs/en/build-with-claude/context-windows

> [!WARNING] "Structured data" is not always the right answer
> It is the right answer for **provenance and attribution** (U28, U70, U24) and for **cross-agent handoff** (U65, U71), where the receiver is a machine or a stranger with no transcript. It is the *wrong* answer for **in-session context pressure**, where the fix is summarize-and-keep-active. Ask what's scarce: fidelity across a boundary → structure it; window space in one conversation → compact it.

---

### U76 — customer_support

✍️ open-response · **Seen as:** `O-Q35` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> During a billing dispute, `get_customer` and `lookup_order` succeed but `process_refund` returns a timeout. The agent can explain charges and verify eligibility but can't process the refund. What best balances first-contact resolution with error handling?

**Correct: C — "Explain the billing, confirm eligibility, acknowledge the system issue blocking immediate processing, and offer escalation or retry-later."**

**Why it's correct:** This is graceful degradation: deliver all the value you can (explanation + eligibility), be transparent about the failure, and preserve customer choice. The customer-support guide frames the agent's job as resolving what it can and handing off cleanly when it cannot, rather than stalling or over-promising [4]. A timeout is transient, so honesty plus options beats both silent retry loops and premature escalation.

**Why the others are wrong:**
- **A:** Escalating immediately punts a transient backend blip to a human when the agent can still deliver explanation, eligibility, and options.
- **B:** Open-ended exponential-backoff retries trap the customer waiting on an outage that may never clear—poor UX.
- **D:** Confirming a refund that didn't actually process misleads the customer—worse than the original timeout.

**Key takeaway:** On partial failure, degrade gracefully—deliver the value you can, be honest about what failed, and let the customer choose the next step.

**Sources:** [4] https://platform.claude.com/docs/en/about-claude/use-case-guides/customer-support-chat

---

### U77 — customer_support

✍️ open-response · **Seen as:** `O-Q36` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> A customer says they've been going "back and forth for days" and "just want to speak to someone who can actually help." `lookup_order` confirms the return is within policy and immediately processable. What should the agent do?

**Correct: A — "Acknowledge the frustration, tell them it's resolvable now, and offer to complete it or escalate."**

**Why it's correct:** The agent already has the dispositive fact (resolvable now), so the best move honors the emotion, surfaces the fast path in writing, and preserves the customer's choice—the core customer-respect posture in Anthropic's support-agent guidance [4]. It leverages the agent's capability without overriding the customer's stated wish.

**Why the others are wrong:**
- **B:** Escalating immediately queues a customer behind a human handoff when resolution is one tool call away—adds delay to a simple case.
- **C:** Unilaterally processing the refund after the customer explicitly asked to speak to someone overrides their stated preference.
- **D:** Interrogating a frustrated customer about prior failures is the opposite of the empathy they requested.

**Key takeaway:** When you can resolve instantly, acknowledge the feeling, state the fast path, and keep the choice with the customer—don't queue and don't override.

**Sources:** [4] https://platform.claude.com/docs/en/about-claude/use-case-guides/customer-support-chat

---

### U78 — customer_support

✍️ open-response · **Seen as:** `O-Q41` — **one sitting only**  
**Authority:** 📘 doc-verified — from the docs-cited Mock Exam key; no grader confirmation

> Mid-dispute, after `get_customer` and `lookup_order`, the agent finds a promotional-pricing error requiring manager approval—beyond its authorization. How should the workflow handle this mid-process escalation?

**Correct: B — "Compile a structured handoff (customer details, order info, identified issue) before calling `escalate_to_human`."**

**Why it's correct:** Same handoff principle as U65: forward synthesized, identifier-rich context so the human can act immediately, consistent with Anthropic's orchestration guidance on passing distilled outputs between stages [1][5]. The brief captures who, which order, what issue, and why it exceeds authorization.

**Why the others are wrong:**
- **A:** Passing only the original message drops the tool-derived context just gathered, forcing the human to re-investigate.
- **C:** Attempting the refund despite knowing it exceeds authorization is a deliberate policy violation, not a safe "let the system catch it" move.
- **D:** Persisting the full history to a DB and passing a reference ID adds infrastructure and an extra lookup when an inline structured brief is simpler and faster.

**Key takeaway:** Mid-process escalation = inline structured brief (customer + order + issue + why escalating), not a raw dump and not a policy-violating attempt.

**Sources:** [1] https://www.anthropic.com/engineering/building-effective-agents · [5] https://www.anthropic.com/engineering/multi-agent-research-system

---

**Back to:** [README.md](../README.md) · [Questions.md](../Questions.md)