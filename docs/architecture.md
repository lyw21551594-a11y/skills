# Architecture

## Three Browser Types, One Interface

Browser-act provides three browser types optimized for different scenarios. They share the same command interface — learn one, use them all.

```
┌─────────────────────────────────────────────────────────┐
│                  Unified CLI Interface                   │
│  navigate · state · click · input · screenshot · ...    │
├──────────────────┬──────────────────┬───────────────────┤
│     chrome       │  chrome-direct   │     stealth       │
│  Import Profile  │  CDP to local    │  Anti-detection   │
│  Standalone      │  Zero-config     │  Proxy rotation   │
│  Isolated proc   │  Active Chrome   │  Fingerprinting   │
└──────────────────┴──────────────────┴───────────────────┘
```

## chrome

**Purpose:** Reuse local Chrome login state, running in a standalone browser instance.

**How it works:**
1. Extracts Profile (Cookies, localStorage, IndexedDB) from local Chrome
2. Browser-act creates a standalone Chromium instance and loads that Profile
3. The original Chrome is unaffected — the copy runs independently

**Best for:**
- Already logged-in websites (Gmail, GitHub, Jira, etc.)
- Long-running automation tasks that should not interfere with daily browsing
- Scenarios requiring login state without depending on active Chrome

**Features:**
- Quota: up to 20 browsers
- Isolated process, separate from local Chrome
- Profile is a one-time snapshot — subsequent changes in local Chrome are not synced
- Supports headed mode for debugging

## chrome-direct

**Purpose:** Directly control a running Chrome via Chrome DevTools Protocol (CDP).

**How it works:**
1. Browser-act connects to local Chrome through the debugging port
2. All automation happens in your real Chrome — extensions, certificates, SSO all ready
3. The Agent operates within your existing browser environment

**Best for:**
- Enterprise SSO requiring specific browser configurations
- Websites depending on specific extensions or certificates
- Quick operations that don't need isolation
- Debugging while manually browsing

**Features:**
- Quota: 1 (your Chrome is the browser)
- Zero-config — no Profile import needed
- Extensions, bookmarks, history all available
- Requires explicit user confirmation before connecting
- Cannot set headed (it is already your browser)

**Trade-off:** While chrome-direct is running, your Chrome is being automated. The Agent can access all logged-in sessions.

## stealth

**Purpose:** Anti-detection browsing with fingerprint spoofing, proxy rotation, and privacy mode.

**How it works:**
1. Browser-act launches a specially configured browser with anti-scraping countermeasures
2. Fingerprint spoofing hides automation traces
3. Optional proxy rotation provides IP diversity

**Best for:**
- Websites with anti-scraping detection (Cloudflare, DataDome, etc.)
- Competitor monitoring without getting banned
- Multi-account isolation requiring unique fingerprints per browser
- Large-scale batch data collection

**Features:**
- Quota: up to 5 browsers
- Requires API Key (hosted service)
- Privacy mode: fresh fingerprint + Profile per session, not persisted
- Dynamic proxy: auto-rotates IPs by region
- Custom proxy: bring your own SOCKS5/HTTP proxy

## Unified Data Model

Regardless of type, every browser is represented with the same structure:

| Field | Description |
|-------|-------------|
| `id` | Unique identifier, auto-generated |
| `name` | Human-readable name |
| `type` | `chrome`, `chrome-direct`, or `stealth` |
| `desc` | Natural language purpose description |
| `dynamic_proxy` | Managed proxy region code (e.g., US, JP), mutually exclusive with custom_proxy. Stealth only |
| `custom_proxy` | Custom proxy address (e.g., socks5://host:port), mutually exclusive with dynamic_proxy. Stealth only |
| `private` | Whether privacy mode is enabled, defaults to false (stealth only) |
| `confirm_before_use` | Whether to prompt the user before each use |

### The `desc` Field

`desc` is Browser-act's cross-session memory. Store the browser's purpose in natural language:

```
"Taobao shopping account, logged in as user123. Used for price monitoring."
```

The Agent uses `desc` to:
- Semantically match browsers to tasks ("check Taobao prices" → finds the Taobao browser)
- Avoid recreating browsers for known workflows
- Accumulate usage knowledge for each browser over time

Updates use append semantics to preserve history:

```bash
browser-act browser update <id> --desc-append "Also used for order tracking"
```

## Session Model

Sessions are windows within a browser. A single browser can host multiple parallel sessions:

```
Browser (chrome, id=abc123)
├── Session "price-check"     ← Agent A's work
├── Session "order-tracking"  ← Agent B's work
└── Session "review-scrape"   ← Agent C's work
```

Sessions share the browser's login state but are independent in:
- Navigation (opening different pages)
- Tab state
- Network capture
- Dialog handling

See [Sessions](sessions.md) for details.

## Browser Selection Logic

When the Agent has multiple browsers, selection follows this priority:

1. **desc match** — Browser description explicitly matches the current task, use directly
2. **Single browser** — Only one browser exists, use directly (no need to ask)
3. **Ask user** — Multiple browsers with no clear match, list options

This logic is executed by the Skill layer, not the CLI.

## When to Switch Browser Types

In real-world usage, you often follow paths like these:

**Scenario 1: From chrome to stealth**

> You use a chrome browser (with imported local login state) to automate an e-commerce site. After running for a few days, the site starts blocking with CAPTCHAs. At this point, switch to a stealth browser — use a "clean identity" to continue, avoiding association.

**Scenario 2: From stealth to chrome-direct**

> You use a stealth browser to try logging into an enterprise internal system, but that system relies on a browser extension for authentication. The stealth browser doesn't have that extension. Switch to chrome-direct to use your local Chrome (with the extension already installed) to complete the task.

**Scenario 3: From chrome-direct to chrome**

> You ran an enterprise system automation task with chrome-direct successfully. But chrome-direct has only 1 quota, and you don't want to occupy your local Chrome every time. So import the login state into a chrome browser — run with chrome from now on, no longer depending on chrome-direct.

## Quotas

| Type | Max Count | Notes |
|------|-----------|-------|
| `chrome` | 20 | Each is an isolated process |
| `chrome-direct` | 1 | Only one local Chrome |
| `stealth` | Per account | Allocated based on account specifics |

## Next Steps

- [Commands](commands.md) — What each browser type can do
- [Sessions](sessions.md) — Parallel isolation model
- [Stealth & Extraction](stealth.md) — Deep dive into anti-detection
