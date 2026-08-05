---
tags:
  - CCA-F
  - domain-2
  - tool-design
  - tool-descriptions
  - misrouting
  - flashcards
  - youtube-course
date: 2026-08-03
status: done
domain: "2 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 06"
---

# 🃏 EP06 Flashcards — Tool Descriptions & Tool Misrouting

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP06 - Tool Descriptions & Tool Misrouting]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D2 - Tool Design & MCP Integration]] · [[D1 - Agentic Architecture & Orchestration]] · [[EP05 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 2 — Why the Description Routes

**Q: What does Claude read to decide which of several tools to call?**
A: The tool's **`description`**, together with its `name`. There is no dedicated routing algorithm or scoring layer — selection is a reading-comprehension task performed on text you wrote.

**Q: Quote official documentation's ranking of the `description` field's importance among all tool-performance factors.**
A: *"Provide extremely detailed descriptions. This is **by far the most important factor** in tool performance."* Not one factor among several — the dominant one.

**Q: An agent looks up an order but never processes the refund. No error appears in any log. Why is nothing logged?**
A: A misroute **raises nothing** — it produces a plausible wrong answer, not an exception. It surfaces only as a business symptom (refunds never processed, needless escalations), unlike a hook denial which returns an explicit reason string.

**Q: Who is the audience for a tool description, and why does that framing change how you write it?**
A: **Claude, not developers.** It is routing input, not documentation — so it must state the decision criteria a model needs to pick this tool over its neighbours, rather than describing the implementation for a human reader.

**Q: A support agent has hooks, a coordinator, subagents, and four tools, and still calls the wrong tool. Which layer do you inspect first, and why that one?**
A: The **descriptions**. Tool selection is the only part of the stack with no code in it, so every other mechanism is working as written — hooks gate execution, coordinators route work between agents, neither influences which tool the model *prefers*.

---

## Domain 2 — The Description Template

**Q: Name the four parts of a production-grade tool description.**
A: **What the tool does** · **when to call it** · **what not to use it for** · **what it returns.**

**Q: What is the official minimum length for a tool description?**
A: **At least 3–4 sentences** per tool, more if the tool is complex.

**Q: The instructor calls one part of the template "negative prompting". Which part, and how do official docs phrase the same requirement?**
A: The **what not to use it for** clause. Officially it is folded into *"when it should be used (and when it shouldn't)"*, and appears in the docs' own good example as *"It will not provide any other information about the stock or company."*

**Q: A tool has a strong four-sentence `description`, but its schema properties are bare `{"type": "string"}`. What requirement is still unmet?**
A: **Per-parameter descriptions.** Official guidance includes *"what each parameter means and how it affects the tool's behavior"* — untyped, undescribed properties are what make the docs' own "poor description" example poor.

**Q: Beyond a description string, which schema construct pins a parameter to a fixed set of valid values?**
A: **`enum`** in the property's JSON Schema — e.g. `"unit": {"type": "string", "enum": ["celsius", "fahrenheit"]}`.

**Q: Read this description against the template — what job does each clause do? *"Retrieve a verified customer profile by customer ID. Returns name, email, plan tier, and account standing. Always call this first before any other tool — order lookups and refunds require customer verification."***
A: Sentence 1 = **what it does**; sentence 2 = **what it returns**; sentence 3 = **when to call** plus the downstream **constraint**. One sentence per template job.

---

## Domain 2 — Overlap & Mutual Exclusivity

**Q: Three tools are described as "Search for information", "Search documents for information", and "Analyze and extract information from content". Each reads acceptably alone. Name the defect.**
A: **Description overlap.** All three are built from *search* / *information* / *content*, so from the model's perspective they are synonyms and there is no discriminating signal to route on.

**Q: Why can't a per-tool code review catch description overlap?**
A: Because overlap is a property of the **set**, not of any member — each description is individually complete. It only appears when you read the whole `tools` array as one document and ask whether two entries could plausibly claim the same request.

**Q: State the audit question that detects description overlap.**
A: *For a given request, could two of these descriptions plausibly claim it?* If yes, at least one is under-specified.

**Q: Name the three properties that make a rewritten description set mutually exclusive.**
A: **Disjoint scope** (no request belongs to two tools) · **explicit disclaimers** naming the neighbouring tool's territory · **distinct vocabulary** so the shared words are gone.

**Q: Give the three disjoint scopes that fix the research-coordinator tool set.**
A: `search_web` = **live internet** · `search_documents` = **already-ingested corpus** · `analyze_content` = **post-retrieval processing** of content you already hold.

**Q: Two tools have genuinely disjoint scopes and specific names, but each description restates the shared verb ("search for information"). Is the set fixed?**
A: **No.** Distinct scopes and names don't help if the description washes the distinction out — the discriminating signal has to survive in the description text, which is what the model actually routes on.

**Q: What does adding "Do not use for documents already in the research corpus" to `search_web` accomplish that stating its own scope does not?**
A: It makes the boundary **explicit rather than inferred** — the description now points at the neighbouring tool's territory instead of leaving the model to deduce where its own scope ends.

**Q: Besides removing ambiguity, what ordering falls out of the search/analyze scope split for free?**
A: A **temporal** one — retrieval (live or corpus) must precede analysis, because `analyze_content` is scoped to content *already retrieved*. Sequencing comes from the scopes without naming a tool.

---

## Domain 2 — Naming, Splitting & Consolidation

**Q: Why does a tool named `search` route worse than one named `search_live_web`, holding the description constant?**
A: The name carries **no semantic weight** — `search` claims every retrieval request and specifies none, so it competes with every other retrieval tool. A name is routing text, not just a code identifier.

**Q: Give the official regex a tool `name` must match.**
A: **`^[a-zA-Z0-9_-]{1,64}$`** — letters, digits, underscore, hyphen; 1–64 characters. No spaces, no dots.

**Q: What naming pattern replaces a generic tool name? Give two examples.**
A: **Resource + access pattern.** `get_data` → `lookup_customer_profile`; `fetch_info` → `retrieve_order_history`; `search` → `search_live_web`.

**Q: Official docs say to *consolidate* related operations into fewer tools, while the lecture says to *split* generic ones. On what axis does each apply?**
A: **Split on distinct purpose** (`get_customer_by_id` vs `list_customer_orders` answer different questions and return different shapes). **Consolidate on verb** (`create_pr`/`review_pr`/`merge_pr` → one tool with an `action` parameter). Both serve the same goal: reduce selection ambiguity.

**Q: You split one tool into `create_pr`, `review_pr`, and `merge_pr`. What failure mode have you introduced?**
A: **Description overlap.** Splitting by verb on the same resource manufactures near-synonyms, so you have traded a vague tool for an ambiguous set. Group them under one tool with an `action` parameter instead.

**Q: What naming technique keeps selection unambiguous as a tool library grows across services, and where is it most critical?**
A: **Namespacing by service** — `github_list_prs`, `slack_send_message`. Officially *"especially important when using tool search"*, where definitions are discovered on demand rather than all being visible at once.

**Q: Official guidance says tool *responses* should return only high-signal information. What does that mean concretely, and how does it relate to the description?**
A: Return **semantic, stable identifiers** (slugs, UUIDs) rather than opaque internal references, and only the fields Claude needs for its next step. It is the output-side sibling of a good description: the description promises a return contract, and a bloated response breaks that promise while burning context.

---

## Domain 2 — Troubleshooting: Which Lever

**Q: An exam stem says the agent *chose the wrong tool*. Which lever, and which lever is the trap?**
A: **Description** (mutual exclusivity, *when to call*, *what not for*) plus the **name**. The trap is a `PreToolUse` hook — it can only deny, never make the model *prefer* the right tool.

**Q: An exam stem says a policy *must never* be violated. Which lever, and why not the description?**
A: A **`PreToolUse` hook** — deterministic denial. A description is a request to a probabilistic system, so it cannot deliver a guarantee.

**Q: Give the one-word test that separates a description problem from a hook problem in an exam stem.**
A: The **verb**. *Chosen* or *skipped* → description. *Blocked* or *guaranteed* → hook. *Forced every turn* → `tool_choice`. *Reformatted* → `PostToolUse`.

**Q: The right tool is called every time, but with malformed nested arguments. Which lever?**
A: **`input_examples`** plus per-parameter descriptions. This is an input-formatting problem, not a selection problem — descriptions own *which tool*, examples own *what arguments*.

**Q: List the four `tool_choice` values and the default in each condition.**
A: **`auto`** (default when `tools` are provided) · **`any`** — must call some tool · **`tool`** — must call the named one · **`none`** (default when no tools are provided).

**Q: You set `tool_choice: {"type": "any"}` and also instruct Claude to explain itself before acting. What happens to the explanation?**
A: It **never appears.** With `any` or `tool`, the API prefills the assistant message to force a tool call, so no natural-language text precedes the `tool_use` block regardless of instructions. For both, use `auto` and put the instruction in the user turn.

**Q: A tool is called with a `location` argument the user never supplied. Which mechanism explains this, and does it vary by model?**
A: **Inference of a missing required parameter** — and yes: Claude **Opus** is much more likely to notice the gap and ask, while Claude **Sonnet** may substitute a plausible value instead. The description is not the mechanism here.

**Q: You already enforce call order with a `PreToolUse` prerequisite gate. What does also cross-referencing the prerequisite in the description buy you?**
A: **Fewer wasted turns.** The hook only denies after the model chooses wrongly, so each out-of-order attempt costs a turn plus a denial reason. The description makes the right path the obvious one; the hook makes the wrong path impossible.

---

## Domain 2 — System Prompt Role Framing

**Q: A system prompt reads only "You are a customer support assistant." Which category of tool gets under-used, and why?**
A: The **action** tools — `process_refund` especially — in favour of lookups like `get_customer`. The role licenses assisting and looking things up; nothing in it licenses acting, so the framing biases selection toward a whole tool category.

**Q: Name the two additions that fix a role-only system prompt.**
A: State the **goal** (complete resolution of returns and refunds, not lookup) and write the **tool use sequence** (`get_customer` → `lookup_order` → `process_refund` → escalate last).

**Q: Official docs describe a graded dial for tool-use triggering via the system prompt. Give the three registers, weakest to strongest.**
A: *"Use your judgment about whether to call a tool or respond directly"* (conservative) → *"Use the tools to investigate before responding"* (increases tool use) → *"Always call a tool first before responding"* (pushes furthest).

**Q: Prompting raises the *tendency* to call tools. What do you use when a call must be *required*?**
A: **`tool_choice`** — officially, *"to require a tool call rather than rely on prompting, set `tool_choice`."* Prompt for a tendency, `tool_choice` for a requirement.

**Q: Three layers govern a single tool call. State what each one decides.**
A: **System prompt** → what *kind* of tool feels appropriate. **Description** → *which* specific tool claims this request. **Hook** → whether the chosen call may *run*.

---

## Domain 2 — Exam Traps & Verified Details

**Q: Stem: "A support agent frequently calls `escalate_to_human` for auto-resolvable cases. All tool definitions have single-sentence descriptions." Give the answer and the specific missing clauses.**
A: **Rewrite the tool descriptions** — add a *what not to use it for* clause to `escalate_to_human` ("do not escalate cases the agent can resolve itself") and a *when to call* clause plus the eligibility boundary to the tool that should have handled it.

**Q: Why is "add few-shot examples" wrong for a misrouting stem, given that `input_examples` is a real API field?**
A: Few-shot examples improve **format consistency**, not **selection correctness**, and `input_examples` shows Claude how to shape *arguments* for a tool it has already chosen. Neither addresses picking the wrong tool.

**Q: State the constraints on `input_examples`.**
A: Each example must validate against the tool's `input_schema` (invalid ones return **400**); **not supported on server tools**; costs roughly **20–50 tokens** for simple examples and **100–200** for complex nested objects.

**Q: The lecture says "there is no separate routing layer." What does the API in fact construct, and what does it cost on Claude Opus 5?**
A: A **tool-use system prompt** built from your tool definitions, tool configuration, and your own system prompt — **286 tokens** with `tool_choice: auto`/`none`, **406 tokens** with `any`/`tool`. The exam answer is still that the description drives selection.

**Q: Where do tool definitions sit in the rendered prompt prefix, and what are the two consequences for prompt caching?**
A: **First** — render order is `tools` → `system` → `messages`. So (1) tool definitions are the most cacheable content in the request, making detailed descriptions nearly free at ~0.1× on cache reads; and (2) **changing any tool definition invalidates the entire cache**, so batch description rewrites into one deployment.

**Q: Which of the lecture's claims about the exam's own structure should be treated as unverified?**
A: The **numbering** — that this is "sample question number two", that there are "12 official sample questions" in the syllabus, and that the multi-agent research setup is "scenario number three." The *content* of both scenarios is verified against official docs; the numbering is not, and Domain 2's exam weight percentage is not published in this vault.

*Back to: [[EP06 - Tool Descriptions & Tool Misrouting]]*
