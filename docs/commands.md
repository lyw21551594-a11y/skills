# Command Reference

Complete command list for Browser-act CLI.

## What do I want to do?

| I want to... | Use these commands |
|---------|-----------|
| Extract protected website content | `stealth-extract` |
| Open a webpage and interact | `browser open` → `state` → `click`/`input` |
| Fill and submit a form | `state` → `input` → `select` → `click` |
| Take a screenshot for archiving | `screenshot` / `screenshot --full` |
| Find the API behind a page | `network requests --type xhr,fetch` → `network request <id>` |
| Handle captchas | `solve-captcha` → `remote-assist` |
| Run multiple accounts in parallel | Open multiple browsers each with `--session` |
| Download page as markdown | `get markdown` |
| Handle a dialog popup | `dialog accept` / `dialog dismiss` |
| Wait for page to finish loading | `wait stable` |

---

## Global Options

| Option | Description |
|------|------|
| `--session <name>` | Session name (required for browser interaction commands) |
| `--format text\|json` | Output format |
| `--no-auto-dialog` | Disable automatic dialog handling |

## Navigation (requires `--session`)

| Command | Description |
|------|------|
| `navigate <url>` | Navigate to URL in the current tab |
| `navigate <url> --new-tab` | Open URL in a new tab |
| `back` | Go back |
| `forward` | Go forward |
| `reload` | Reload the current page |

## Page State (requires `--session`)

| Command | Description |
|------|------|
| `state` | Get URL, title, and indexed list of interactive elements |
| `screenshot [path]` | Take a screenshot (save to specified path or temp directory) |
| `screenshot --full` | Full-page screenshot |

`state` is the core observation command. It returns:
- Current URL and page title
- Page element tree, with interactive elements indexed as `*[N]`
- Each element shows tag type, attributes, and text content

## Page Interaction (requires `--session`)

| Command | Description |
|------|------|
| `click <index>` | Click an element by index |
| `type <text>` | Type text into the currently focused element |
| `input <index> <text>` | Click an element then type text |
| `keys <key_combo>` | Send keyboard keys (Enter, Tab, Ctrl+a, etc.) |
| `select <index> <option>` | Select a dropdown option (by visible text) |
| `hover <index>` | Hover over an element |
| `scroll up\|down [--amount N]` | Scroll the page |
| `scrollintoview --selector <css>` | Scroll an element into the viewport |
| `upload <index> <path>` | Upload a file to a file input |

### Keyboard Combinations

`keys` supports modifier key combinations:

```bash
browser-act --session s1 keys Enter
browser-act --session s1 keys Tab
browser-act --session s1 keys Ctrl+a
browser-act --session s1 keys Ctrl+c
browser-act --session s1 keys Shift+Tab
```

## Data Extraction (requires `--session`)

| Command | Description |
|------|------|
| `get title` | Page title |
| `get html` | Full page HTML |
| `get html --selector <css>` | HTML of a specified element |
| `get text <index>` | Get element text content by index |
| `get value <index>` | Get the value of an input/textarea |
| `get markdown` | Convert page to markdown |

## JavaScript Execution (requires `--session`)

```bash
browser-act --session s1 eval "document.title"
browser-act --session s1 eval "document.querySelectorAll('a').length"
browser-act --session s1 eval --stdin < script.js
```

`eval` executes arbitrary JavaScript in the page context and returns the result.

## Wait Commands (requires `--session`)

| Command | Description |
|------|------|
| `wait stable` | Wait for the page to stabilize (document ready + network idle) |
| `wait stable --timeout 10` | Custom timeout (seconds) |
| `wait selector <index>` | Wait for an element by index |
| `wait selector --selector <css>` | Wait by CSS selector |
| `wait selector --state visible\|hidden\|attached\|detached` | Wait for a specified state |
| `wait selector --timeout 10` | Custom timeout |

## Network Inspection (requires `--session`)

| Command | Description |
|------|------|
| `network requests` | List all captured requests |
| `network requests --filter <substring>` | Filter by URL substring |
| `network requests --type xhr,fetch` | Filter by resource type |
| `network requests --method POST` | Filter by HTTP method |
| `network requests --status 2xx` | Filter by status code |
| `network request <request_id>` | Full details of a single request (headers, body, response body) |
| `network clear` | Clear captured requests |
| `network offline on\|off` | Toggle network offline mode |
| `network har start` | Start HAR recording |
| `network har stop [path]` | Stop and save HAR file |

