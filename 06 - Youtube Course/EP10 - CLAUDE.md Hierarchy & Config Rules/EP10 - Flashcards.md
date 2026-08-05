---
tags:
  - CCA-F
  - domain-3
  - claude-md
  - configuration
  - youtube-course
  - flashcards
date: 2026-08-04
status: done
domain: "3 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 10"
---

# 🃏 EP10 — Flashcards

> [!NOTE] How to Use This Deck
> Self-contained review for **EP10 — CLAUDE.md Hierarchy & Config Rules** (Domain 3, task statements 3.1 and 3.3). Overlap with the vault-wide deck is intentional — this deck stands alone as a complete review of the episode.
> Cards marked **(docs)** carry a correction or expansion verified against official Anthropic documentation that the lecture did not cover. Where the exam answer and real Claude Code behaviour differ, both are given and labelled.

**Related:** [[EP10 - CLAUDE.md Hierarchy & Config Rules]] · [[D3 - Claude Code Configuration & Workflows]] · [[EP09 - Flashcards]] · [[EP11 - Custom Slash Commands & Skills]]

---

## Domain 3 — What CLAUDE.md Is

**Q: Define `CLAUDE.md` in two words, and say when Claude reads it.**
A: **Persistent context.** It is read at the start of every session, before your first message — so a new session gets the same project context without re-explaining anything.

**Q: Why doesn't Claude "remember" your conventions between sessions the way the lecture's opening scenario implies?**
A: Every session starts with a fresh context window. Nothing accumulates in the model — a file did the work, loaded again each session. If the file isn't shared, the knowledge isn't shared.

**Q: An instruction must run before every single commit, no exceptions. Why is `CLAUDE.md` the wrong home for it? (docs)**
A: `CLAUDE.md` is context, not enforced configuration — it's delivered as a user message after the system prompt, so Claude tries to comply but compliance isn't guaranteed. Use a **hook** for fixed lifecycle points, or `permissions.deny` / `PreToolUse` to block outright.

**Q: What kind of content earns a place in `CLAUDE.md`, and what should be moved elsewhere? (docs)**
A: Keep facts needed in **every** session — build commands, conventions, project layout, "always do X". Move a **multi-step procedure** to a skill, and anything that **only matters for part of the codebase** to a path-scoped rule.

**Q: Two `CLAUDE.md` files give contradictory instructions. What does Claude do? (docs)**
A: It may pick one **arbitrarily** — there's no defined winner. That's why periodic review for conflicts is real maintenance, and why `claudeMdExcludes` exists for monorepos.

---

## Domain 3 — The Hierarchy and Scoping

**Q: Name the four documented `CLAUDE.md` scopes in load order, broadest first. (docs)**
A: **Managed policy** (fixed OS path, org-wide) → **User** (`~/.claude/CLAUDE.md`) → **Project** (`./CLAUDE.md` or `./.claude/CLAUDE.md`) → **Local** (`./CLAUDE.local.md`). The lecture teaches three; subdirectory files are on-demand loading, not a separate scope.

**Q: Where does user-scope `CLAUDE.md` live, and who else ever sees it?**
A: `~/.claude/CLAUDE.md`. Nobody — it applies to all *your* projects on *your* machine and is never shared, by design. Not teammates, not CI, not your second laptop.

**Q: Give both valid project-level `CLAUDE.md` locations, and what actually distinguishes them.**
A: `./CLAUDE.md` and `./.claude/CLAUDE.md`. Both work identically; the root is more **discoverable** on clone, `.claude/` is more **organized** alongside commands and skills. Team convention, not a technical difference.

**Q: A parent directory and your working directory each have a `CLAUDE.md`. Does the nearer file override the further one? (docs)**
A: No — all discovered files are **concatenated**, ordered filesystem root → working directory. Nothing is discarded; the nearer file is just read **last**.

**Q: Within one directory, in what order are `CLAUDE.md` and `CLAUDE.local.md` read? (docs)**
A: `CLAUDE.md` first, then `CLAUDE.local.md` appended after it — so your personal notes are the last thing Claude reads at that level.

**Q: Your org needs a coding standard on every machine and every repo that developers cannot switch off. Which scope, and what guarantees it? (docs)**
A: **Managed policy** — `/Library/Application Support/ClaudeCode/CLAUDE.md` (macOS), `/etc/claude-code/CLAUDE.md` (Linux/WSL), `C:\Program Files\ClaudeCode\CLAUDE.md` (Windows). `claudeMdExcludes` can skip any other `CLAUDE.md` but explicitly **cannot** exclude managed policy.

