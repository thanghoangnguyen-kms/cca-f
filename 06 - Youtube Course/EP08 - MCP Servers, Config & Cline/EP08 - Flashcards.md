---
tags:
  - CCA-F
  - domain-2
  - mcp
  - configuration
  - flashcards
  - youtube-course
date: 2026-08-03
status: done
domain: "2 of 5"
source: "Peace Of Code — Claude Certified Architect Ep 08"
---

# 🃏 EP08 Flashcards — MCP Servers, Config & Cline

> [!NOTE] How to Use This Deck
> Active-recall cards drawn from [[EP08 - MCP Servers, Config & Cline]]. Cover the `A:` line and answer before revealing. This deck is **self-contained** — it covers the episode in full, so some cards overlap with the vault-wide [[Flashcards]] deck by design. Study either on its own.
>
> **Related:** [[D2 - Tool Design & MCP Integration]] · [[C3 - Introduction to MCP]] · [[EP07 - Flashcards]] · [[Critical Terms Glossary]] · [[CCA-F Study Roadmap]]

---

## Domain 2 — Why MCP Exists

**Q: You wrote a tool as a function inside your agent. What can't a second agent do with it, and why?**
A: It **cannot call it at all** — the tool is effectively a private method, tightly coupled to that agent's code. Only that agent knows it exists, so every integration becomes custom and brittle.

**Q: A wrapped API moves from `/v1/` to `/v2/`. Compare the blast radius with an embedded HTTP call versus an MCP server.**
A: **Embedded:** you edit and redeploy **every** agent that calls it. **MCP:** you change the **server** only, and every connected host keeps working — the agent↔server contract is unaffected.

**Q: Name the three capability kinds an MCP server can expose.**
A: **Tools**, **resources**, and **prompts** — discovered via `tools/list`, `resources/list`, and `prompts/list`. MCP is not tools-only.

**Q: What does the "USB-C for AI" analogy actually claim about MCP?**
A: That standardising the **port** removes the need for a custom connector per pairing — any host (Claude Code, Cline, Codex, your own agent) can use any MCP server, instead of each agent hand-rolling each integration.

---

## Domain 2 — Architecture & Lifecycle

**Q: Name the three MCP architectural components and say which contains which.**
A: The **host** (agent or application) contains the **MCP client** (an SDK), which connects to an **MCP server** holding the tools, resources, and prompts.

**Q: What is MCP's wire format, and does it always run over HTTP?**
A: **JSON-RPC 2.0.** Not always over HTTP — remote servers carry JSON-RPC over HTTP, but **stdio** servers carry the same JSON-RPC over standard input/output with no HTTP involved.

**Q: Is MCP stateless or stateful, and what does that imply for a sequence of calls?**
A: **Stateful.** A session is established at initialize — on streamable HTTP via a session ID carried on subsequent calls — so it has a lifecycle rather than being independent one-shot requests.

**Q: List the six phases of MCP communication in order.**
A: **Connect** → **Initialize** (session negotiated) → **Discover** (`tools/list`) → **Reason** → **Invoke** (`tools/call`) → **Return** structured result.

**Q: Give the exact MCP method names for discovering tools and for invoking one.**
A: **`tools/list`** to discover, **`tools/call`** to invoke.

**Q: In the MCP model, where do tool descriptions come from — and why does that matter?**
A: From the **server**, at runtime, rather than from text you wrote into your own `tools` array. It matters because you no longer control that text, which is why an MCP tool can lose a selection contest to a built-in and why you can't fix its description locally.

**Q: An MCP server adds a new tool while your session is running. Must you reconnect?**
A: **No.** Claude Code supports MCP `list_changed` notifications and refreshes the server's tools, prompts, and resources automatically. If a refresh fails it keeps the previously discovered capabilities rather than emptying the list.

**Q: How many times does Claude Code retry a failed `tools/list`, and which failures are not retried?**
A: Up to **three times** with short backoff for transient network and server errors. **Authentication errors, 4xx responses, and request timeouts are not retried** — the same retryable/non-retryable split as [[EP07 - Agent Error Handling & tool_choice]], at the protocol layer.

---

## Domain 2 — MCP vs APIs

**Q: Will MCP servers replace REST APIs?**
A: **No.** APIs expose backend business logic to services and UIs, with clients knowing routes up front; MCP exposes tools and context to AI applications with runtime discovery. In practice MCP **wraps** an API rather than replacing it.

