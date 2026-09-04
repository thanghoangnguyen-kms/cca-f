---
tags:
  - CCA-F
  - practice-exam
  - questions
  - domain-3
  - claude-code
date: 2026-08-25
status: done
---

# Claude Code Scenario Drills — Questions

20 questions, four options each, **no answers here.** Each question ends with an **Answer:** link that jumps straight to its worked entry in the right scenario key — no manual lookup, and no working out which of the two keys holds it. Keys: [Answer Key/scenario-2-code-generation.md](Answer%20Key/scenario-2-code-generation.md) · [Answer Key/scenario-5-continuous-integration.md](Answer%20Key/scenario-5-continuous-integration.md) · index: [README.md](README.md)

> [!NOTE] Vault-authored
> Derived from the official task statements, not from any question bank. See [README.md](README.md) § Provenance.

---

## Scenario 2 — Code Generation with Claude Code

*Your team uses Claude Code for code generation, refactoring, debugging, and documentation, with custom slash commands, `CLAUDE.md` configuration, and a mix of plan mode and direct execution.*

### Q1

A new engineer joins the team. Everyone else's Claude Code sessions consistently apply the team's error-handling conventions, but theirs doesn't — despite having pulled the same repository. The conventions were written by the tech lead six months ago and have worked ever since. What is the most likely cause?

- **A.** The new engineer needs to run `/init` to generate project configuration before the conventions apply
- **B.** The conventions live in the tech lead's `~/.claude/CLAUDE.md`, which is user-level and not shared via version control
- **C.** The repository's `CLAUDE.md` exceeds the size limit and is being silently truncated for new sessions
- **D.** Directory-level `CLAUDE.md` files only load after a session has visited that directory once

