# Installation

## Agent Integration (Recommended)

Simply tell your AI agent:

> Install browser-act Skill from https://github.com/browser-act/skills/tree/main/browser-act — verify it works after installation.

The agent will automatically handle CLI installation, Skill configuration, and verify everything works.

## Manual Installation

```bash
uv tool install browser-act-cli --python 3.12
```

Verify:

```bash
browser-act --version
```

## Authentication (Optional)

An API Key unlocks the following features:

- **stealth browsers** — Anti-detection fingerprint spoofing
- **stealth-extract** — One-command protected page extraction
- **dynamic proxy** — Managed IP rotation by region
- **solve-captcha** — Automatic captcha solving

Chrome and chrome-direct browsers work without authentication.

Get an API Key:

```bash
browser-act auth login
# Opens registration link → complete signup → poll for key
browser-act auth poll
```

Or set directly:

```bash
browser-act auth set <your-api-key>
```

## Upgrade

```bash
uv tool upgrade browser-act-cli
```

The Skill layer automatically detects CLI/Skill version mismatches and guides upgrades.

## Platform Support

| Platform | Status |
|----------|--------|
| Windows | Supported |
| macOS | Supported |
| Linux | Supported |

## Requirements

- Python 3.12+
- uv package manager
- Chrome/Chromium (for `chrome` and `chrome-direct` types)
- API Key (only for `stealth` type)

## Troubleshooting

### Command not found after install

Ensure the `uv` tool directory is in your PATH:

```bash
uv tool dir
# Add the output path to your shell PATH
```

### Diagnostics

Upload logs for issue diagnosis:

```bash
browser-act report-log
```

Send improvement suggestions:

```bash
browser-act feedback "Description of issue or suggestion"
```