**Q: What does MCP standardise, and what does it explicitly not give you?**
A: It standardises **how an agent discovers and calls capabilities**. It gives you **no security or authorization** for free — *"MCP is not magic... if you're using OAuth, keep using OAuth."* Your auth model is unchanged.

**Q: Distinguish MCP from A2A.**
A: **MCP** connects an agent to capabilities (tools, resources, prompts). **A2A (Agent2Agent)** connects an agent to another agent. A chatbot might reach an agent over A2A, and that agent reaches its tools over MCP — they sit at different layers.

---

## Domain 2 — Configuration Scopes

**Q: Name all three MCP configuration scopes, and the file each is stored in.**
A: **Local** — `~/.claude.json`, under that project's path. **Project** — `.mcp.json` at the repo root. **User** — `~/.claude.json`, at top level.

**Q: Which MCP scope is the default when you run `claude mcp add` with no scope flag?**
A: **Local scope** — the server loads only in that project and stays private to you, written into `~/.claude.json` under the project's path.

**Q: Which MCP scope is the only one shared with your team, and by what mechanism?**
A: **Project scope** — `.mcp.json` at the repo root, shared by **version control**. Local and user scope both live in `~/.claude.json`, which is never committed.

**Q: Local and user scope use the same file. What distinguishes them inside it?**
A: **Where in the file the entry sits.** Local-scope servers are nested under `projects."<project path>"`, so they load only in that project. User-scope servers sit at the **top level**, so they load in all your projects.

**Q: A teammate clones the repo and has no MCP tools. What is the most likely scope-level cause?**
A: The server was added at **local scope** — the default — so it lives in your `~/.claude.json` under the project path and was never committed. Re-add with `--scope project`.

**Q: Give the `claude mcp add` command that puts a remote HTTP server where the team will get it.**
A: `claude mcp add --transport http --scope project <name> <url>` — `--scope project` is required; omitting it lands in local scope.

**Q: State the MCP scope precedence order.**
A: **Local → Project → User → Plugin-provided → claude.ai connectors.** Claude Code connects once, using the highest-precedence definition.

**Q: The same server name exists at local and project scope with different URLs. What happens to the two entries?**
A: The **local entry wins entirely** — Claude Code uses the whole entry from the highest-precedence source and **does not merge fields across scopes**. You cannot override just the URL or just the token from another scope.

**Q: How do the three scopes decide two entries are duplicates, and how do plugins and connectors decide?**
A: The three scopes match duplicates by **name**; plugins and claude.ai connectors match by **endpoint** (same URL or command).

**Q: You commit a correct `.mcp.json`. Why might your teammate still have no tools?**
A: Claude Code **prompts for approval** before using project-scoped servers from `.mcp.json`, for security. Until the teammate approves, the servers don't load. Reset approval choices with `claude mcp reset-project-choices`.

---

## Domain 2 — `.mcp.json` Structure & Transports

**Q: What is the top-level key in `.mcp.json`, and what does each entry under it represent?**
A: **`mcpServers`** — an object keyed by server name, where each entry tells Claude how to run (local) or connect to (remote) that server.

**Q: Name all four `type` values for an MCP server entry.**
A: **`http`** (recommended for remote; `streamable-http` is an accepted alias) · **`sse`** (deprecated) · **`stdio`** (local subprocess) · **`ws`** (WebSocket).

**Q: Which MCP transport is deprecated, and what should replace it?**
A: **`sse`** (Server-Sent Events) is deprecated — use **`http`** instead where available. Some services still expose only an SSE endpoint.

**Q: What happens if a server entry has a `url` but no `type` field?**
A: It is a **configuration error**. Claude Code reads a typeless entry as **stdio**, skips the server, and reports that the entry has a `url` but no `type`, telling you to add `"type": "http"` (or `"sse"` / `"ws"`).

**Q: What is unusual about configuring a `ws` MCP server?**
A: It can only be set up in **`.mcp.json` or `claude mcp add-json`** — `claude mcp add --transport` does not accept `ws`. Auth is **header-only** (no OAuth), via static `headers` or `headersHelper`.

**Q: What single question decides whether you configure a server as stdio or HTTP?**
A: **Where the server lives** — local means stdio (Claude spawns it as a subprocess), remote means HTTP. Not what the server does; the same capability can be offered either way.