## Tab Management (requires `--session`)

| Command | Description |
|------|------|
| `tab list` | List open tabs and their IDs |
| `tab switch <tab_id>` | Switch tab by ID |
| `tab close [tab_id]` | Close a tab (closes the current tab if not specified) |

Use `navigate <url> --new-tab` to open a URL in a new tab.

## Dialog Handling (requires `--session`)

| Command | Description |
|------|------|
| `dialog accept [text]` | Accept a dialog (optional prompt text) |
| `dialog dismiss` | Cancel/close a dialog |
| `dialog status` | Check if a dialog is open |

By default, `alert` and `beforeunload` dialogs are automatically accepted. Use `--no-auto-dialog` to handle all dialogs manually.

## Cookie Management (requires `--session`)

| Command | Description |
|------|------|
| `cookies get` | Get all cookies |
| `cookies get --url <url>` | Get cookies for a specified URL |
| `cookies set <name> <value>` | Set a cookie |
| `cookies set <name> <value> --domain <d> --path <p> --secure --http-only` | Set with options |
| `cookies clear` | Clear all cookies |
| `cookies clear --url <url>` | Clear cookies for a specified URL |
| `cookies export <file>` | Export to a JSON file |
| `cookies import <file>` | Import from a JSON file |

## Captcha & Remote Assist (requires `--session`)

| Command | Description |
|------|------|
| `solve-captcha` | Attempt to automatically solve a captcha |
| `captcha-aid` | Assist with the current page captcha |
| `remote-assist` | Return a live remote URL that the user can open in any browser to operate |
| `remote-assist --objective "Complete 2FA"` | With a reason description |

## Session Management

| Command | Description |
|------|------|
| `session list` | List all active sessions |
| `session close [name]` | Close a session (closes the current session if not specified) |

## Browser Management

| Command | Description |
|------|------|
| `browser list` | List all browsers |
| `browser open <id> [url]` (requires `--session`) | Open a URL in a browser |
| `browser open <id> [url] --headed` (requires `--session`) | Show the browser window |
| `browser create --type <t> --name <n> --desc <d>` | Create a browser |
| `browser update <id> [options]` | Update browser settings |
| `browser delete <id>` | Delete a browser |
| `browser regions` | List proxy regions |
| `browser list-profiles` | List importable profiles |
| `browser import-profile <browser_id> <profile_id>` | Import a profile |

### browser create Options

```bash
browser-act browser create \
  --type chrome|chrome-direct|stealth \
  --name "my-browser" \
  --desc "Purpose description" \
  --source-profile <profile_id> \
  --dynamic-proxy <region> \
  --custom-proxy <url> \
  --private \
  --confirm-before-use
```

### browser update Options

```bash
browser-act browser update <id> \
  --name "New name" \
  --desc "Override description" \
  --desc-append "Append to description" \
  --dynamic-proxy <region> \
  --custom-proxy <url> \
  --no-proxy \
  --private true|false \
  --confirm-before-use|--no-confirm-before-use
```

## Stealth Extract

```bash
browser-act stealth-extract <url>
browser-act stealth-extract <url> --content-type html|markdown
browser-act stealth-extract <url> --dynamic-proxy <region>
browser-act stealth-extract <url> --custom-proxy <proxy-url>
browser-act stealth-extract <url> --timeout 60
browser-act stealth-extract <url> --output ./result.md
```

Standalone anti-detection content extraction. No session required.

## Authentication

| Command | Description |
|------|------|
| `auth login` | Get a registration link |
| `auth poll` | Check registration status |
| `auth set <api_key>` | Manually set an API key |
| `auth clear` | Remove the API key |

## Skills & System

| Command | Description |
|------|------|
| `get-skills core --skill-version <v>` | Get core skill content |
| `get-skills advanced` | Get advanced feature content |
| `get-skills main` | Get the latest skill file (for self-update) |
| `report-log` | Upload logs for diagnostics |
| `feedback <message>` | Send improvement suggestions |
