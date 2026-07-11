# code_exploration — CCAF Study Guide

This guide covers the 15 `code_exploration` questions (Q16–Q30) from the CCAF mock exam. The domain centers on three recurring themes: (1) **tool selection is driven by tool descriptions** — the model picks tools by reading their descriptions, so weak descriptions lose to rich built-ins; (2) **context is the fundamental constraint** during long explorations — manage it with scratchpads, compaction, and subagents rather than brute-force reading; and (3) **session lifecycle** — `--resume`/`--continue` keep accumulated context, `fork_session` branches it cleanly, and you communicate deltas (changed files) rather than re-reading everything.

---

### Q16 — code_exploration
**Question:** A local MCP server exposes `analyze_dependencies` ("Analyzes dependency graph") alongside the built-in Grep ("Search file contents for a pattern using regular expressions. Returns matching lines with line numbers and surrounding context."). The server is healthy and listed, but the agent keeps using Grep for dependency questions even when users explicitly ask about "code dependencies." What's the most effective fix?

**Correct answer: D** — Expand the MCP tool descriptions to detail capabilities and outputs (e.g., "Builds dependency graph showing direct imports, transitive dependencies, and cycles").

**Why it's correct:** When you pass `tools`, the API constructs a system prompt from the tool definitions, and the model chooses tools purely from the descriptions it sees [1]. Anthropic's tool-use guidance states that providing extremely detailed descriptions is "by far the most important factor in tool performance" — explaining what the tool does, when to use it, and what it returns [1]. A one-line "Analyzes dependency graph" loses to Grep's richer, behavior-specific description, so beefing up the MCP description is the root-cause fix [1].

**Why the others are wrong:**
- **A:** Removing Grep cripples a legitimate general-purpose tool to force adoption of a specialized one, punishing every other valid Grep use case rather than fixing the actual signal the model reads.
- **B:** System-prompt routing rules can help at the margin, but they patch the symptom; the underlying cause is a thin tool description that the model is comparing unfavorably against Grep's. Fix the description first.
- **C:** Splitting into granular tools doesn't help if the new sibling tools also carry weak descriptions — the same selection bug reappears. Description quality, not granularity, is the lever here.

**Key takeaway:** The model selects tools by reading their descriptions; a specialized tool with a thin description will lose to a built-in with a rich one. Invest in detailed, behavior-specific tool descriptions before adding routing logic or removing tools.

**Sources:** [1] Tool use with Claude — Best practices for tool definitions: https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview

---

### Q17 — code_exploration
**Question:** An engineer wants all callers of a function found before removal. The function lives in a core library but is re-exposed under renamed aliases by wrapper modules (e.g., `calculateTax` → `computeOrderTax`). What exploration strategy most reliably finds all callers?

**Correct answer: A** — Read the library and wrapper modules to identify every exposed name/alias, then Grep for each name across the codebase.

**Why it's correct:** Claude Code's exploration model uses primitives like glob and grep to navigate the codebase and retrieve files just-in-time, rather than relying on a stale pre-built index [1]. Reliable caller-finding means first reading the modules to enumerate every alias the function is exposed under, then grepping for each — grounding the search in the actual code edges (re-exports and renames) instead of a single name [1][2].

**Why the others are wrong:**
- **B:** Searching for importers then reading each file misses dynamic imports, re-exports, and indirect call chains, and scales poorly across a large codebase.
- **C:** Grepping only the original name misses every caller that uses a renamed wrapper alias — a significant class of callers that would silently break on removal.
- **D:** Documentation is incomplete and frequently stale; you cannot safely delete a function based on documentation-level evidence alone.

**Key takeaway:** Before deleting code, enumerate every name a symbol is exposed under (read the wrappers/re-exports), then grep for each alias. Search must be grounded in the real code, not one assumed name.

**Sources:** [1] Effective context engineering for AI agents — Context retrieval and agentic search: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Best practices for Claude Code: https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q18 — code_exploration
**Question:** In 30+ minute exploration sessions, the agent gives inconsistent answers about code it discussed earlier, and engineers must re-supply context about already-explored modules. Most effective fix?

**Correct answer: A** — Have the agent maintain a scratchpad file recording key findings, referencing it for later questions.

**Why it's correct:** For long-horizon tasks where work exceeds the context window, Anthropic recommends structured note-taking: persisting findings to durable storage outside the context window that the agent can re-read on demand [1]. Even within a window, context pollution and relevance degradation affect models of all sizes, so a scratchpad gives the agent a stable external "memory" independent of how crowded the live context gets [1].

