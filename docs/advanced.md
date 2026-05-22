# Advanced Features

## Profile Import

Browser-act can import login state from local Chrome into managed browser instances.

### List Available Profiles

```bash
browser-act browser list-profiles
```

Returns importable sources:
- Local Chrome profiles (Default, Profile 1, Profile 2, etc.)
- Already-created Browser-act browsers (for cross-browser sync)

### Import a Profile

```bash
browser-act browser import-profile <browser_id> <profile_id>
```

Extracts cookies, localStorage, and IndexedDB from the source and injects them into the target browser.

**Options:**

```bash
# Allow Chrome restart for CDP access
browser-act browser import-profile abc123 chrome-default --allow-restart-chrome
```

### Prerequisites

- Source browser (Chrome) may need to be closed for local import
- Target browser must already exist (created via `browser create`)
- Must call `list-profiles` first to discover profile IDs

### What Gets Imported

| Included | Excluded |
|----------|----------|
| Cookies | Browsing history |
| localStorage | Bookmarks |
| IndexedDB | Extensions |
| Session storage | Cache |
| | Saved passwords |

### Import Modes

- **Local mode** (chrome → chrome): Direct file copy, fastest, most complete
- **CDP mode** (any → stealth, cross-type): Extraction via DevTools Protocol over network

### Risk Notes

Profile import has inherent risks that the agent must communicate to users:

1. Source browser may be closed during import
2. IP changes at the target location may trigger re-verification on some sites
3. Environment differences (fingerprint, location) may trigger re-login
4. Import is a snapshot — subsequent changes to the source do not propagate

## Remote Assist

When automation encounters obstacles that programmatic solutions cannot handle:

```bash
browser-act --session s1 remote-assist --objective "Complete 2FA verification"
```

The CLI returns a live remote URL. The user can open this link in **any browser on any device** to directly see and operate the automated browser — no need to be on the same machine, no software installation required.

### How It Works

1. Agent calls `remote-assist` with a target description
2. CLI returns a live remote URL
3. User opens the URL in any browser → sees the browser screen in real-time, can click, type, scroll
4. User completes manual operation (2FA, complex captcha, etc.)
5. User signals completion
6. Agent resumes automation

### After Remote Assist

Once `remote-assist` is called, the agent must not send further browser commands until the user signals completion. The Skill layer enforces this lockout.

### Use Cases

- Two-factor authentication requiring a phone
- Complex captchas that auto-solving cannot handle
- Enterprise SSO flows requiring hardware tokens
- Any situation in a workflow where human judgment is needed

## Proxy Configuration

### Dynamic Proxy (Managed)

Automatic IP rotation managed by Browser-act cloud service:

```bash
# Set at creation
browser-act browser create --type stealth --name s1 --desc "..." --dynamic-proxy US

# Update later
browser-act browser update <id> --dynamic-proxy JP

# List available regions
browser-act browser regions
```

IP rotates on every browser restart. Region determines exit node location.

### Custom Proxy

Use your own proxy server:

```bash
# HTTP proxy
browser-act browser create --type stealth --name s1 --desc "..." \
  --custom-proxy http://user:pass@host:port

# SOCKS5 proxy
browser-act browser update <id> --custom-proxy socks5://host:port
```

### Remove Proxy

```bash
browser-act browser update <id> --no-proxy
```

### Constraints

- Only stealth browsers support proxy configuration
- Dynamic proxy and custom proxy are mutually exclusive
- Proxy changes take effect on next `browser open`

## Network Capture & HAR Recording

### Real-Time Network Inspection

Browser-act automatically captures network traffic during sessions:

```bash
# View all requests
browser-act --session s1 network requests

# Filter by URL pattern
browser-act --session s1 network requests --filter api.example.com

# Filter by type
browser-act --session s1 network requests --type xhr,fetch

# Filter by method
browser-act --session s1 network requests --method POST

# Filter by status code range
browser-act --session s1 network requests --status 4xx

# Get full request/response details
browser-act --session s1 network request <request_id>

# Clear captured data
browser-act --session s1 network clear
```

### HAR Recording

Capture complete HAR (HTTP Archive) files for analysis:

```bash
browser-act --session s1 network har start
# ... perform operations ...
browser-act --session s1 network har stop ./capture.har
```

HAR files are compatible with Chrome DevTools, Fiddler, Charles, and other HTTP analysis tools.

### Use Cases

- Discovering API endpoints behind web UIs
- Debugging authentication flows
- Capturing data loaded via XHR that isn't in the HTML
- Performance analysis of page load sequences

## Browser Description Management

The `desc` field enables semantic browser selection across sessions:

### Setting Descriptions

```bash
# At creation
browser-act browser create --type chrome --name work \
  --desc "Work Chrome: logged into GitHub, Jira, Gmail"

# Overwrite (full rewrite)
browser-act browser update <id> --desc "Completely new description"

# Append (preserve history, add new info)
browser-act browser update <id> --desc-append "Now also logged into Figma"
```

### How Agents Use desc

When selecting a browser for a task:

1. Agent reads all browsers' `desc` from `browser list`
2. Matches task keywords against descriptions
3. Clear match → use that browser directly
4. No match → ask user which one
5. After doing something new with a browser → append to desc

This creates an ever-growing knowledge base of what each browser can do.

## Captcha Handling

### Auto-Solve

```bash
browser-act --session s1 solve-captcha
```

Attempts automated solving. Works for common captcha types (image selection, text recognition). Requires API Key.

### Escalation When Auto-Solve Fails

```bash
# Try automatic first
browser-act --session s1 solve-captcha

# If it fails, show to user
browser-act --session s1 browser open <id> <url> --headed
# User completes in the visible window

# Or escalate to remote
browser-act --session s1 remote-assist --objective "Please solve the captcha"
```

## Headed Mode

Display the browser window for manual inspection or user intervention:

```bash
browser-act --session debug browser open <id> https://example.com --headed
```

The browser appears on screen. The agent can still send commands while the user observes simultaneously. Use cases:
- Visually debugging automation issues
- User completes manual steps, agent handles the rest
- Demos and co-browsing scenarios

## Offline Mode

Completely disconnect a session's network:

```bash
browser-act --session s1 network offline on
# ... test offline behavior ...
browser-act --session s1 network offline off
```

## Cookie Import/Export

When Profile import is too heavy but you need specific cookies:

```bash
# Export from one session
browser-act --session s1 cookies export ./cookies.json

# Import into another session
browser-act --session s2 cookies import ./cookies.json
```

Use cases:
- Sharing login state between different browser types
- Persisting state in CI/CD pipelines
- Migrating sessions across machines

## Next Steps

- [Commands](commands.md) — Complete command list
- [Security](security.md) — Confirmation requirements for these features
- [Stealth & Extraction](stealth.md) — Anti-detection details