**Q: How does Claude run a stdio MCP server, and how does it talk to it?**
A: It **spawns the server as a subprocess** (`command` + `args`) and exchanges JSON-RPC over that process's **standard input and output**.

**Q: In `claude mcp add`, what does the `--` separator do?**
A: It separates Claude's own flags (`--transport`, `--env`, `--scope`) from the command that runs the server. Everything after `--` is passed through untouched — without it, Claude tries to parse the server's flags (like `--port`) as its own.

**Q: Which `.mcp.json` field sets a per-server tool-execution timeout, and in what unit?**
A: **`timeout`**, in **milliseconds** — e.g. `"timeout": 600000` for ten minutes. It overrides the `MCP_TOOL_TIMEOUT` environment variable for that server only.

---

## Domain 2 — Environment Variables

**Q: `.mcp.json` must be committed but needs a GitHub token. What is the mechanism, and what is the one-line rule?**
A: **Environment variable expansion** — write `${GITHUB_TOKEN}` in the file and Claude resolves it from the shell at server start. The rule: **values stay local, variables ship to Git.**

**Q: Give both supported environment-variable expansion forms in `.mcp.json`.**
A: **`${VAR}`** — expands to the value of `VAR`. **`${VAR:-default}`** — expands to `VAR` if set, otherwise uses `default`.

**Q: Which environment-variable syntaxes are *not* supported in `.mcp.json`?**
A: Bare **`$VAR`** and Windows-style **`%VAR%`**. Only `${VAR}` and `${VAR:-default}` work.

**Q: Name the five fields where environment variable expansion works in `.mcp.json`.**
A: **`command`**, **`args`**, **`env`**, **`url`**, and **`headers`**.

**Q: Why does referencing `CLAUDE_PROJECT_DIR` in `.mcp.json` require the `${VAR:-default}` form?**
A: Because Claude Code sets `CLAUDE_PROJECT_DIR` in the **spawned server's** environment, not in its own — so it isn't set in the environment doing the expansion. Write **`${CLAUDE_PROJECT_DIR:-.}`**.

---

## Domain 2 — MCP Resources

**Q: A documentation MCP server holds 500 articles and exposes only a search tool. What does that cost?**
A: Repeated exploratory calls — search, re-search with different terms, read a few articles, search again — **burning tokens before finding the right content**. Discovery becomes a guessing loop.

**Q: What is an MCP resource, and what does it change about discovery?**
A: Content the server exposes **directly as a structured catalog** that Claude can see up front. Discovery becomes a **lookup instead of a search**, removing the exploratory tool calls.

**Q: Give the syntax for referencing an MCP resource in Claude Code.**
A: **`@server:protocol://resource/path`** — e.g. `@github:issue://123`, `@docs:file://api/authentication`. Typing `@` lists available resources alongside files, and referenced resources are fetched automatically as attachments.

**Q: You want resources on a third-party MCP server that doesn't expose any. What are your options?**
A: **None on the consumer side** — exposing resources is a **server-side** decision made by the server's author. You'd have to fork, wrap, or build your own server. Major public servers (GitHub, OpenAI docs) generally do expose them.

**Q: Give the heuristic for choosing between exposing something as a tool versus a resource.**
A: **Tools are verbs, resources are nouns.** A tool is something the agent *does*; a resource is something it *reads*. Static, enumerable content — docs, templates, schemas, a source tree — belongs in a resource.

---

## Domain 2 — Community vs Custom Servers

**Q: What is the recommended starting point for a standard third-party integration, and when do you deviate?**
A: Start with a **community server** (GitHub, Notion, Asana, filesystem). Build a **custom** server only when the capability is specific to your organisation — internal docs or internal tools a public model cannot know about.

**Q: Name a Python route to building a custom MCP server.**
A: **FastMCP.**

**Q: The lecture says to enhance a community server's tool descriptions "via overrides in your `.mcp.json` file." What is wrong with that?**
A: **No such field exists.** A `.mcp.json` server entry accepts only `type`, `command`, `args`, `env`, `url`, `headers`, `headersHelper`, `timeout`, and `alwaysLoad`. Descriptions come from the server — to change them you fork, wrap, or modify the server.

**Q: If you can't override an MCP server's tool descriptions from config, what consumer-side levers do you have over its tools?**
A: **`alwaysLoad`** (force into context at session start), **permission rules** denying a tool by its full `mcp__<server>__<tool>` name, and **`enabledMcpServers` / `disabledMcpServers`** to allow or deny whole servers.