**Why the others are wrong:**
- **B:** A larger context window delays the problem but doesn't cure attention degradation over long, noisy contexts — Anthropic notes windows of all sizes are subject to context pollution [1].
- **C:** Automatic clearing every 15 minutes discards valid findings — recreating exactly the lost state engineers are already complaining about.
- **D:** Pre-summarizing all files upfront strips the detail that makes exploration useful and often misrepresents the very files the engineer cares about most.

**Key takeaway:** Offload durable findings to a scratchpad/notes file the agent can re-read; this is structured note-taking and it survives context degradation that a bigger window alone won't fix.

**Sources:** [1] Effective context engineering for AI agents — Context engineering for long-horizon tasks (structured note-taking): https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

---

### Q19 — code_exploration
**Question:** An engineer built up 2 hours of context yesterday in a session she named "auth-deep-dive," has since worked on three other codebases, and wants to continue that specific investigation. How should she resume?

**Correct answer: D** — Use `--resume auth-deep-dive` to load that specific session by name.

**Why it's correct:** `--resume` is designed to return to a *specific* prior session — it picks one session out of many [1]. Resuming restores full prior context: files already read, analysis already performed, decisions already made [2]. Since she named the session, resuming by that name targets it directly without hunting for an ID [1][2].

**Why the others are wrong:**
- **A:** Starting fresh and re-reading discards the 2 hours of accumulated context — the entire value of session persistence [2].
- **B:** Using `--session-id` with the UUID works but is cumbersome; she'd have to hunt for the UUID in transcript files when she already has the memorable session name [1].
- **C:** `--continue` resumes the *most recent* session in the current directory; since she's worked on three other codebases since, the most recent is not "auth-deep-dive" [1].

**Key takeaway:** `--continue` = the most recent session in this directory; `--resume` = pick a specific prior session (by name/ID). Use `--resume` when the target isn't the most recent.

**Sources:** [1] CLI reference — resume/continue flags: https://docs.claude.com/en/docs/claude-code/cli-reference — [2] Work with sessions: https://docs.claude.com/en/docs/agent-sdk/sessions

---

### Q20 — code_exploration
**Question:** After 25 minutes exploring a rendering subsystem, the agent now references "typical rendering patterns" instead of the specific `VulkanPipeline` and `FrameGraph` classes it found earlier. An engineer asks how physics integrates with rendering for collision debug overlays. Most effective approach?

**Correct answer: C** — Summarize key rendering findings, then spawn a sub-agent for physics exploration with that summary in its initial context.

**Why it's correct:** The agent is showing context degradation — losing the specific classes it discovered (context pollution over a long session) [1]. The remedy combines two of Anthropic's long-horizon techniques: compaction (distill the rendering findings into a compact summary) and multi-agent architecture (hand a fresh subagent that summary plus the physics task) [1]. The subagent works in a clean context window, escaping the degraded one while preserving the important rendering signal [1][2].

**Why the others are wrong:**
- **A:** Spawning a physics subagent but then manually synthesizing in the main conversation pushes more into the already-degrading context — the opposite of what's needed [1].
- **B:** Continuing with more targeted prompts in the same degraded context doesn't restore lost specificity; the agent is already substituting generic patterns for the real classes.
- **D:** `/clear` nukes the rendering findings entirely, but the question is a cross-cut (physics ↔ rendering) that needs the preserved rendering knowledge.

**Key takeaway:** When context degrades mid-exploration, compact what you've learned into a summary and seed a fresh subagent with it — preserve the signal, escape the polluted context.

**Sources:** [1] Effective context engineering — long-horizon tasks (compaction + multi-agent): https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Best practices for Claude Code — subagents: https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q21 — code_exploration
**Question:** An hour of context was built yesterday analyzing a legacy auth module (12 files). The session ID is valid, but version control shows 3 of the 12 files were modified overnight by a teammate's merge. What balances efficiency and accuracy?

**Correct answer: C** — Resume the session and inform the agent which specific files changed for targeted re-analysis.

**Why it's correct:** Resuming restores the full prior context — files read, analysis done, decisions made — so you keep the expensive hour of work intact [1]. You then give the agent a precise delta (the 3 changed files) so it re-reads only what's stale. This is minimum waste, maximum accuracy: keep the 9 valid files, refresh the 3 changed ones [1].

