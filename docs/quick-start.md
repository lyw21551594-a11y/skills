# Quick Start

## Up and Running in 60 Seconds

### Content Extraction (Zero Configuration)

Just need to get rendered page content with anti-bot bypass:

```bash
browser-act stealth-extract https://example.com
```

Returns page content in markdown format. No browser management needed, no session naming required. Done.

```bash
# Get raw HTML
browser-act stealth-extract https://example.com --content-type html

# Use proxy to access geo-restricted content
browser-act stealth-extract https://example.com --dynamic-proxy US
```

### Full Browser Automation

Interactive workflows — login, fill forms, click actions:

```bash
# 1. List available browsers
browser-act browser list

# 2. Open browser to target URL
browser-act --session my-task browser open <browser-id> https://example.com

# 3. View interactive elements (returns indexed list)
browser-act --session my-task state

# 4. Interact using element indices
browser-act --session my-task click 2
browser-act --session my-task input 3 "hello@example.com"

# 5. Close when done
browser-act --session my-task session close my-task
```

## Core Workflow Pattern

Every Browser-act interaction follows the same loop:

```
Open → State → Interact → State → ... → Close
```

1. **Open** browser to target URL
2. **State** view the page (title, URL, indexed interactive elements)
3. **Interact** operate using element indices (click, input, select, etc.)
4. **State** check the result
5. Repeat until task is complete
6. **Close** the session

## Understanding `state` Output

The `state` command is your eyes. It returns the page URL, title, and an indexed element tree:

```
url=https://example.com/login
title=Login

*[1]<div id=login-form />
  *[2]<input type=email placeholder=Email address />
  *[3]<input type=password placeholder=Password />
  *[4]<button id=submit />
    Sign In
  *[5]<a />
    Forgot password?
```

Each `*[N]` is an interactive element. Use that index in commands:

```bash
browser-act --session login input 2 "user@example.com"
browser-act --session login input 3 "password123"
browser-act --session login click 4
```

## Command Chaining

When intermediate output is not needed, chain commands with `&&`:

```bash
browser-act --session s1 input 2 "user@example.com" && \
browser-act --session s1 input 3 "secret" && \
browser-act --session s1 click 4
```

When you need to read output between steps (e.g., check `state` before deciding what to click), execute them separately.

## Differentiating Scenarios

The following scenarios are capabilities unique to Browser-act compared to general-purpose automation tools:

### Extracting Content from Protected Websites

Regular crawlers blocked by Cloudflare? One command handles it:

```bash
browser-act stealth-extract https://anti-bot-site.com
```

Content that requires a specific region's IP to access:

```bash
browser-act stealth-extract https://geo-restricted.com --dynamic-proxy JP
```

### Multi-Account Parallel Monitoring

Three competitor stores, each with independent identities, scraped simultaneously:

```bash
# Each stealth browser has independent fingerprint and proxy, sites cannot correlate them
browser-act --session shop-a browser open stealth1 https://competitor-a.com
browser-act --session shop-b browser open stealth2 https://competitor-b.com
browser-act --session shop-c browser open stealth3 https://competitor-c.com
```

### Handling CAPTCHA Interruptions

CAPTCHA pops up during automation — no need to abort the task:

```bash
# Try automatic solving first
browser-act --session s1 solve-captcha

# Auto-solve failed? Let the user take over remotely
browser-act --session s1 remote-assist --objective "Please complete the CAPTCHA"
# After the user completes it remotely, the Agent continues with subsequent steps
```

### Reusing Existing Login State

Don't want to log in to GitHub again? Import your local Chrome's login state:

```bash
browser-act browser list-profiles
# Select your Chrome Profile
browser-act browser import-profile <browser-id> <profile-id>
# Already logged in when opened
browser-act --session gh browser open <browser-id> https://github.com
```

### Enterprise SSO Pass-Through

Corporate intranet requires specific certificates and extensions? Control your Chrome directly:

```bash
browser-act --session work browser open <browser-id> https://internal.corp.com
# Your certificates, extensions, and SSO cookies are all ready
```

## Choosing a Browser Type

| I need to... | Use |
|---|---|
| Reuse Chrome logins (Gmail, GitHub, etc.) | `chrome` + Profile import |
| Directly control local Chrome (SSO, certs, extensions) | `chrome-direct` |
| Scrape websites with anti-bot protection | `stealth` |
| Quick content extraction without sessions | `stealth-extract` |

See [Architecture](architecture.md) for details.

## Next Steps

- [Architecture](architecture.md) — Deep dive into browser types
- [Commands](commands.md) — Complete command list
- [Sessions](sessions.md) — Parallel operations and session lifecycle
