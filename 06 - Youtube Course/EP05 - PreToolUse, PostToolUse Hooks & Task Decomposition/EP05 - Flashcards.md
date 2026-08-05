---
tags:
  - CCA-F
  - domain-1
  - hooks
  - task-decomposition
  - flashcards
  - youtube-course
date: 2026-08-03
status: done
domain: "1 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 05"
---

# 🃏 EP05 Flashcards — PreToolUse, PostToolUse Hooks & Task Decomposition

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP05 - PreToolUse, PostToolUse Hooks & Task Decomposition]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D1 - Agentic Architecture & Orchestration]] · [[D5 - Context Management & Reliability]] · [[EP04 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 1 — Why Prompts Can't Guarantee

**Q: A system prompt says "only process refunds under $500". Why is that a request rather than a constraint?**
A: Language models are **probabilistic** — they comply most of the time with a **non-zero failure rate**, and nothing intervenes when they don't. There is no enforcement mechanism behind a sentence in a prompt.

**Q: Besides outright disobedience, what is the subtler way a prompt rule fails? Give the refund example.**
A: **Reinterpretation of an underspecified rule.** "Don't refund over $500" doesn't say "even for verified customers", so a capable model may reason the rule targeted unverified customers and approve $2,000. It filled a gap — which is normally the behaviour you want.

**Q: Which words in an exam stem mean the answer is programmatic enforcement, never a prompt change?**
A: **"Guarantee", "must never", "policy requires", "100%", "reliably".** These rule out every prompt-based option, including "write a more explicit instruction".

**Q: A prompt-enforced limit is followed 98% of the time across 10,000 refunds/month. Why is that not compliance?**
A: **~200 violations per month.** Even across just 100 decisions, the chance of zero violations is $0.98^{100} \approx 13\%$ — so a breach is near-certain at any real volume. A hook takes the expected violations to **zero**.

**Q: Contrast a prompt instruction and a hook on four dimensions.**
A: **Nature:** probabilistic vs deterministic. **Model override:** possible vs impossible. **Expressed as:** natural language vs code. **Guarantee:** "most of the time" vs 100%.

---

## Domain 1 — Hook Placement & Timing

**Q: Put these in execution order: `PostToolUse`, tool execution, the model seeing the result, `PreToolUse`.**
A: **`PreToolUse` → tool execution → `PostToolUse` → model sees the result.**

**Q: Why can't a `PostToolUse` hook enforce a refund limit?**
A: The refund **already executed**. Enforcement isn't a do-while loop — you can't execute and then check. On an irreversible action a post-hook performs **detection**, not enforcement.

**Q: `PostToolUse` is named "post". Post relative to what — and does the model see the result before or after it runs?**
A: Post relative to the **tool**, not the model. It fires after the tool succeeds but **before the next model call**, which is exactly why it can rewrite what the model reads.

**Q: An exam stem mentions data normalization or format conversion. Which hook?**
A: **`PostToolUse`** — it intercepts tool **results**.

**Q: An exam stem mentions a policy violation, blocking, or permission. Which hook?**
A: **`PreToolUse`** — it intercepts tool **calls before execution**.

---

## Domain 1 — PreToolUse Contract

**Q: Name all four `permissionDecision` values and what each does.**
A: **`allow`** — permit the call. **`deny`** — block it. **`ask`** — escalate to the user for manual approval. **`defer`** — this hook declines to decide; normal permission flow applies.

**Q: Write the return value that blocks a tool call in a `PreToolUse` hook.**
A: `{"hookSpecificOutput": {"hookEventName": "PreToolUse", "permissionDecision": "deny", "permissionDecisionReason": "..."}}`

**Q: Three hooks fire on one call: two return `allow`, one returns `deny`. What happens, and what's the precedence order?**
A: **Blocked.** Precedence is **`deny` > `defer` > `ask` > `allow`** — any `deny` blocks the operation regardless of the others. This is what makes layered policy hooks safe to compose.

**Q: Why does omitting `permissionDecisionReason` on a deny cause a problem?**
A: The reason is delivered **to the model** so it understands why and stops retrying. Denying silently invites a retry loop.

**Q: Which `PreToolUse` field lets a hook sanitize a call instead of vetoing it, and what must it be paired with?**
A: **`updatedInput`**, which rewrites the tool's arguments before execution. Pair it with **`permissionDecision: "allow"`** to auto-approve the modified call.

**Q: How does a shell-command hook block a tool call, and where does its message go?**
A: **Exit code 2.** Stdout is ignored; **stderr** is fed back to Claude as an error message.

**Q: What does a hook return to allow an operation with no changes?**
A: An empty object — **`{}`**.

---

## Domain 1 — PostToolUse Contract

**Q: Name the `PostToolUse` field that replaces a tool's result, the one that appends to it, and the deprecated one.**
A: Replace: **`updatedToolOutput`** (any tool, both SDKs). Append: **`additionalContext`**. Deprecated: **`updatedMCPToolOutput`** (MCP tools only).

**Q: `lookup_order` returns `{"status": 3, "created": 1735689600}`. What should a hook do, and what does the model gain?**
A: Map the status code to `"delivered"` and convert the epoch to a readable date via **`updatedToolOutput`**. The model then reasons over **meaning rather than integers**, which removes a guessing surface.

**Q: What should a hook do when it fires for a tool it doesn't care about, and why does it matter?**
A: **Pass through** — return the input untouched (`{}` in the real SDK). A hook that mangles or blocks unrelated tools breaks every other tool in the loop.

---

## Domain 1 — Prerequisite Gates

**Q: Define a prerequisite gate.**
A: A **`PreToolUse` hook that tracks session state and blocks downstream tools until an upstream tool has successfully completed and set a verified flag.**

**Q: Why does a prerequisite gate need two hooks rather than one?**
A: The **authority** is pre-tool (only `PreToolUse` can block) but the **evidence** is post-tool — you only know verification succeeded *after* `get_customer` ran. So `PostToolUse` sets the flag and `PreToolUse` reads it.

**Q: In a prerequisite gate, why must the flag flip only when the upstream tool succeeded, not merely when it ran?**
A: A flag set on mere execution would let a **failed** lookup unlock the gated path — precisely the failure the gate exists to prevent. The guard is on the result: `if raw_result.get("found")`.

**Q: "Support agent occasionally calls `process_refund` before `get_customer` despite system prompt instruction. What most reliably guarantees proper ordering?"**
A: A **prerequisite gate implemented as a `PreToolUse` hook** reading a session flag. The word **"occasionally"** signals a prompt instruction is already in place and already failing.

**Q: A requirement says a refund must never run for an unverified customer AND never above $500, both guaranteed. How many hooks, and what are they?**
A: **Three.** `PostToolUse` on `get_customer` sets the verified flag on success; `PreToolUse` prerequisite gate denies without the flag; `PreToolUse` policy gate denies `amount > 500`. A call violating both is blocked — `deny` beats everything.

---

## Domain 1 — Hooks: Lecture vs Real SDK

**Q: The lecture builds hooks as arrays of functions its own loop iterates. Name three ways the real hook system differs.**
A: **Registration** — `options.hooks` keyed by event name (or `.claude/settings.json`), not a self-iterated array. **Filtering** — a declarative `matcher`, not an `if tool_name != ...` guard. **Return shape** — `hookSpecificOutput` with `permissionDecision`, not `{"allowed": bool}`. (Also the signature: `(input_data, tool_use_id, context)`.)

**Q: What is a hook `matcher`, and what does it replace?**
A: The declarative filter deciding which tools a hook fires for — `"Bash"`, `"Edit|Write"`, `"mcp__memory__.*"`. It replaces a hand-written tool-name guard inside the hook body.

**Q: Why did the lecture have to hand-build a hook mechanism at all?**
A: Its capstone runs on the **raw `anthropic` API SDK**, which has **no hook system** — hooks are a Claude Code / Agent SDK feature. The pattern transfers; the field names and registration don't.

**Q: Are `PreToolUse` and `PostToolUse` the only hook events?**
A: **No.** Claude Code defines roughly **thirty** lifecycle events; the Agent SDK exposes a large subset with Python/TS parity gaps (`PostToolBatch`, `SessionStart`, `SessionEnd`, `MessageDisplay` are TypeScript-only). Those two are the exam's core pair, not the whole surface.

**Q: How do shell-command hooks receive the event data?**
A: As **JSON on stdin** — `tool_name`, `tool_input`, `session_id`, `cwd`, `hook_event_name`, `tool_use_id`, and event-specific fields. HTTP hooks get the same JSON as the POST body.

---

## Domain 1 / 5 — Structured Handoff

**Q: An escalation reads "customer escalated, please assist." What's wrong, and what does the receiver have to do?**
A: There is **no structured handoff summary**, so the receiver must **reconstruct context from the transcript** — defeating the purpose of the loop.

**Q: What are the four required fields of a structured handoff summary?**
A: **Customer ID · root cause · action details (e.g. refund amount) · recommended action.** Priority and routing tier are useful additions that make it actionable.

**Q: Where in the code should a handoff summary be assembled, and why there?**
A: In the **hook that blocks the call**. That hook already knows the reason, the required action, and the escalation tier — so the block payload carries them rather than a bare `deny`.

**Q: The demo's escalation message was well-worded. Why is that not evidence the handoff was good?**
A: The **LLM formatted it**, so it reads nicely — but the model can only phrase the fields it was given. Good prose is not the same as a complete, self-contained payload.

---

## Domain 1 — Task Decomposition

**Q: What single question decides between prompt chaining and dynamic decomposition?**
A: **Do you know the steps before you start?** Known steps → prompt chaining. Steps depend on what you discover → dynamic decomposition.

**Q: Define prompt chaining and describe its data flow.**
A: Decomposition into a **predefined sequence** executed in fixed order, where the **output of step *n* feeds step *n+1***. Like an assembly line or a CI/CD pipeline; high control and reproducibility.

**Q: Define dynamic decomposition and what the prompt supplies instead of a step list.**
A: Steps are **generated at runtime from findings** — each result determines the next action. The prompt supplies a **starting point and a stopping condition** ("continue until you identify root cause and remediation") plus **generic** tools.

**Q: Why does prompt chaining fail on an open-ended task?**
A: **Predefined steps become wrong the moment findings diverge from the initial assumptions.** It doesn't degrade gracefully — it confidently executes a plan that stopped applying.

**Q: Which decomposition strategy fits an incident investigation, and why can't you pre-plan it?**
A: **Dynamic decomposition.** You cannot write debugging steps for a failure you haven't diagnosed — the second step depends entirely on what the first reveals.

**Q: Are prompt chaining and dynamic decomposition mutually exclusive across an architecture?**
A: **No.** Strategy is a **per-level** choice: a coordinator can chain subagents in fixed order while a single subagent decomposes dynamically internally (or vice versa).

**Q: Why is a large code review split into a per-file pass then a cross-file pass?**
A: To avoid **attention dilution** and contradictory findings from reviewing 10+ files at once. It is also a textbook prompt chain — both steps are known up front.

---

## Domain 1 — Distractor Patterns

**Q: "Add a stronger prompt instruction" appears as an option on a question asking for a guarantee. Evaluate it.**
A: **Always wrong.** A stronger instruction is still probabilistic with a non-zero failure rate. No amount of prompt emphasis produces a guarantee.

**Q: Few-shot examples are a legitimate technique. Why are they wrong as a compliance answer?**
A: Few-shot improves **consistency**, not **compliance**. You cannot enumerate enough examples to cover every case, and the failure rate stays non-zero — so it can't back a "must never" requirement.

**Q: Why is "retry on failure" wrong for an irreversible operation?**
A: It's **detection after the fact**. Once the refund fired, retrying doesn't unfire it — the control had to be pre-execution.

---

*Back to: [[EP05 - PreToolUse, PostToolUse Hooks & Task Decomposition]]*