**Why the others are wrong:**
- **A:** Silent resume leaves the agent reasoning on stale content for 3 files — exactly the source of bad recommendations.
- **B:** A fresh session throws away valid context about the 9 unchanged files, redoing an hour of work for no benefit [1].
- **D:** Re-reading all 12 files wastes tokens on the 9 unchanged files without improving accuracy.

**Key takeaway:** Resume to preserve accumulated context, then tell the agent the precise delta (which files changed) so it re-reads only what's stale — not everything, not nothing.

**Sources:** [1] Work with sessions — resuming restores full prior context: https://docs.claude.com/en/docs/agent-sdk/sessions

---

### Q22 — code_exploration
**Question:** Yesterday's session identified two refactoring approaches (extract a microservice vs. refactor in-place). Today the engineer wants the agent to propose concrete code changes for *both*, in depth, before choosing. Best structure?

**Correct answer: D** — Use `fork_session` to create two branches from yesterday's analysis, exploring one approach per fork.

**Why it's correct:** Forking creates a new session that starts with a *copy* of the original's history while leaving the original unchanged — purpose-built to "try a different direction while keeping the option to go back" [1]. Two forks give each refactoring approach its own independent context starting from the identical analysis baseline, with no cross-contamination and no re-analysis [1].

**Why the others are wrong:**
- **A:** Resume one approach then start a new session for the other with manual context recreation is error-prone and loses yesterday's exact working state [1].
- **B:** Two fresh sessions with a hand-written summary redo work and risk the two diverging from the shared baseline.
- **C:** Exploring both approaches sequentially in one thread lets each approach's reasoning contaminate the other's context.

**Key takeaway:** To explore mutually exclusive alternatives from a shared baseline, `fork_session` gives each its own clean branch — independent contexts, identical starting point, original preserved.

**Sources:** [1] Work with sessions — fork creates a new session from a copy of history, original unchanged: https://docs.claude.com/en/docs/agent-sdk/sessions

---

### Q23 — code_exploration
**Question:** Caching logic spans 15 files (~8,000 lines) across decorators, middleware, and service classes. The engineer wants to add a new invalidation trigger. Most effective next step for building understanding under context constraints?

**Correct answer: B** — Analyze imports and class hierarchies to find the base cache class, Read that file to understand the interface, then trace the specific invalidation implementations.

**Why it's correct:** Claude Code navigates with glob/grep to retrieve files just-in-time rather than loading everything [1]. Starting from the architectural root (the base interface) and then following real code edges to only the invalidation-relevant implementations is focused, grounded reading at low context cost — versus flooding the window with 8,000 lines [1][2].

**Why the others are wrong:**
- **A:** Sequentially loading all 8,000 lines floods context and buries the invalidation logic in unrelated code [2].
- **C:** Grepping "invalidate"/"expire" and reading bare line ranges strips the class/method context that tells you what the invalidation actually guards.
- **D:** File size is a poor proxy for importance — you'd burn context on large utility files and miss the small, strategically central ones.

**Key takeaway:** Explore from the architectural root outward (interface → relevant implementations) following real imports, retrieving files just-in-time — not by bulk-reading or by file size.

**Sources:** [1] Effective context engineering — agentic search with glob/grep: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Best practices for Claude Code — scoping exploration: https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q24 — code_exploration
**Question:** Asked to find untested code paths in a 45-file legacy payment module, after reading 8 files the agent's answers degrade — forgetting earlier patterns, not yet locating all test files or tracing critical flows. Most effective approach to finish?

**Correct answer: B** — Spawn subagents for well-scoped investigations (e.g., "find all test files for payment processing," "trace refund flow dependencies") while the main agent coordinates and preserves the high-level overview.

**Why it's correct:** Anthropic names multi-agent architectures as a core technique for tasks whose token count exceeds a single context window: subagents explore in separate context windows, returning condensed findings while the lead agent keeps the architectural overview [1]. Claude Code best practices explicitly recommends delegating investigations to subagents to preserve the main context [2]. This is the pattern for scaling exploration beyond one window.

**Why the others are wrong:**
- **A:** A single summary report becomes the only source of truth and tends to compress away the specific code patterns you'd need later [1].
- **C:** `/clear` plus selective re-reading discards the understanding already built across 8 files; a scratchpad helps but clearing wastes valid context.
- **D:** Grepping function names instead of reading can't identify untested *paths* — you must read enough of each path to know which branches the tests miss.

