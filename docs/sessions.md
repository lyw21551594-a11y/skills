# Sessions

## What is a Session

A session is an independent browser window bound to a name. All interaction commands require the `--session` flag to specify which session to operate on.

```bash
browser-act --session my-task browser open <id> https://example.com
browser-act --session my-task state
browser-act --session my-task click 2
browser-act --session my-task session close my-task
```

The session name is your handle to a specific browser context. It persists until explicitly closed.

## Why Explicit Sessions

Unlike tools that use a single implicit browser, Browser-act requires named sessions because:

1. **Parallel safety** — Multiple agents (or conversations) can operate simultaneously without conflicts
2. **Clear ownership** — Each session belongs to the agent that created it
3. **Controlled lifecycle** — Sessions open with intent and close when done
4. **Multi-browser targeting** — Different sessions can point to different browsers

## Session Lifecycle

```
Create (browser open) → Use (state/click/input/...) → Close (session close)
```

### Creating a Session

The first time you use a `--session` name with `browser open`, the session starts:

```bash
browser-act --session price-check browser open abc123 https://shop.example.com
```

If you use the same session name again, it reuses the existing session — no duplicates created.

### Using a Session

All commands within the same session operate on the same browser window:

```bash
browser-act --session price-check state
browser-act --session price-check click 5
browser-act --session price-check get markdown
```

### Closing a Session

Sessions must be closed when work is done:

```bash
browser-act --session price-check session close price-check
```

This is mandatory hygiene. Unclosed sessions consume resources and may interfere with subsequent operations.

## Parallel Sessions

A single browser can host multiple sessions simultaneously. Each session is an independent window sharing the browser's login state:

```bash
# Two parallel tasks on the same browser
browser-act --session task-a browser open abc123 https://site.com/page1
browser-act --session task-b browser open abc123 https://site.com/page2

# They don't interfere with each other
browser-act --session task-a click 3
browser-act --session task-b input 1 "search keywords"
```

**Key principle:** For same-account parallel work — open a new session on the same browser rather than creating a new browser.

### Parallel Session Naming

Each parallel session must have a unique name. Names should reflect their purpose:

```bash
browser-act --session monitor-prices browser open shop1 https://shop.com
browser-act --session track-orders browser open shop1 https://shop.com/orders
```

## Session Ownership

Sessions have ownership semantics:

- A session belongs to the agent/conversation that created it
- Agents should not reuse sessions they didn't create
- `session list` shows all active sessions, but only operate on your own

When `get-skills core` returns environment state, it displays existing sessions. If sessions from other conversations exist, the agent should create new ones rather than taking over.

## Listing Sessions

```bash
browser-act session list
```

Returns all active sessions across all browsers, showing:
- Session name
- Associated browser ID
- Current URL

## Session-Browser Relationship

```
┌── Browser A (chrome) ───────────────────┐
│  Session "search"  → google.com        │
│  Session "monitor" → analytics.com     │
└────────────────────────────────────────┘

┌── Browser B (stealth) ──────────────────┐
│  Session "scrape"  → target-site.com   │
└────────────────────────────────────────┘
```

- A session belongs to exactly one browser
- A browser can have multiple sessions
- Sessions on the same browser share cookies/logins but navigate independently

## Commands That Don't Need Sessions

Some commands are browser-management or system-level and don't require `--session`:

- `browser list`, `browser create`, `browser delete`, `browser update`
- `browser regions`, `browser list-profiles`
- `session list`
- `auth login`, `auth poll`, `auth set`, `auth clear`
- `get-skills`, `report-log`, `feedback`
- `stealth-extract` (creates and destroys its own temporary context)

## Auto-Reclamation

Sessions that receive no commands for 8 hours are automatically reclaimed. No need to worry about forgotten sessions permanently consuming resources.

## Best Practices

1. **Descriptive naming** — `check-price` not `s1`
2. **Close when done** — Don't leave sessions hanging
3. **One browser, many sessions** — Prefer parallel sessions over duplicate browsers
4. **Unique names** — Don't reuse session names from other conversations
5. **One task, one session** — One logical task = one session

## Next Steps

- [Commands](commands.md) — Complete command list
- [Security](security.md) — Session isolation guarantees
- [Advanced](advanced.md) — Multi-browser orchestration patterns