**Q: What is `CLAUDE.local.md` for, and what must you do after creating one? (docs)**
A: Private per-project preferences — sandbox URLs, preferred test data. Add it to `.gitignore` so it isn't committed; it loads alongside `CLAUDE.md` and is treated the same way.

**Q: In a monorepo, another team's ancestor `CLAUDE.md` keeps loading into your sessions. What fixes it? (docs)**
A: The `claudeMdExcludes` setting — a list of glob patterns matched against absolute paths, configurable at any settings layer. Put it in `.claude/settings.local.json` to keep the exclusion to your machine.

**Q: When is a subdirectory `CLAUDE.md` loaded into context?**
A: **On demand** — when Claude reads a file in that subdirectory. Files at or above the working directory load in full at launch; files below load lazily.

**Q: After `/compact`, a root `CLAUDE.md` instruction still applies but a subdirectory one seems forgotten. Why? (docs)**
A: Project-root `CLAUDE.md` is re-read from disk and re-injected after compaction. Nested files are **not** re-injected — they reload only the next time Claude reads a file in that subdirectory.

---

## Domain 3 — The Classic Team Bug

**Q: A teammate clones the repo and Claude knows nothing about the project, though it has worked for you for three weeks. Root cause?**
A: The `CLAUDE.md` was written at **user scope** (`~/.claude/CLAUDE.md`) and therefore never committed — the repo contains no `CLAUDE.md` at all.

**Q: An exam question says "a teammate isn't following the project's conventions." What do you check first, and why is the intuitive place wrong?**
A: Check the **config location**, on the machine where it *works*. Every symptom appears on the new clone, which draws attention to the newcomer's setup, but the cause is that the working config was never in a shared location.

**Q: A `CLAUDE.md` sits at the project root and the teammate still gets nothing. What's the next check?**
A: Whether it's actually **tracked** — `git ls-files CLAUDE.md`. A root file that was never `git add`ed, or is gitignored, behaves exactly like user scope from a teammate's perspective.

**Q: Beyond teammates, what else does committing project-scope `CLAUDE.md` reach?**
A: **CI.** The same file that configures your interactive session configures Claude in automation, which is why standards belong at project scope rather than user scope.

**Q: Under what conditions is keeping project architecture notes in `~/.claude/CLAUDE.md` genuinely fine — and what breaks it?**
A: Fine while you are the only person on the only machine. It breaks the moment anyone else clones the repo, CI runs, or you work from a second machine — none of those can see your home directory.

---

## Domain 3 — @import

**Q: Your `CLAUDE.md` is 4,000 lines. Does splitting it into `@import`ed files reduce tokens loaded per session? (docs)**
A: **No.** Imported files are expanded and loaded into context **at launch**, so the total is unchanged. `@import` buys modularity — clean ownership, clean PRs, no merge conflicts on one giant file.

**Q: If `@import` doesn't reduce context, what does? (docs)**
A: **Path-scoped rules** — a `.claude/rules/` file with `paths` frontmatter loads only when Claude reads a matching file. Trimming content that isn't needed every session is the other official remedy.

**Q: What is the maximum `@import` chain depth? (docs)**
A: **Four hops.** Imported files can recursively import others up to that depth.

**Q: A relative `@import` path resolves relative to what? (docs)**
A: **The file containing the import** — not the working directory.

**Q: A `CLAUDE.md` has `` `@README` `` in backticks and `@docs/api.md` without. What gets imported? (docs)**
A: Only `docs/api.md`. Import parsing **skips code spans and fenced code blocks**, which is the documented way to mention a path without importing it.

**Q: Why does an import pointing outside your working directory trigger an approval dialog the first time? (docs)**
A: It protects you from files other people commit to a shared project — an external import in a project-level memory file could pull in arbitrary content. Imports in user-scope files skip the dialog, since you wrote them.

**Q: Your repo already has an `AGENTS.md` for other coding agents. How do you make Claude Code use it without duplicating content? (docs)**
A: Claude Code reads `CLAUDE.md`, not `AGENTS.md` — so create a `CLAUDE.md` whose first line is `@AGENTS.md`, then add any Claude-specific instructions below it. A symlink also works if you need nothing Claude-specific.

---

## Domain 3 — .claude/rules and Path Scoping

**Q: What can a `.claude/rules/` file do that no `CLAUDE.md` can, at any level?**
A: Scope by **file pattern** instead of tree position. A rule with `paths` can target `**/*.test.tsx` files scattered across twenty packages; a `CLAUDE.md` is bound to where it sits in the directory tree.