**Key takeaway:** When exploration outgrows one context window, delegate well-scoped sub-investigations to subagents (fresh contexts) while the lead agent retains the overview — the multi-agent pattern for scaling.

**Sources:** [1] Effective context engineering — multi-agent architectures: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Best practices for Claude Code — use subagents to investigate: https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q25 — code_exploration
**Question:** A developer asks why an API endpoint intermittently returns 500s. The codebase has 200+ files, the involved components are unknown, and the error must be traced through routing, middleware, business logic, and database layers. Best task-decomposition approach?

**Correct answer: B** — Have the agent dynamically generate investigation subtasks based on what it discovers at each step, adapting its plan as the error path emerges.

**Why it's correct:** Anthropic's guidance favors "the simplest thing that works" and letting capable models act intelligently with progressively less rigid human curation [1]. Debugging an unknown failure is inherently adaptive — each file read changes the most useful next step, so the agent should follow the evidence rather than commit to a blind plan [1][2].

**Why the others are wrong:**
- **A:** You cannot build a correct, comprehensive plan for an unknown error without any exploration; planning blind wastes time and misses the real failure path.
- **C:** A fixed step sequence executed regardless of findings wastes work on uninvolved layers and can lock the agent out of the actual root-cause path.
- **D:** Parallel fan-out across all four layers is useful only once subtasks are well-scoped; here they aren't, so you'd pay 4× to investigate three layers that aren't the problem.

**Key takeaway:** For open-ended debugging, prefer adaptive, evidence-driven decomposition over upfront fixed plans or premature fan-out — let each discovery shape the next step.

**Sources:** [1] Effective context engineering — "do the simplest thing that works," let models act intelligently: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Building effective agents — agents handle open-ended, hard-to-predict problems: https://www.anthropic.com/engineering/building-effective-agents

---

### Q26 — code_exploration
**Question:** An exploration subagent spent 30 minutes reading 47 files and documenting data flows, then the engineer's connection dropped. Meanwhile a teammate merged a PR renaming two utility functions. The engineer wants to continue the same exploration. Most effective approach?

**Correct answer: D** — Resume the subagent from its previous transcript and inform it about the renamed functions.

**Why it's correct:** Resuming restores the full prior context — every file read, every analysis done — so the 30 minutes of accumulated understanding across 47 files is preserved [1]. Supplying a targeted delta about the two renames lets the agent update its mental model precisely. This mirrors the Q21 pattern: keep the expensive context, communicate only what changed [1].

**Why the others are wrong:**
- **A:** Resuming silently leaves the agent referencing the old function names in its recommendations — a correctness bug.
- **B:** Loading a 30-minute transcript into a *fresh* subagent's prompt is wasteful and pollutes its starting context with raw history.
- **C:** A fresh subagent with a re-summary throws away the fine-grained data flows the agent was already tracking across 47 files [1].

**Key takeaway:** Resume to keep deep accumulated understanding, then hand the agent a precise delta (the renames) rather than restarting or silently resuming on stale names.

**Sources:** [1] Work with sessions — resuming restores prior context: https://docs.claude.com/en/docs/agent-sdk/sessions

---

### Q27 — code_exploration
**Question:** An MCP server adds refactoring tools (`extract_function`, `rename_variable`, `inline_function`), each with a minimal description like "`extract_function`: extracts a function from code," but the agent still refactors via Write and Bash `sed`. Server is healthy. Most effective way to improve adoption?

**Correct answer: D** — Enhance the MCP tool descriptions to explain when each tool is preferable to text manipulation and clarify expected inputs and outputs.

**Why it's correct:** Tool selection is driven by the descriptions the model sees, and detailed descriptions are "by far the most important factor in tool performance" [1]. When `extract_function` says only "extracts a function from code" while Write/sed come with rich built-in documentation, the model prefers the better-described option. Strengthening the descriptions — including when to choose them over text manipulation and their I/O — fixes the root cause [1].

**Why the others are wrong:**
- **A:** A separate request classifier is another system to build and maintain and can misroute; the cheaper fix is making the descriptions strong enough that the agent self-selects them.
- **B:** Removing Write forces adoption by subtraction and breaks legitimate Write use cases.
- **C:** Accepting `sed` as "more predictable" capitulates and defeats the purpose of integrating the refactoring tools — the integration is fine; the descriptions are the problem.