---

## Domain 2 — Competing With Built-in Tools

**Q: You connect a semantic code-search MCP server and Claude keeps using built-in `Grep`. Why?**
A: Your tool **competes with the built-ins** for selection, and Claude knows `Grep` intimately. A vague description ("tool to search the code base") claims nothing `Grep` doesn't already do, so Claude defaults to what it understands better.

**Q: Rewrite "Tool to search the code base" so it beats `Grep`, and name the three things the rewrite must do.**
A: *"Vector semantic search. Use instead of grep when the query describes intent or concept rather than an exact string. Returns ranked file paths with relevance scores."* It must name the **capability the built-in lacks**, say **when to prefer it over the named alternative**, and declare its **return contract**.

**Q: What is the first, highest-leverage fix for any wrong-tool-selection scenario?**
A: **Enhance the tool description.** This holds across three settings: overlap between your own tools (EP06), scoped access per agent (EP07), and competition with built-ins (EP08).

**Q: Which MCP server field helps Claude decide whether to *search* for your tools, and what should it contain?**
A: **Server instructions.** It should explain what category of tasks your tools handle, when Claude should search for them, and the key capabilities — similar to how skills work.

**Q: What is the size ceiling on MCP tool descriptions and server instructions in Claude Code?**
A: **2KB each** — they are truncated beyond that, so put critical details near the start.

---

## Domain 2 — Tool Loading

**Q: Does Claude Code load all MCP tool definitions into context at session start?**
A: **No.** **Tool search is enabled by default**: definitions are **deferred** and discovered on demand. Only **tool names and server instructions** load at session start, so adding servers has minimal context impact.

**Q: Give all five `ENABLE_TOOL_SEARCH` values and what each does.**
A: *(unset)* — all deferred, loaded on demand · **`true`** — all deferred, forced even through proxies · **`auto`** — load upfront if within **10%** of the context window, defer the overflow · **`auto:N`** — the same with a custom percentage · **`false`** — all loaded upfront, no deferral.

**Q: Which `.mcp.json` field exempts a server from deferred loading, and what does it cost?**
A: **`alwaysLoad: true`** — all that server's tools load at session start regardless of `ENABLE_TOOL_SEARCH`. Cost: it **blocks startup** until the server connects (capped at the 5-second connect timeout), and each upfront tool consumes context.

**Q: How can an MCP server mark a single tool as always-loaded rather than the whole server?**
A: Include **`"anthropic/alwaysLoad": true`** in that tool's **`_meta`** object.

**Q: State the trade-off between upfront and deferred tool loading.**
A: **Upfront** buys lower latency — everything is already in context — at the cost of **context pollution** that degrades selection and crowds out conversation. **Deferred** buys context back at the cost of a search round trip. `auto` is the middle setting.

**Q: Deferred loading, per-agent tool scoping, and hooks all limit tools. What different problem does each solve?**
A: **Deferral** manages **context** (what's loaded). **Scoping** manages **selection** (what an agent can choose). **Hooks** manage **permission** (whether a call may execute).

**Q: Does Claude Code cap how many tools an MCP server may expose?**
A: **No fixed per-server cap** — *"the practical limit is your context window budget."*

---

## Domain 2 — Decision Framework

**Q: Config must be shared with the team. Where does it go?**
A: **`.mcp.json` at the project root** (project scope), committed to version control — never `~/.claude.json`.

**Q: A secret is needed by an MCP server. Where does it go?**
A: **Not in the file.** Reference it as `${VAR}` in `.mcp.json` and set the value in the environment — shell `export`, `.zshrc`, container env, or Dockerfile.

**Q: The agent repeatedly searches for the same static documentation. What's the fix?**
A: Expose it as **MCP resources** so it appears as a catalog up front, instead of forcing exploratory search-tool calls.

**Q: Which claims about the exam's own structure in this episode are unverified?**
A: Whether the three named scenarios (scope, secrets, tool selection) appear in Anthropic's published sample set and in what form — the *content* of all three is verified against official docs, but the sample-question claim isn't checkable. Domain 2's exam weight percentage also isn't published in this vault.

**Q: The transcript repeatedly says "cloud.json". What file is meant?**
A: **`.claude.json`** — specifically `~/.claude.json` in the home directory, which holds both local-scope and user-scope MCP servers. It is the episode's most frequent transcription artifact.

*Back to: [[EP08 - MCP Servers, Config & Cline]]*