**Q: Which frontmatter field makes a rule conditional?**
A: **`paths`** — a list of glob patterns. Without it, the rule loads unconditionally.

**Q: The lecture says a rule's frontmatter always contains a `description` field. What do the docs say? (docs)**
A: `description` is **not** a documented rules field — the docs document `paths` only. It's required for **skills**, which is the likely source of the confusion. For rules, `paths` is the field that matters.

**Q: A rule file's frontmatter has a `description` but no `paths`. What happens?**
A: It loads **unconditionally at launch**, at the same priority as `.claude/CLAUDE.md` — because `paths` is what makes a rule conditional and it's absent. The `description` has no scoping effect.

**Q: When exactly does a path-scoped rule enter the context window? (docs)**
A: When Claude **reads a file matching one of its `paths` patterns** — not at launch, and not on every tool use. Until then it costs nothing.

**Q: Where do personal rules that apply to every project live, and do they beat project rules? (docs)**
A: `~/.claude/rules/`. They load **before** project rules, so **project rules take priority**.

**Q: How can several projects share one maintained set of rules? (docs)**
A: `.claude/rules/` supports **symlinks** — link a shared directory or an individual file (`ln -s ~/company-standards/security.md .claude/rules/security.md`). Circular symlinks are detected and handled.

**Q: Are `.claude/rules/` files discovered in subdirectories of `rules/`? (docs)**
A: Yes — all `.md` files are discovered **recursively**, so you can organize them into `frontend/`, `backend/`, and so on.

---

## Domain 3 — Glob Patterns

**Q: What does `**` match in a `paths` glob, versus `*`?**
A: `**` matches **any number of directories**, at any depth. `*` matches **any name within a single level** and does not descend.

**Q: Distinguish what `src/api/*.ts` matches from `src/api/**/*.ts`.**
A: `src/api/*.ts` matches `.ts` files **directly** in `src/api/` only. `src/api/**/*.ts` matches them at **any depth** beneath `src/api/`.

**Q: What does the pattern `*.md` match? (docs)**
A: Markdown files in the **project root only** — a leading single `*` doesn't descend into subdirectories.

**Q: Write one pattern matching both `.ts` and `.tsx` files anywhere under `src/`. (docs)**
A: `src/**/*.{ts,tsx}` — brace expansion matches multiple extensions in a single pattern.

**Q: What is the risk of stacking many brace groups in one `paths` list? (docs)**
A: Each group multiplies the pattern count, and a rule's whole `paths` list shares a budget of **1,000 expanded patterns and 4 MiB**. Any pattern that would exceed it is used **unexpanded**, so its literal braces match nothing.

**Q: A rule targets `photos [2024/**` and matches nothing. Why? (docs)**
A: Glob syntax reads `[` as the start of a bracket expression, so an unmatched `[` makes the pattern invalid — it matches nothing while the rule's other patterns keep working. Escape it as `photos \[2024/**`.

---

## Domain 3 — Verifying Configuration

**Q: Claude appears to ignore your `CLAUDE.md`. Which command tells you whether it actually loaded? (docs)**
A: **`/context`** — check the list under **Memory files**. If a file is missing there, Claude cannot see it.

**Q: What does `/memory` actually do, and why can't it confirm a file loaded? (docs)**
A: It **lists memory-file locations** across user and project scope — *including entries for files that don't exist yet* — toggles auto memory, and opens a selected file in your editor. Because it reports locations rather than load state, it shows a path whether or not anything loaded.

**Q: You need to know exactly which instruction files loaded, when, and why — including lazily-loaded nested files. What's built for that? (docs)**
A: The **`InstructionsLoaded` hook**, which logs precisely that. It's the recommended tool for debugging path-specific rules and on-demand subdirectory loading.

**Q: What is the official size target for a single `CLAUDE.md`, and what happens past it? (docs)**
A: Under **200 lines**. Longer files consume more context and **reduce adherence** — Claude follows a shorter, more specific file more reliably.

**Q: Your `CLAUDE.md` is too large. Rank the official remedies. (docs)**
A: Move content to **path-scoped rules** so it loads only for matching files, or **trim** what isn't needed every session. `@import` is explicitly *not* a remedy — it reorganises without reducing context.

**Q: Which command generates a first-draft `CLAUDE.md`, and what does it do if one already exists? (docs)**
A: **`/init`**. It analyses the codebase for build commands, tests, and conventions — and if a `CLAUDE.md` already exists it **suggests improvements rather than overwriting** it.

---

*Back to: [[EP10 - CLAUDE.md Hierarchy & Config Rules]]*
