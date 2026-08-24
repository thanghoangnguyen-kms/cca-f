---
tags:
  - CCA-F
  - practice-exam
  - answer-key
  - code-exploration
date: 2026-08-23
status: done
---

# Code Exploration — New Mock Exam Answer Key

[← New Mock Exam index](../README.md) · [Questions](../Questions.md)

> [!NOTE] Scope
> The **15** questions tagged `code_exploration` in [Questions.md](../Questions.md). Numbers are the **sitting's original numbering**, so they interleave with the other three domains and are not contiguous — `Q1` here is `Question 1` there. All `[[#Q…]]` cross-references in this file point to other entries **within this file**.

**Answers:** **Q1** C · **Q7** B · **Q9** B · **Q10** B · **Q11** B · **Q12** D · **Q14** B · **Q20** C · **Q31** D · **Q37** D · **Q38** B · **Q39** D · **Q42** C · **Q44** A · **Q50** D

**Verification:** Q1 ✅ · Q7 ✅ · Q9 ✅ · Q10 ✅ · Q11 📘 · Q12 ✅ · Q14 ✅ · Q20 ✅ · Q31 ✅ · Q37 ✅ · Q38 ✅ · Q39 ✅ · Q42 ✅ · Q44 📘 · Q50 📘
_✅ confirmed by the practice site's grader · 📘 matches the doc-verified key in `CyberSkill CCAF - Mock Exam/` · 🤔 reasoned judgment, not yet confirmed_

---

### Q1 — code_exploration

**Correct: C — "Summarize key rendering findings, then spawn a sub-agent for physics exploration with that summary in its initial context."**

The symptom — generic "typical rendering patterns" instead of the `VulkanPipeline`/`FrameGraph` specifics it actually read — is **context degradation**: the concrete detail has been squeezed out. The fix has two halves, and C is the only option with both: **compact** what matters into a durable summary, then **isolate** the new exploration in a fresh sub-agent context that is seeded with that summary.

**Wrong answers**

- **A. "Spawn a sub-agent to explore physics independently, then manually synthesize its findings with the rendering knowledge accumulated in the main conversation."** — It isolates the physics work but synthesizes against the *degraded* main context. You'd be merging fresh physics findings into rendering knowledge that has already gone fuzzy, which is precisely the thing that's broken.
- **B. "Continue in the current context with more targeted prompts referencing the specific classes by name."** — Naming `VulkanPipeline` in the prompt doesn't restore the detail; it just asks a saturated context to try harder. Prompt phrasing cannot fix a context-window problem.
- **D. "Use /clear to reset context completely, then start fresh with physics exploration using file paths from the project's CLAUDE.md."** — `/clear` throws away 25 minutes of rendering discovery with nothing captured first. The engineer's question is explicitly about how physics *integrates with rendering*, so the rendering knowledge is required, not disposable.

**Takeaway:** Summarize *before* you isolate. Compaction preserves the findings; sub-agents preserve the context budget.

---

### Q7 — code_exploration

**Correct: B — "Read the entry points and project structure, then search for the area the feature touches."**

Progressive, targeted exploration: establish a cheap high-level map, then narrow to the specific region using search. You load only what you need, in the order that makes each next read informed.

**Wrong answers**

- **A. "Load every file into context so nothing is missed."** — Exhausts the context window on a repository where the overwhelming majority of files are irrelevant, and degrades reasoning about the files that do matter.
- **C. "Start editing the first file that looks related."** — Editing before understanding. "Looks related" is a guess, and the cost of a wrong guess is a broken change plus wasted context.
- **D. "Ask the user to explain every file."** — Offloads onto the engineer the exact work the agent has tools to do, and doesn't scale past a handful of files.

**Takeaway:** Orient → narrow → read. Structure first, grep second, full reads last.

---

### Q9 — code_exploration

**Correct: B — "Confirm in the current code where the auth check actually runs, then make the change there."**

Documentation is a claim about the code; the code is the code. Before a security-relevant edit, verify the claim against the current source — READMEs drift silently as modules get refactored.

**Wrong answers**

- **A. "Trust the README and edit the module it names."** — Acting on an unverified secondary source. If the check moved, you patch a dead path and leave the live one open — the worst possible outcome for an auth change.
- **C. "Search the commit history for the original author and ask them."** — Slow, dependent on someone's availability, and still second-hand. The answer is sitting in the working tree.
- **D. "Assume the check moved and search at random."** — Replaces one unverified assumption with another, minus a strategy.

**Takeaway:** Docs are a hypothesis. Grounding a change means confirming it in the code you're about to edit.

---

### Q10 — code_exploration

**Correct: B — "Search the codebase for all references first, then plan the change across the call sites."**

Enumerate the blast radius before you touch anything. The reference list *is* the change plan, and it lets you spot the hard cases (dynamic dispatch, re-exports, tests, string references) while it's still cheap to adjust the approach.

