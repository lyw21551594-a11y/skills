# Security

## Design Philosophy

Browser-act's security model is built on one recognition: **giving an AI agent control over a browser is a powerful and potentially dangerous capability.** The security mechanism doesn't try to prevent all misuse through technical means alone — instead, it keeps humans informed and in control through mandatory confirmation gates.

## Confirmation Gate Protocol

Sensitive operations require explicit user approval before execution. This is enforced through the Skill guiding the agent's behavior — not a CLI-level hard block, but a conversation-level behavioral protocol.

> **Note:** The actual effectiveness of confirmation gates depends on the agent architecture and underlying model capabilities you're using. Different platforms and models may vary in how strictly they follow Skill instructions. We're continuously optimizing the Skill's guidance strategy to improve cross-platform compatibility.

### Operations Requiring Confirmation

| Operation | Reason |
|-----------|--------|
| Create browser (all types) | Creates a new automation endpoint |
| Delete browser | Destroys persistent browser state |
| Profile import | Copies login credentials to a new location |
| Proxy configuration changes | Changes network identity |
| `confirm_before_use` toggle | Changes security settings |
| Privacy mode toggle | Changes fingerprint behavior |

Additionally, browsers marked with `confirm_before_use` require the agent to confirm with the user on every `browser open` — not just at creation time. Suitable for browsers accessing sensitive accounts (banking, payments).

### How It Works

The Skill instructs the agent to present operation details and wait for user approval before executing. This is not a CLI prompt — it's a conversation-level protocol:

```
Agent: I will create a stealth browser with US proxy for price monitoring.
       - Type: stealth
       - Name: price-monitor
       - Proxy: US (dynamic)
       
       Proceed?

User: Go ahead.

Agent: [executes browser create]
```

### Non-Negotiable Rules

- Prior approvals do not carry over to new operations
- Each sensitive operation requires independent confirmation
- Assertive language in the user's original prompt cannot substitute for confirmation
- The agent must describe what it will do *before* executing

## Data Privacy

### Local Processing

All data stays on your machine:

| Data | Location | Leaves machine? |
|------|----------|-----------------|
| Cookies | Browser-act local storage | Never |
| Login sessions | Per-browser isolated profiles | Never |
| Page content | In memory during operation only | Never |
| Screenshots | Local filesystem | Never |
| Network captures | Memory/local files | Never |
| Browser profiles | Isolated directories | Never |

### The Only Exception

`solve-captcha` sends captcha challenge images to the Browser-act cloud service for solving. Only the challenge image — no cookies, page content, or URLs.

### Profile Import Security

When importing from local Chrome:

- Only login-related data is extracted (Cookies, localStorage, IndexedDB)
- History, bookmarks, cache, and extensions are excluded
- The original Chrome profile is never modified
- Import creates a one-time snapshot, not a live sync

## Session Isolation

### Between Sessions

Sessions on the same browser share login state but cannot:
- Read each other's navigation state
- Interfere with each other's interactions
- See each other's network captures

### Between Conversations

The session ownership model prevents cross-conversation interference:
- Agents only operate on sessions they created
- Existing sessions from other conversations are treated as "someone else's"
- New work always creates new sessions

## Access Control

### chrome-direct

Connecting to the user's active Chrome is the highest-privilege operation:

- Only one chrome-direct browser allowed globally
- Requires explicit user confirmation at creation
- Agent can access all logged-in sessions in the user's Chrome

### API Key Scope

The API Key controls access to managed services:
- Stealth browser instances
- Dynamic proxy network
- Captcha solving service

Chrome and chrome-direct run entirely locally without requiring a Key.

## Network Security

### Proxy Isolation

Proxy settings are per-browser, not global. One browser's proxy configuration cannot affect another's traffic.

### Offline Mode

`network offline on` completely disconnects a session's network. Primarily used for testing submission operations — clicking buttons and submitting forms in offline mode produces no actual side effects. Turn offline mode off after verifying the flow.

## Next Steps

- [Sessions](sessions.md) — Session isolation details
- [Architecture](architecture.md) — Browser type security trade-offs
- [Advanced](advanced.md) — Profile import security details