**Answer:** [[scenario-2-code-generation#Q1 — New engineer doesn't get the team's conventions → B|↳ Q1 in the Scenario 2 key]]

### Q2

Claude Code behaves inconsistently between two of your projects — in one it follows your personal commit-message convention, in the other it ignores it. Before changing any configuration, which command does the exam guide name for diagnosing **inconsistent behavior across sessions**?

- **A.** Run `/context` to see the token breakdown of the current session
- **B.** Run `/compact` to reset accumulated context, then re-test the behavior
- **C.** Run `/memory` to see which memory files are loaded
- **D.** Add `@import` statements to both projects so the configuration is explicit

**Answer:** [[scenario-2-code-generation#Q2 — Diagnosing inconsistent behavior across projects → C|↳ Q2 in the Scenario 2 key]]

### Q3

A monorepo has eight packages. Each maintainer knows which coding standards apply to their package, and the standards documents already exist as separate Markdown files. The root `CLAUDE.md` has grown to include all of them and is now unwieldy. What is the most maintainable structure?

- **A.** Keep everything in the root `CLAUDE.md` but reorganize under clear per-package headers
- **B.** Use `@import` in each package's `CLAUDE.md` to pull in only the standards files relevant to that package
- **C.** Move every standards file into `.claude/skills/` so Claude loads them on demand
- **D.** Duplicate the relevant standards inline into each package's `CLAUDE.md` so nothing depends on file resolution

**Answer:** [[scenario-2-code-generation#Q3 — Monorepo, eight packages, standards files already exist → B|↳ Q3 in the Scenario 2 key]]

### Q4

Your project's root `CLAUDE.md` has grown to cover testing conventions, API conventions, and deployment procedures in one file. You want the content organized by topic without losing coverage. What is the recommended approach?

- **A.** Split it into topic-specific files in `.claude/rules/` — `testing.md`, `api-conventions.md`, `deployment.md`
- **B.** Split it into `.claude/commands/testing.md`, `.claude/commands/api.md`, and so on
- **C.** Move each topic into its own subdirectory `CLAUDE.md` at the directory where it applies
- **D.** Keep one file — splitting `CLAUDE.md` means only the last-loaded file takes effect

**Answer:** [[scenario-2-code-generation#Q4 — One `CLAUDE.md` covering three unrelated topics → A|↳ Q4 in the Scenario 2 key]]

### Q5

You've built a `.claude/skills/` skill that analyzes the codebase and reports architectural findings. It works, but it dumps thousands of tokens of exploration output into the main conversation, and subsequent work in the session degrades. Which frontmatter option addresses this?

- **A.** `allowed-tools: [Read, Grep, Glob]` to limit how much the skill can read
- **B.** `context: fork` to run the skill in an isolated sub-agent context
- **C.** `argument-hint` to require a narrower scope before the skill runs
- **D.** `model: haiku` so the exploration consumes fewer tokens

**Answer:** [[scenario-2-code-generation#Q5 — Skill floods the main conversation → B|↳ Q5 in the Scenario 2 key]]

### Q6

A team skill automates a database-migration workflow. During review, someone points out that a mis-generated step could run a destructive command. You want this handled by the skill's **configuration**, not by an instruction in its body. Which frontmatter field does the exam guide name for restricting a skill's tool access?

- **A.** A `PreToolUse` hook in the skill's `SKILL.md` frontmatter
- **B.** A `[!WARNING]` block at the top of the skill body stating that destructive commands are forbidden
- **C.** `context: fork`, so any destructive action is contained to the sub-agent
- **D.** `allowed-tools` in the skill frontmatter, restricting it to the operations it legitimately needs

**Answer:** [[scenario-2-code-generation#Q6 — Restricting a skill's tool access → D|↳ Q6 in the Scenario 2 key]]

### Q7

Developers keep invoking your `/scaffold` skill with no arguments, then getting a generic result because the skill doesn't know which component type to scaffold. Which frontmatter field does the exam guide name for signalling the expected parameter at invocation time?

- **A.** `argument-hint` frontmatter naming the expected parameter
- **B.** A required-parameters section in the skill body
- **C.** `context: fork`, so the skill can ask clarifying questions in isolation
- **D.** A `PreToolUse` hook that blocks the skill until arguments are supplied

**Answer:** [[scenario-2-code-generation#Q7 — Skill invoked without required parameters → A|↳ Q7 in the Scenario 2 key]]

### Q8

Your team shares a `/review` skill in `.claude/skills/`. You want a personal variant with a stricter checklist, without changing what your teammates get. What should you do?

- **A.** Edit `.claude/skills/review/SKILL.md` and add a conditional branch for your username
- **B.** Create a variant under `~/.claude/skills/` with a different name
- **C.** Add your stricter checklist to `~/.claude/CLAUDE.md` so it layers on top of the shared skill
- **D.** Fork the repository and maintain your version on a personal branch

**Answer:** [[scenario-2-code-generation#Q8 — Personal variant of a shared skill → B|↳ Q8 in the Scenario 2 key]]

### Q9

A production bug produces a clear stack trace pointing at a single function that fails to handle a null date. The fix is one conditional. Which approach is appropriate?

- **A.** Plan mode, so the change is explored and designed before any edit is made
- **B.** Plan mode for investigation, then direct execution for the one-line change
- **C.** Direct execution — the scope is clear and well-understood
- **D.** Spawn an `Explore` subagent to trace the null's origin before deciding

**Answer:** [[scenario-2-code-generation#Q9 — One-line null-date fix with a clear stack trace → C|↳ Q9 in the Scenario 2 key]]

### Q10

You've described a data-transformation requirement in prose three times, and each time Claude produces a slightly different interpretation of the edge cases. The requirement itself is stable — your description of it isn't landing. What is the most effective next step?

- **A.** Restate the requirement more precisely and add "be careful with edge cases"
- **B.** Move the requirement into `CLAUDE.md` so it is always loaded
- **C.** Provide 2–3 concrete input/output example pairs showing the expected transformation
- **D.** Ask Claude to write a specification first, then implement against its own specification

**Answer:** [[scenario-2-code-generation#Q10 — Prose keeps being interpreted inconsistently → C|↳ Q10 in the Scenario 2 key]]

---

## Scenario 5 — Claude Code for Continuous Integration

*Your CI/CD pipeline runs automated code review, generates test cases, and posts feedback on pull requests. Prompts must give actionable feedback and minimize false positives.*

### Q11

Your review job currently prints prose findings to the build log, and a brittle regex in your pipeline script tries to turn them into inline PR comments. It breaks whenever the phrasing shifts. What is the correct fix?

- **A.** Instruct Claude in the prompt to always emit findings as a Markdown table with fixed columns
- **B.** Run the review with `--output-format json` and `--json-schema` to produce machine-parseable structured findings
- **C.** Switch the job to the Message Batches API, whose responses are already structured
- **D.** Pipe the output through a second Claude Code invocation that converts prose into JSON

**Answer:** [[scenario-5-continuous-integration#Q11 — Regex parsing prose findings keeps breaking → B|↳ Q11 in the Scenario 5 key]]

### Q12

Your review job re-runs on every push. Developers complain that after pushing a fix, the bot re-posts the same comments it posted on the previous commit, alongside any new ones. How should the pipeline handle this?

- **A.** Only run the review on the first push of a pull request
- **B.** Deduplicate posted comments in the pipeline by hashing the finding text
- **C.** Include the prior review findings in context and instruct Claude to report only new or still-unaddressed issues
- **D.** Reduce the review scope to the diff of the latest commit rather than the whole PR

**Answer:** [[scenario-5-continuous-integration#Q12 — Bot re-posts the same comments after each push → C|↳ Q12 in the Scenario 5 key]]

### Q13

Your CI test-generation job produces test cases that substantially duplicate scenarios the existing suite already covers. The generated tests are correct — just redundant. What is the most effective change?

- **A.** Instruct the prompt to "avoid generating duplicate tests"
- **B.** Provide the existing test files in context so generation can see what is already covered
- **C.** Run generation through a second pass that removes duplicates before opening the PR
- **D.** Restrict generation to files whose test coverage is measured below a threshold

**Answer:** [[scenario-5-continuous-integration#Q13 — Generated tests duplicate existing coverage → B|↳ Q13 in the Scenario 5 key]]

### Q14

The tests your CI job generates are syntactically fine but low-value: they assert trivia, ignore your fixture conventions, and don't reflect what your team considers worth testing. The criteria are repo-wide judgments about what your team considers worth testing, and don't attach to any one file path. Where should these standards, fixture conventions, and valuable-test criteria live so that CI-invoked Claude Code picks them up?

- **A.** In the pipeline's prompt string, passed with `-p` on every invocation
- **B.** In `.claude/rules/` with a `paths:` glob matching the test files
- **C.** In `CLAUDE.md`, as project context for the repository
- **D.** In a skill under `.claude/skills/` that the pipeline invokes before generation

**Answer:** [[scenario-5-continuous-integration#Q14 — Where CI-invoked Claude Code should read team standards → C|↳ Q14 in the Scenario 5 key]]

### Q15

Your pipeline generates an implementation, then — in the same session — asks Claude to review what it just produced. The reviews are consistently shallow and rarely find real problems. What is the root cause, and the fix?

- **A.** The review prompt is under-specified; add explicit review criteria to the same session
- **B.** The model retains its generation reasoning and is unlikely to question its own decisions — use an independent review instance without that context
- **C.** The session has accumulated too much context; run `/compact` before the review step
- **D.** Reviews need extended thinking enabled; the same session is fine once it can reason longer

**Answer:** [[scenario-5-continuous-integration#Q15 — Same session generates then reviews → B|↳ Q15 in the Scenario 5 key]]

### Q16

Your review bot reports too many false positives. You've already added *"be conservative"* and *"only report high-confidence findings"* to the prompt, with no measurable improvement. What actually works?

- **A.** Have the model attach a confidence score to each finding and filter below a threshold
- **B.** Write specific categorical criteria defining which issues to report (bugs, security) and which to skip (minor style, local patterns)
- **C.** Run the review three times and report only findings that appear in every run
- **D.** Lower the temperature so the model's judgments become more conservative

**Answer:** [[scenario-5-continuous-integration#Q16 — "Be conservative" doesn't cut false positives → B|↳ Q16 in the Scenario 5 key]]

### Q17

Of your five review categories, four are accurate and one — "comment accuracy" — is wrong most of the time. Developers have started ignoring *all* the bot's comments, including the accurate ones. You need a prompt rewrite for that category but it will take a sprint. What should you do meanwhile?

- **A.** Keep all categories running; suppressing findings risks missing a real issue
- **B.** Post the comment-accuracy findings as non-blocking suggestions rather than review comments
- **C.** Temporarily disable the comment-accuracy category to restore trust while you improve its prompt
- **D.** Route comment-accuracy findings to a separate report only the tech lead reads

**Answer:** [[scenario-5-continuous-integration#Q17 — One bad category poisoning trust in four good ones → C|↳ Q17 in the Scenario 5 key]]

### Q18

Severity labels from your review job are inconsistent: the same class of issue is marked `critical` in one PR and `minor` in another. Your prompt currently says *"assign a severity of critical, major, or minor based on impact."* What most improves consistency?

- **A.** Reduce the scale to two levels — `blocking` and `non-blocking`
- **B.** Define explicit severity criteria with a concrete code example for each level
- **C.** Have a second Claude instance re-grade severities after the first pass
- **D.** Compute severity in the pipeline from the file path and change size, not from the model

**Answer:** [[scenario-5-continuous-integration#Q18 — Inconsistent severity labels → B|↳ Q18 in the Scenario 5 key]]

### Q19

Your findings are accurate but formatted inconsistently — some name a file and line, some don't; some suggest a fix, some only describe the problem. Your prompt already spells out the required format in detail. What is the most effective technique to get consistent, actionable output?

- **A.** Add few-shot examples demonstrating the exact output shape: location, issue, severity, suggested fix
- **B.** Repeat the format requirements at both the start and end of the prompt
- **C.** Split each finding into its own request so the model has less to track
- **D.** Move the format requirements into `CLAUDE.md` so they're always loaded

**Answer:** [[scenario-5-continuous-integration#Q19 — Format inconsistent despite detailed instructions → A|↳ Q19 in the Scenario 5 key]]

### Q20

Developers dismiss roughly a third of your bot's findings. You want to know *systematically* which code constructs are producing the dismissed ones, so you can target prompt fixes rather than guess. What should you change in the structured output?

- **A.** Add a `confidence` field so you can correlate dismissals with low confidence
- **B.** Add a `detected_pattern` field recording which code construct triggered the finding
- **C.** Add a free-text `reasoning` field so you can read the model's justification per finding
- **D.** Add a `severity` field and analyze dismissals by severity level

**Answer:** [[scenario-5-continuous-integration#Q20 — Systematically analyzing dismissed findings → B|↳ Q20 in the Scenario 5 key]]

---

*Keys: [Answer Key/scenario-2-code-generation.md](Answer%20Key/scenario-2-code-generation.md) · [Answer Key/scenario-5-continuous-integration.md](Answer%20Key/scenario-5-continuous-integration.md)*