**Wrong answers**

- **A. "Rename it and run the build to see what fails."** — Compile-and-see only finds statically resolved references. Reflection, dynamic imports, string-based lookups, config, and docs all pass the build and break at runtime — and in a dynamically typed codebase there may be no build to fail at all.
- **C. "Rename only the definition and assume callers will adapt."** — Ships a knowingly broken codebase; callers do not adapt on their own.
- **D. "Add a second function and leave the old one untouched."** — Doesn't perform the rename; it creates duplicate implementations that will drift.

**Takeaway:** Grep first, edit second. The set of call sites is the plan, and it catches the references a compiler never will.

---

### Q11 — code_exploration

**Correct: B — "Spawn subagents to investigate specific questions (e.g., 'find all test files for payment processing', 'trace refund flow dependencies') while the main agent coordinates findings and preserves high-level understanding."**

Sub-agents each get their own context window, so the expensive file-reading happens *outside* the main context and only compact answers come back. The main agent keeps the high-level model of the module and stays coherent — and the sub-tasks are independent, so they can run in parallel.

**Wrong answers**

- **A. "Document all current findings in a summary report, clear context completely, then use that report as the sole reference for continuing the investigation."** — "Sole reference" is the flaw. Once the context is cleared, anything the summary omitted is unrecoverable without re-reading, and the investigation is only 8 of 45 files in — far too early to freeze what matters.
- **C. "Clear context with /clear, then selectively re-read only the most critical files discovered so far, writing key findings to a scratchpad file that persists between context resets."** — The scratchpad is a genuinely good technique (see [[#Q44 — code_exploration]]), but here it's bolted to a reset-and-re-read cycle that pays for the same files twice and still funnels all 45 files through one context. Sub-agents avoid the re-reading entirely.
- **D. "Switch to using Grep to search for specific function names instead of reading full files, reducing the content loaded into context for remaining exploration."** — Grep is cheaper per query, but "identify untested code paths" requires understanding control flow, not locating names. It slows the burn rate without changing the fact that one context must hold everything.

**Takeaway:** When the work exceeds one context window, delegate the reading and keep the synthesis. Sub-agent context isolation is the lever.

---

### Q12 — code_exploration

**Correct: D — "Enhance the MCP tool descriptions to explain when each tool is preferable to text manipulation and clarify expected inputs and outputs."**

Tool selection is driven by the tool description — it's the only thing the model sees when choosing. "`extract_function`: extracts a function from code" gives it no reason to prefer that over `Write`, which it already understands well. Fix the input to the decision, and the decision fixes itself.

**Wrong answers**

- **A. "Implement a request classifier that detects refactoring intent and automatically routes those requests to the MCP server before the agent processes them."** — Builds a whole routing layer to work around a one-line documentation gap, and hard-codes intent detection that the model already does natively.
- **B. "Remove the Write tool from the agent's configuration for refactoring sessions so it must use the MCP tools for code modifications."** — Coercion by deprivation. It cripples the agent for every legitimate `Write` need in the session, and the model still doesn't know *which* refactoring tool fits — it just has fewer escape hatches.
- **C. "Accept this as expected behavior since simpler tools like sed are more predictable than specialized refactoring tools."** — Gives up on the investment. `sed`-based refactoring is *less* reliable than AST-aware tooling, not more.

**Takeaway:** A tool the model won't pick is a description problem. Say what the tool does, when to prefer it, and what it returns. Same lesson as [[#Q37 — code_exploration]].

---

### Q14 — code_exploration

**Correct: B — "Search within the file for the function and read only that region and its immediate dependencies."**

Targeted reads. You get the function plus the context needed to understand it (imports, helpers it calls) without paying for thousands of unrelated lines.

**Wrong answers**

- **A. "Read the entire file into context to be thorough."** — Thoroughness measured in tokens. A few thousand lines of unrelated code crowds out the reasoning space for the part you actually care about.
- **C. "Read the first few hundred lines and stop."** — Arbitrary truncation. The target function is very likely not in the first few hundred lines, and you'd have no way to know.
- **D. "Reformat the file so it is easier to scan."** — Modifies source you weren't asked to change, produces a noisy diff, and doesn't reduce the amount you have to read.

**Takeaway:** Read regions, not files. Search for the anchor, then expand outward only as far as comprehension requires.

---

### Q20 — code_exploration

**Correct: C — "Use Grep to find authentication entry points, read those files, then follow imports and function calls to map the auth flow incrementally."**

Grounded, incremental tracing. Each read is chosen by what the previous read revealed, so context is spent only on files that are provably on the auth path — the right approach for 800+ files with a finite window.

**Wrong answers**

- **A. "Read any CLAUDE.md and README files first, then ask the engineer to specify which 10-15 files are most important for understanding the auth system."** — The engineer *just joined* and is asking the agent precisely because they don't know which files matter. Reading `CLAUDE.md` first is fine; making the newcomer pick the file list inverts the request.
- **B. "Launch parallel subagents to explore different services simultaneously, then synthesize their findings into an architectural overview."** — Superficially attractive, but auth flow is *cross-cutting*: it threads through services via shared middleware and token passing. Partitioning by service cuts the flow at every boundary, and each sub-agent sees only its fragment.
- **D. "Read all files containing 'auth', 'login', 'permission', or 'token' in their content or filename."** — Keyword-matched bulk reading in an 800-file codebase pulls in an enormous, mostly irrelevant set (every file that logs a token, every test fixture) while missing auth logic that doesn't use those words.

**Takeaway:** Grep to find the door, read to understand the room, follow the imports to map the building.

---

### Q31 — code_exploration

**Correct: D — "Use `fork_session` to create two branches from yesterday's analysis, exploring one approach in each fork."**

Two explorations need the *same* starting context and must not contaminate each other. Forking gives each branch a full copy of yesterday's analysis, and they proceed independently — which is exactly what "explore both in depth before deciding" requires.

**Wrong answers**

- **A. "Resume yesterday's session to explore the first approach, then start a new session for the second, manually recreating the original context."** — Asymmetric and lossy. The second approach is evaluated against a hand-rebuilt approximation of the context the first one got for free, so the comparison isn't fair.
- **B. "Start two fresh sessions, manually providing a summary of yesterday's analysis findings to establish context."** — Discards an hour of accumulated detail in favour of a summary, when the full context is still available. The comparison is fair but both sides are impoverished.
- **C. "Resume yesterday's session and explore both approaches sequentially within the same conversation thread."** — Cross-contamination: the microservice exploration sits in context while the in-place refactor is evaluated, biasing the second analysis and inflating a single context with two full explorations.

**Takeaway:** `fork_session` = shared history, divergent futures. Reach for it whenever you want an apples-to-apples comparison from a common baseline.

---

### Q37 — code_exploration

**Correct: D — "Expand MCP tool descriptions to detail capabilities and outputs—e.g., 'Builds dependency graph showing direct imports, transitive dependencies, and cycles.'"**

Compare the two descriptions in the question: Grep's spells out behaviour *and* return shape, while `analyze_dependencies` says "Analyzes dependency graph" and stops. The model is picking rationally on the information it has. Describe what the tool builds and returns, and it becomes the obviously better choice for dependency questions.

**Wrong answers**

- **A. "Remove Grep from available tools when the MCP server is connected to eliminate functional overlap."** — Removes an excellent general-purpose tool to force use of a poorly-described one. Grep is needed constantly for work that has nothing to do with dependencies.
- **B. "Add routing instructions to the system prompt specifying that dependency-related questions should use MCP tools rather than Grep."** — Puts tool documentation in the wrong place. It's advisory, it doesn't scale as tools are added, and it leaves the description — the thing the model actually consults when choosing — still uninformative.
- **C. "Split `analyze_dependencies` into granular tools (`list_imports`, `resolve_transitive_deps`, `detect_circular_deps`) so each has a focused purpose less likely to overlap with Grep."** — Three vague descriptions instead of one. Granularity can help, but the defect here is description quality; splitting without fixing the text just multiplies the problem.

**Takeaway:** Tool descriptions are the model's only selection input. Write them like API docs: capability, when to use, what comes back.

---

### Q38 — code_exploration

**Correct: B — "Analyze imports and class hierarchies to identify the base cache class, Read that file to understand the interface, then trace specific invalidation implementations."**

Structure-first. Find the abstraction that the decorators, middleware, and service classes all depend on, understand *that* interface, and the 15 files become comprehensible as implementations of it. Then follow only the invalidation path — which is what the engineer actually needs to extend.

**Wrong answers**

- **A. "Use the Read tool to sequentially load all 15 files, building complete understanding across the full caching implementation."** — ~8,000 lines with no prioritisation. It exhausts context on decorator boilerplate and configuration before reaching the invalidation logic that matters.
- **C. "Use Grep to search for 'invalidate' and 'expire' patterns across all files, then Read only those specific line ranges with minimal surrounding context."** — Cheap but ungrounded: isolated line ranges show *where* invalidation happens without the interface contract that explains *how* it's supposed to work, and any invalidation path not using those two words is missed entirely. That's a poor foundation for adding a new trigger.
- **D. "Use Glob to find files matching common caching patterns (cache.py, caching/), prioritize the largest files by reading them first, then check smaller files for gaps."** — File size is not relevance. The base class defining the interface is often small; the largest file is often generated config or an exhaustive test suite.

**Takeaway:** Find the abstraction before the instances. Interface first, implementations second, and only the ones on your path.

---

### Q39 — code_exploration

**Correct: D — "Use Read to load the file, add the function at the appropriate location, then Write the updated file."**

At 150 lines the whole file fits comfortably in context. Read it, place the function exactly where it belongs, and write the complete file back — no uniqueness constraint to satisfy, and full control over the insertion point.

**Wrong answers**

- **A. "Use Edit with an extremely long `old_string` capturing 30+ lines of context to guarantee uniqueness"** — Fights the tool's constraint instead of stepping around it. With repetitive docstrings and structural patterns, even 30 lines may not be unique, and a single whitespace mismatch across 30 lines fails the match.
- **B. "Use Edit's `replace_all` parameter to target a common pattern and embed the new function in the replacement text"** — Catastrophic: `replace_all` hits *every* occurrence of a pattern the question describes as repetitive. You'd insert the helper function many times over.
- **C. "Use Bash to append the function definition to the end of the file using heredoc syntax"** — Puts the function in the wrong place. The requirement is insertion *between two existing functions*, and appending also bypasses the edit-tracking path for no benefit.

**Takeaway:** When `old_string` can't be made unique, stop tuning it. Read → modify → Write is the reliable route for a small file.

---

### Q42 — code_exploration

**Correct: C — "Resume the session and inform the agent which specific files changed for targeted re-analysis."**

Keeps the 9 files' worth of still-valid understanding and corrects only the 3 that actually moved. You get accuracy where it's needed and efficiency everywhere else — and naming the files explicitly means the agent knows which of its prior conclusions to distrust.

**Wrong answers**

- **A. "Resume the session without informing the agent about the changed files"** — Silent staleness. The agent keeps reasoning from a version of 3 files that no longer exists, and neither it nor the engineer will notice until a conclusion turns out to be wrong.
- **B. "Start a fresh session to ensure the agent works with current codebase state without stale assumptions"** — Correct but wasteful: it discards an hour of accurate analysis of 9 unchanged files to fix a problem confined to 3.
- **D. "Resume the session and immediately have the agent re-read all 12 previously analyzed files"** — Re-reads 9 files that didn't change, spending context to confirm what the session already knows.

**Takeaway:** On resume, reconcile the *delta*. Name what changed; keep what didn't. Same principle as [[#Q50 — code_exploration]].

---

### Q44 — code_exploration

**Correct: A — "Have the agent maintain a scratchpad file that records key findings, referencing it for subsequent questions."**

An external file is memory that doesn't decay with the context window. Findings written down during exploration stay retrievable at minute 45 exactly as they were at minute 5, and re-reading a compact scratchpad costs a fraction of re-reading the source files.

**Wrong answers**

- **B. "Switch to a higher-capacity model tier to provide more context window space for accumulated exploration data."** — Buys a bigger bucket for the same leak. A longer session refills any window, and quality degrades as a window fills regardless of its size.
- **C. "Implement automatic context clearing every 15 minutes to ensure the agent starts with fresh, uncontaminated context."** — Guarantees the exact failure being reported. Clearing without persisting anything means the engineers re-supply context every 15 minutes by design.
- **D. "Create summaries of all source files before exploration begins, loading only these compressed representations into context."** — Summarising the whole codebase up front is expensive and premature — you don't yet know what matters — and the summaries omit precisely the details exploration turns out to need.

**Takeaway:** Persist findings outside the context window. A scratchpad is durable memory; a bigger window is only a slower leak.

---

### Q50 — code_exploration

**Correct: D — "Resume the subagent from its previous transcript and inform it about the renamed functions."**

Preserve the 47 files of accumulated analysis and patch the one thing that changed. Telling the sub-agent about the renames lets it update the affected references without re-deriving the data flows it already documented.

**Wrong answers**

- **A. "Resume the subagent from its previous transcript without mentioning the changes—the architecture understanding remains valid."** — The architecture *is* mostly still valid, but the sub-agent will keep referring to function names that no longer exist, producing recommendations that don't apply to the current tree.
- **B. "Launch a fresh subagent and include the prior transcript in the initial prompt for context."** — Pays the full token cost of the transcript to rebuild a session that already exists, and a raw 30-minute transcript is a poor prompt — verbose, and still describing the pre-rename names.
- **C. "Launch a fresh subagent with a summary of prior findings."** — Discards detail from 47 files that resuming would have kept for free. Summarising is the right move when the old context is stale or unavailable; here it's neither.

**Takeaway:** Resume + delta notification. Reserve fresh starts for when the prior context is genuinely unusable.

---

## Related notes

- [New Mock Exam index](../README.md) · [Questions.md](../Questions.md)
- Other domains: [Research Pipeline](research_pipeline.md) · [Customer Support](customer_support.md) · [Extraction Pipeline](extraction_pipeline.md)
- [[Weak Areas Deep Dive]] — log the ones you missed here
