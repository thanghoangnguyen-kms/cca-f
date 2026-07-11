# 03 — Context Management & Sessions (Code Exploration)

Covers: context degradation, scratchpads/compaction, sessions (resume/fork/fresh),
just-in-time exploration, and Edit vs Write. (Mock Q16–Q30; concept chapters 14, 15.)

---

## Core concepts (memorize)

### Context degradation & attention dilution
- Do **not** dump a whole large codebase into one giant context — attention gets
  **diluted** and the agent starts substituting **generic patterns** for the
  specific classes/facts it found earlier.
- Context pollution affects **windows of all sizes** — a bigger window only
  **delays** the problem, it doesn't cure it.
- Correct approach = **multi-pass architecture**: focused pass per file/concern →
  synthesis pass that only receives **summaries/findings**.

### Managing long explorations (three techniques)
1. **Scratchpad / structured note-taking** — persist key findings to a durable
   file the agent re-reads on demand. Survives context degradation.
2. **Compaction** — distill accumulated findings into a compact summary.
3. **Subagents** — hand a *fresh* subagent a summary + a well-scoped task so it
   works in a **clean context window**, escaping the degraded one. This is the
   pattern when the work **exceeds one context window** (lead agent keeps the overview).

### Sessions: resume / fork / fresh
- **Resume** — continue a *specific* prior session; restores full prior context
  (files read, analysis done, decisions made).
  - `--resume <name/id>` → pick a specific session (by memorable name or UUID).
  - `--continue` → the **most recent** session in the current directory only.
  - If files changed since: **resume + tell it the exact delta** (which files
    changed) so it re-reads only what's stale — not everything, not nothing.
    Never resume *silently* on stale content.
- **Fork** (`fork_session`) — new session from a **copy** of history; original
  **unchanged**. Use to explore **mutually-exclusive alternatives** from one
  shared baseline (one branch per approach, no cross-contamination, no re-analysis).
- **Fresh start** — when old context is noisy/wrong, or you need isolation.

### Just-in-time exploration (grounded search)
- Claude Code navigates with **glob/grep**, retrieving files **just-in-time** —
  no stale pre-built index.
- Large unfamiliar codebase → **grep the entry points → read those → follow
  imports/calls outward incrementally.** Beats keyword dragnets and blind fan-out.
- Deleting a symbol → first **read the wrappers/re-exports to enumerate every
  alias**, then grep each alias. Search must be grounded in real code edges.
- Explore from the **architectural root** (base interface) outward to the
  relevant implementations — not by bulk-reading, not by file size.
- Open-ended debugging (unknown 500s) → **adaptive, evidence-driven
  decomposition**: let each discovery shape the next step. No blind upfront plan,
  no fixed sequence, no premature 4-way fan-out.

### Edit vs Write (file editing)
- **Edit** needs a **unique `old_string`** match. In a repetitive file where
  uniqueness can't be met, fall back to **Read → modify in memory at the target
  line → Write the full file back.**
- `replace_all: true` → only when the **same exact string** appears multiple
  times and **every** occurrence should change.
- Don't use a giant 30+ line `old_string` (brittle), and don't append via Bash
  heredoc when the requirement is to insert **between** functions.

---

## Decision rules

| Trigger | Answer |
|---|---|
| 30-min session, answers go generic / inconsistent | **Scratchpad** of key findings (structured note-taking) |
| Context degraded, now need a cross-cutting question | **Summarize** findings → seed a **fresh subagent** with it |
| Resume a *named* session after working elsewhere | `--resume <name>` (not `--continue`) |
| Resume but N of M files changed overnight | Resume + **name the changed files** for targeted re-read |
| Compare two exclusive approaches from one analysis | **`fork_session`**, one branch each |
| Connection dropped mid-exploration; 2 fns renamed | **Resume the subagent + tell it the renames** |
| Understand a huge unfamiliar codebase | **Grep entry points → read → follow imports** incrementally |
| Find all callers incl. renamed re-exports | Read wrappers to list aliases → **grep each alias** |
| Exploration exceeds one context window | Delegate scoped sub-investigations to **subagents** |
| Debug unknown intermittent error, unknown components | **Adaptive** decomposition, evidence-driven |
| Edit fails: no unique `old_string` in repetitive file | **Read → edit in memory → Write** full file |

---

## Anti-patterns

- Solving degradation with just a **bigger context window**.
- **Auto-clearing** context on a timer (discards valid findings).
- **Pre-summarizing every file upfront** (strips the detail exploration needs).
- Starting **fresh** and re-reading when you could resume (throws away work).
- Re-reading **all** files on resume when only a few changed.
- Loading a long transcript into a **fresh** subagent's prompt (pollutes it).
- **Bulk-reading** thousands of lines or picking files **by size**.
- Keyword **dragnet** ("auth/login/token") across 800+ files.
- Building a **comprehensive plan for an unknown error** with no exploration.

---

## Key phrases to recognize

"context degradation / pollution / attention dilution" · "multi-pass" ·
"scratchpad / structured note-taking" · "compaction" · "clean context window" ·
"`--resume` vs `--continue`" · "`fork_session`" · "just-in-time retrieval" ·
"glob/grep" · "communicate the delta" · "adaptive decomposition."

## Sources
- Effective context engineering for AI agents — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Best practices for Claude Code — https://www.anthropic.com/engineering/claude-code-best-practices
- Work with sessions — https://docs.claude.com/en/docs/agent-sdk/sessions
