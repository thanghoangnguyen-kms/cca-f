---
tags:
  - CCA-F
  - practice-exam
  - questions
  - official
date: 2026-08-25
status: done
---

# Exam Guide — Sample Questions (unmarked)

12 questions, four options each, **no answers here.** Key: [Answer Key.md](Answer%20Key.md) · index: [README.md](README.md)

Condensed restatements of § 9 of the official exam guide v1.0 (July 2026). Ignore letter positions — see the warning in [README.md](README.md).

---

## Scenario: Customer Support Resolution Agent

*Agent SDK agent handling returns, billing disputes, and account issues through MCP tools `get_customer`, `lookup_order`, `process_refund`, `escalate_to_human`. Target: 80%+ first-contact resolution.*

### Q1

Production data shows that in **12% of cases** the agent skips `get_customer` entirely and calls `lookup_order` using only the customer's stated name — occasionally misidentifying accounts and issuing incorrect refunds. What change would most effectively address this reliability issue?

- **A.** Add a programmatic prerequisite that blocks `lookup_order` and `process_refund` until `get_customer` has returned a verified customer ID.
- **B.** Enhance the system prompt to state that verification via `get_customer` is mandatory before any order operation.
- **C.** Add few-shot examples showing the agent always calling `get_customer` first, even when customers volunteer order details.
- **D.** Implement a routing classifier that analyzes each request and enables only the subset of tools appropriate to that request type.

### Q2

Logs show the agent frequently calls `get_customer` when users ask about orders (*"check my order #12345"*) instead of `lookup_order`. Both tools have minimal descriptions — *"Retrieves customer information"* / *"Retrieves order details"* — and accept similar identifier formats. What is the most effective **first step**?

- **A.** Add 5–8 few-shot examples to the system prompt showing order-related queries routing to `lookup_order`.
- **B.** Expand each tool's description to include input formats, example queries, edge cases, and boundaries explaining when to use it versus similar tools.
- **C.** Implement a routing layer that parses user input each turn and pre-selects a tool from detected keywords and identifier patterns.
- **D.** Consolidate both into a single `lookup_entity` tool that accepts any identifier and internally decides which backend to query.

### Q3

The agent achieves **55% first-contact resolution** against an 80% target. Logs show it escalates straightforward cases (standard damage replacements with photo evidence) while attempting to autonomously handle complex situations that require policy exceptions. What most effectively improves escalation calibration?

- **A.** Add explicit escalation criteria to the system prompt with few-shot examples demonstrating when to escalate versus resolve autonomously.
- **B.** Have the agent self-report a confidence score (1–10) before each response and route to humans below a threshold.
- **C.** Deploy a separate classifier trained on historical tickets to predict which requests need escalation before the main agent starts.
- **D.** Implement sentiment analysis and escalate automatically when negative sentiment exceeds a threshold.

---

## Scenario: Code Generation with Claude Code

*Team uses Claude Code for generation, refactoring, debugging, and documentation, with custom slash commands and `CLAUDE.md` configuration.*

### Q4

You want a custom `/review` slash command running your team's standard review checklist, available to **every developer when they clone or pull the repository**. Where should the command file live?

- **A.** In the `.claude/commands/` directory in the project repository
- **B.** In `~/.claude/commands/` in each developer's home directory
- **C.** In the `CLAUDE.md` file at the project root
- **D.** In a `.claude/config.json` file with a `commands` array

### Q5

You must restructure a monolithic application into microservices — changes across dozens of files, plus decisions about service boundaries and module dependencies. Which approach?

- **A.** Enter plan mode to explore the codebase, understand dependencies, and design an approach before making changes.
- **B.** Start with direct execution and change things incrementally, letting the implementation reveal natural service boundaries.
- **C.** Use direct execution with comprehensive upfront instructions detailing exactly how each service should be structured.
- **D.** Begin in direct execution and switch to plan mode only if unexpected complexity appears during implementation.

### Q6

Your codebase has distinct areas with different conventions: React components use functional style with hooks, API handlers use `async`/`await` with specific error handling, database models follow a repository pattern. **Test files sit next to the code they test** (`Button.test.tsx` beside `Button.tsx`) and all tests must follow the same conventions regardless of location. Most maintainable way to have Claude apply the correct conventions automatically?

- **A.** Create rule files in `.claude/rules/` with YAML frontmatter glob patterns that conditionally apply conventions by file path
- **B.** Consolidate all conventions in the root `CLAUDE.md` under a header per area, relying on Claude to infer which section applies
- **C.** Create skills in `.claude/skills/` for each code type, with the relevant conventions in their `SKILL.md` files
- **D.** Place a separate `CLAUDE.md` in each subdirectory containing that area's conventions

