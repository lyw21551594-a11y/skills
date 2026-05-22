# Skills

## Installing the Skill

Tell your agent:

> Install browser-act Skill from https://github.com/browser-act/skills/tree/main/browser-act

The agent will automatically download the SKILL file to its Skills directory. Once installed, the agent becomes aware of Browser-act and will automatically invoke it when appropriate.

## Two-Layer Architecture

Browser-act uses a two-layer Skill architecture:

```
┌─────────────────────────────────────────┐
│  Entry Skill (the installed file)       │
│  Lightweight, stable, rarely changes    │
│  Teaches the agent: "browser-act exists"│
├─────────────────────────────────────────┤
│  Runtime Content (get-skills CLI)       │
│  Environment-aware, version-matched     │
│  Teaches the agent: "use it like this"  │
└─────────────────────────────────────────┘
```

### Layer 1: Entry Skill

The file installed to the agent's Skills directory. It's intentionally minimal:
- Triggers agent awareness of Browser-act
- Contains trigger words for activation
- Points the agent to `get-skills` for actual instructions

The entry Skill rarely changes — it's the stable entry point.

### Layer 2: Runtime Content via `get-skills`

The agent's first command must always be:

```bash
browser-act get-skills core --skill-version <version>
```

This returns everything the agent needs for the current session:
- **Environment state** — CLI version, API Key status
- **Browser list** — All available browsers with IDs, types, descriptions
- **Active sessions** — Currently running sessions
- **Core commands** — Interaction reference
- **Directives** — Dynamic rules based on current state

### Why This Design

**Solves version drift:** The installed Skill file points to `get-skills`. The CLI always returns content matching its own version. No stale instructions.

**Environment awareness:** `get-skills` returns actual current state — what browsers exist, which sessions are active, whether the API Key is configured. The agent starts with complete situational awareness.

**Progressive disclosure:** `get-skills core` covers 80% of use cases. `get-skills advanced` loads additional capabilities only when needed.

## Topics

| Command | Content | When to use |
|---------|---------|-------------|
| `get-skills core --skill-version <v>` | Core workflow, commands, environment state | Always first |
| `get-skills advanced` | Proxy, Profile, privacy mode, advanced features | When core isn't enough |
| `get-skills main` | Latest SKILL.md content for self-update | When version mismatch detected |

## Version Compatibility

The `--skill-version` parameter lets the CLI detect incompatibilities:

```bash
# Skill claims to be version 2.0.2
browser-act get-skills core --skill-version 2.0.2
```

If CLI version is incompatible with Skill version, the output includes upgrade guidance:
- CLI too old → `uv tool upgrade browser-act-cli`
- Skill too old → `browser-act get-skills main` to get latest Skill content

## Directives: Dynamic Guidance

`get-skills` output includes "Directives" — context-aware rules generated based on current state:

**Multi-browser directive:** When multiple browsers exist, provides selection guidance based on `desc` matching.

**Session directive:** When existing sessions are found, reminds the agent of ownership rules.

**Authentication directive:** When API Key is missing, steers away from stealth operations.

Directives are Browser-act's mechanism for adapting guidance to the agent's current situation without hardcoding every scenario in the Skill.

## Agent Compatibility

The Skill system works with any agent that can:
1. Read SKILL files from a Skills directory
2. Execute shell commands
3. Parse text output

Compatibility list:
- **Claude Code** — SKILL.md in `.claude/skills/`
- **GitHub Copilot** — Discovered via Skills directory
- **Cursor** — Rules/Skills directory
- **Windsurf** — Agent Skills system
- **Google Gemini CLI** — AGENTS.md integration

## Workflow from the Agent's Perspective

```
1. User says "check the price on example.com"
2. Agent's entry Skill activates (trigger word match)
3. Agent runs: browser-act get-skills core --skill-version 2.0.2
4. Agent receives: environment state + browser list + commands + dynamic directives
5. Agent selects appropriate browser (desc matching)
6. Agent confirms with user (confirmation gate)
7. Agent executes browser operations
8. Agent closes session, updates browser desc
```

### Key Design Points

**Progressive loading reduces noise.** Most tasks only need `get-skills core`. Advanced capabilities like proxy configuration and Profile import load on-demand via `get-skills advanced`.

**Version compatibility is automatic.** The Skill file declares its version, the CLI checks compatibility. When incompatible, the output directly includes upgrade guidance that the agent executes automatically.

**desc is cumulative memory.** After each browser use, the agent appends new discoveries to desc. Next time a similar task comes up, it matches directly without asking the user again.

## Next Steps

- [Installation](installation.md) — How to install the Skill
- [Quick Start](quick-start.md) — First automation workflow
- [Architecture](architecture.md) — Understanding what get-skills reports
