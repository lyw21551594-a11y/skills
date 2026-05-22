# Stealth & Extraction

## Three-Layer Anti-Detection System

Browser-act doesn't rely on a single technique to bypass website blocks. Instead, it uses three progressively escalating layers:

```
Environment Layer (reduce verification triggers) → Execution Layer (auto-solve verification) → Human Interaction Layer (remote fallback)
```

Most scenarios are resolved at the environment layer; the few that trigger verification are handled automatically by the execution layer; only extreme cases require human intervention.

## Environment Layer: Make the Website Think You're a Real User

### stealth-extract: Zero-Config Content Extraction

When you just need to read page content, one command does it all:

```bash
browser-act stealth-extract https://protected-site.com
```

**What it does:**
1. Launches a browser with full fingerprint spoofing and anti-detection
2. Navigates to the target URL
3. Waits for content to render (including JavaScript execution)
4. Returns page content in markdown (or HTML) format
5. Closes the browser — no cleanup needed

**No session, no browser management, no state.** Input a URL, get content back.

```bash
# HTML output
browser-act stealth-extract https://example.com --content-type html

# Use managed proxy (region-specific IP)
browser-act stealth-extract https://example.com --dynamic-proxy US

# Use custom proxy
browser-act stealth-extract https://example.com --custom-proxy socks5://host:port

# Custom timeout (default 30 seconds)
browser-act stealth-extract https://example.com --timeout 60

# Save to file
browser-act stealth-extract https://example.com --output ./result.md
```

**Rule:** Just need to *read* — use `stealth-extract`. Need to *interact* — use a stealth browser.

### Stealth Browser: Full Automation with Anti-Detection

When you need stateful interaction on protected websites, create a stealth browser:

```bash
browser-act browser create \
  --type stealth \
  --name "competitor-monitor" \
  --desc "Competitor price monitoring" \
  --dynamic-proxy US

browser-act --session monitor browser open <stealth-id> https://protected-site.com
browser-act --session monitor state
browser-act --session monitor click 3
```

### Anti-Detection Capabilities

| Technology | Purpose |
|------------|---------|
| Fingerprint spoofing | Canvas, WebGL, fonts, plugins — all consistently faked |
| Navigator patching | `webdriver`, `chrome.runtime`, plugin array normalization |
| Headless hiding | Passes headless detection tests |
| TLS fingerprint rotation | Matches real browser TLS signatures |
| Consistent persona | All fingerprint components tell the same story |

### Proxy System

Two proxy modes (mutually exclusive) to further reduce correlation risk:

**Dynamic Proxy (Managed)**
```bash
browser-act browser create --type stealth --name s1 --desc "..." --dynamic-proxy US
```
- IP rotates automatically on every browser restart
- Query available regions: `browser-act browser regions`

**Custom Proxy (BYO)**
```bash
browser-act browser create --type stealth --name s1 --desc "..." --custom-proxy socks5://user:pass@host:port
```

### Privacy Mode

When enabled, each session uses a fresh fingerprint and empty profile, with no data persisted:

```bash
browser-act browser create --type stealth --name "ephemeral" --desc "One-off tasks" --private
```

Or toggle on an existing browser:

```bash
browser-act browser update <stealth-id> --private true
```

Use cases: multi-account isolation, preventing fingerprint accumulation, one-off operations. Trade-off: cannot retain login state.

## Execution Layer: Auto-Solve Verification

When the environment layer doesn't fully prevent verification triggers:

```bash
browser-act --session s1 solve-captcha
```

Automatically identifies and solves common captcha types (image selection, text recognition, etc.). No human intervention needed.

## Human Interaction Layer: Remote Collaboration Fallback

When auto-solving also fails, escalate to a human:

```bash
# Option A: Show browser window for local user operation
browser-act --session s1 browser open <id> <url> --headed

# Option B: Generate remote link for user on any device
browser-act --session s1 remote-assist --objective "Please complete the captcha verification"
```

`remote-assist` returns a live remote URL. The user opens it in any browser on any device to see and operate the automated browser in real-time. Once the human finishes, the agent seamlessly continues.

## Batch Collection Mode

Multiple targets in parallel, each session operating independently:

```bash
browser-act --session target-1 browser open stealth1 https://site1.com
browser-act --session target-2 browser open stealth1 https://site2.com
browser-act --session target-3 browser open stealth1 https://site3.com
```

Combined with privacy mode, each session appears as a different user.

## Next Steps

- [Security](security.md) — Confirmation gate and data privacy
- [Advanced](advanced.md) — Proxy configuration details, Profile import
- [Commands](commands.md) — Complete command list