---

## Scenario: Multi-Agent Research System

*Coordinator delegating to web search, document analysis, synthesis, and report subagents.*

### Q7

Running the system on *"impact of AI on creative industries"*, every subagent succeeds: search finds relevant articles, document analysis summarizes papers correctly, synthesis produces coherent output. But the reports cover **only visual arts** — music, writing, and film are missing entirely. The coordinator's logs show it decomposed the topic into three subtasks: *"AI in digital art creation"*, *"AI in graphic design"*, *"AI in photography"*. Most likely root cause?

- **A.** The synthesis agent lacks instructions for identifying coverage gaps in the findings it receives.
- **B.** The coordinator's task decomposition is too narrow, so subagent assignments don't cover all relevant domains of the topic.
- **C.** The web search agent's queries aren't comprehensive enough and need expanding to more creative-industry sectors.
- **D.** The document analysis agent filters out non-visual sources due to overly restrictive relevance criteria.

### Q8

The web search subagent **times out** on a complex topic. How should that failure flow back to the coordinator to best enable intelligent recovery?

- **A.** Return structured error context: failure type, attempted query, any partial results, and potential alternative approaches.
- **B.** Retry with exponential backoff inside the subagent, returning a generic *"search unavailable"* status only after retries are exhausted.
- **C.** Catch the timeout in the subagent and return an empty result set marked successful.
- **D.** Propagate the timeout exception to a top-level handler that terminates the entire research workflow.

### Q9

The synthesis agent frequently needs to verify claims while combining findings. Today it returns control to the coordinator, which invokes the web search agent, then re-invokes synthesis — **2–3 extra round trips per task, +40% latency**. Evaluation shows **85%** of these verifications are simple fact-checks (dates, names, statistics) and **15%** need deeper investigation. Most effective way to cut overhead while keeping reliability?

- **A.** Give the synthesis agent a scoped `verify_fact` tool for simple lookups; complex verifications keep delegating to the web search agent through the coordinator.
- **B.** Have the synthesis agent accumulate all verification needs and return them as one batch to the coordinator at the end of its pass, which then sends them all to the web search agent at once.
- **C.** Give the synthesis agent access to all web search tools so it can handle any verification need directly, without round-trips through the coordinator.
- **D.** Have the web search agent proactively cache extra context around each source during initial research, anticipating what the synthesis agent might need to verify.

---

## Scenario: Claude Code for Continuous Integration

*CI/CD pipeline running automated code review, test generation, and PR feedback.*

### Q10

Your pipeline script runs `claude "Analyze this pull request for security issues"` and the job **hangs indefinitely** — logs show Claude Code waiting for interactive input. Correct approach?

- **A.** Add the `-p` flag: `claude -p "Analyze this pull request for security issues"`
- **B.** Set `CLAUDE_HEADLESS=true` before running the command
- **C.** Redirect stdin from `/dev/null`: `claude "Analyze this pull request for security issues" < /dev/null`
- **D.** Add the `--batch` flag

### Q11

Two workflows currently use real-time Claude calls: **(1)** a blocking pre-merge check developers wait on before merging, and **(2)** a technical-debt report generated overnight for review next morning. Your manager proposes moving both to the Message Batches API for its 50% cost saving. How do you evaluate this?

- **A.** Use batch for the technical-debt reports only; keep real-time calls for pre-merge checks.
- **B.** Switch both to batch with status polling for completion.
- **C.** Keep real-time for both, to avoid batch result-ordering issues.
- **D.** Switch both to batch with a timeout fallback to real-time if batches take too long.

### Q12

A PR modifies **14 files** across the stock-tracking module. Your single-pass review over all files together gives inconsistent results: detailed feedback on some files and superficial comments on others, obvious bugs missed, and **contradictory feedback** — flagging a pattern as problematic in one file while approving identical code elsewhere in the same PR. How should you restructure the review?

- **A.** Split into focused passes: analyze each file individually for local issues, then run a separate integration-focused pass on cross-file data flow.
- **B.** Require developers to split large PRs into 3–4 file submissions before the automated review runs.
- **C.** Switch to a higher-tier model with a larger context window so all 14 files get adequate attention in one pass.
- **D.** Run three independent review passes on the full PR and flag only issues appearing in at least two of the three.

---

*Answers and the official reasoning: [Answer Key.md](Answer%20Key.md)*