**Key takeaway:** Specialized tools win adoption through detailed descriptions that state when to prefer them and their exact I/O — not through classifiers or by crippling general tools.

**Sources:** [1] Tool use with Claude — Best practices for tool definitions (descriptions are the most important factor): https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview

---

### Q28 — code_exploration
**Question:** After analyzing a service module (23 files, request flows, error patterns), a developer wants to independently develop two testing strategies — E2E with mocked services vs. snapshot tests — to compare trade-offs. How should you manage the sessions?

**Correct answer: B** — Resume the analysis session with `fork_session` enabled, creating a separate branch for each testing strategy.

**Why it's correct:** Forking starts a new session from a copy of the original's history while leaving the original intact, so each strategy gets an independent context branching from the exact 23-file analysis baseline [1]. No cross-contamination between the two strategies, and no re-analysis of the source files — exactly the parallel-alternatives use case fork is built for [1].

**Why the others are wrong:**
- **A:** Exporting key findings to a file is lossier than the live session state the agent built while reading 23 files.
- **C:** Two fresh sessions re-reading the source burn tokens redoing work and risk reaching different conclusions from the same code.
- **D:** Developing both sequentially in one thread lets the first strategy's implementation bias the reasoning about the second.

**Key takeaway:** Compare alternatives from a shared analysis baseline by forking — each branch is isolated, starts identically, and avoids re-reading the codebase. (Same principle as Q22.)

**Sources:** [1] Work with sessions — fork from a copy of history, original unchanged: https://docs.claude.com/en/docs/agent-sdk/sessions

---

### Q29 — code_exploration
**Question:** The agent must insert a new helper function in the middle of a 150-line module, between two existing functions. The Edit tool fails because `old_string` can't find unique text — the file has repetitive docstrings, variable names, and structural patterns. Most reliable way to complete the insertion?

**Correct answer: D** — Use Read to load the file, add the function at the right location in memory, then Write the updated file back.

**Why it's correct:** Edit requires a unique `old_string` match to perform a targeted replacement; in a repetitive file that uniqueness contract can't be satisfied [1]. The correct fallback is to Read the whole file, modify it at the intended line in memory, then Write the full file back — sidestepping the unique-match requirement entirely while still inserting at the precise location [1].

**Why the others are wrong:**
- **A:** A 30+ line `old_string` is brittle — it frequently fails on whitespace or minor differences and produces confusing match errors.
- **B:** `replace_all` mutates *every* occurrence of the pattern, corrupting the whole file rather than inserting once.
- **C:** Appending via Bash heredoc puts the function at the end of the file, but the requirement is to insert *between* two existing functions — wrong location.

**Key takeaway:** When Edit's unique-match contract can't be met in a repetitive file, fall back to Read → modify in memory at the target line → Write the full file back.

**Sources:** [1] Best practices for Claude Code — file editing tools (Edit/Write) and exact-match behavior: https://www.anthropic.com/engineering/claude-code-best-practices

---

### Q30 — code_exploration
**Question:** A newly-joined engineer asks the agent to help understand the auth/authorization architecture before making security improvements. The codebase has 800+ files across multiple services. Best exploration strategy given built-in tools and context limits?

**Correct answer: C** — Use Grep to find authentication entry points, read those files, then follow imports and function calls to map the auth flow incrementally.

**Why it's correct:** Claude Code's hybrid model uses glob/grep to navigate and retrieve files just-in-time, avoiding stale indexes and context floods [1]. Starting at concrete entry points (login, token verification, middleware) and tracing outward along real code edges is incremental and grounded, and fits within context limits — the right pattern for a large unfamiliar codebase [1][2].

**Why the others are wrong:**
- **A:** Asking the brand-new engineer which 10–15 files matter fails — they just joined and don't know; identifying those files is precisely what the agent should do.
- **B:** Parallel subagents across services without knowing where auth lives explore too broadly, duplicate work, and miss cross-service flows.
- **D:** Reading everything containing "auth"/"login"/"permission"/"token" hits a huge amount of unrelated code across 800+ files and drowns context in noise.

**Key takeaway:** In a large unfamiliar codebase, start at concrete entry points via grep, then trace real imports/calls outward incrementally — grounded just-in-time retrieval beats keyword dragnets or blind fan-out.

**Sources:** [1] Effective context engineering — hybrid agentic search with glob/grep: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — [2] Best practices for Claude Code — scoping exploration, avoiding infinite exploration: https://www.anthropic.com/engineering/claude-code-best-practices
