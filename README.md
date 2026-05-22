<div align="center">
  <a href="https://www.browseract.com" style="text-decoration: none;">
    <img src="https://browseract-prod.browseract.com/prod/tools/20260205-154549.png" alt="BrowserAct Logo" width="150">
  </a>
  <h1>BrowserAct Skills</h1>

  <p>
    <a href="https://discord.com/invite/UpnCKd7GaU"><img src="https://img.shields.io/discord/1234567890?label=Discord&logo=discord&color=7289DA" alt="Discord"></a>
    <a href="https://github.com/browser-act/skills/stargazers"><img src="https://img.shields.io/github/stars/browser-act/skills?style=social" alt="GitHub Stars"></a>
    <a href="https://github.com/browser-act/skills/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="License"></a>
    <br><br>
    <a href="https://www.browseract.com"><img src="https://img.shields.io/badge/Website-BrowserAct.com-success" alt="Website"></a>
    <a href="https://x.com/browseract"><img src="https://img.shields.io/badge/X-browseract-000000?style=flat&logo=x&logoColor=white" alt="X (Twitter)"></a>
    <a href="https://www.linkedin.com/company/browseract/"><img src="https://img.shields.io/badge/LinkedIn-BrowserAct-0A66C2?style=flat&logo=linkedin&logoColor=white" alt="LinkedIn"></a>
    <a href="https://www.youtube.com/@browseract"><img src="https://img.shields.io/badge/YouTube-@browseract-FF0000?style=flat&logo=youtube&logoColor=white" alt="YouTube"></a>
  </p>
</div>

---

Browser automation CLI built for AI agents. Break through anti-bot walls, hand off to humans across platforms when stuck. Parallel multi-task execution, independent multi-session operation, isolated multi-account browsing.

## Why BrowserAct

AI agents need more than a headless Chrome wrapper — they need a complete browser automation platform:

**Go where standard tools can't — three-layer anti-blocking, progressively escalating:**

1. **Environment layer** — stealth fingerprints, TLS rotation, proxy switching. Most scenarios resolved here without triggering any challenge.
2. **Execution layer** — `solve-captcha` auto-solves common CAPTCHAs; `stealth-extract` pulls protected pages in one command, fully unattended.
3. **Human interaction layer** — `remote-assist` generates a live URL; user opens it on any device to take over. Once done, agent continues seamlessly.

**Concurrent sessions, zero interference:**

- Same-browser multi-session — shared login state, independent execution, tasks don't affect each other
- Cross-browser multi-session — different browsers operating simultaneously, fully independent
- Multi-account isolation — each browser has its own fingerprint, proxy, and cookies; websites cannot correlate them

**Three browser types for different scenarios:**

| Type | Use Case | Key Feature |
|------|----------|-------------|
| `chrome` | Reuse local Chrome logins | Import Profile, run independently |
| `chrome-direct` | Control your running Chrome via CDP | Zero config, full extensions + SSO |
| `stealth` | Anti-detection browsing | Fingerprint spoofing, proxy rotation, batch collection |

All three share the same command interface. Learn one, use all.

**Designed for agent context efficiency:**

- Compact text output, consuming fewer tokens than JSON
- 50+ commands covering navigation, forms, screenshots, network capture, cookie management

**Security: confirmation gating** — sensitive operations (creating browsers, deletion, importing Profiles) require explicit user approval. No exceptions. Prior approvals do not carry over. Enforced at the Skill layer, not a configuration toggle.

---

## Install

Tell your AI agent:

> Install browser-act Skill from https://github.com/browser-act/skills/tree/main/browser-act

[Installation details →](docs/installation.md)

---

## Quick Start

```bash
# Extract protected page content (zero config)
browser-act stealth-extract https://example.com

# Full browser automation
browser-act --session my-task browser open <id> https://example.com
browser-act --session my-task state          # See clickable elements
browser-act --session my-task click 3        # Click by index
browser-act --session my-task input 2 "hi"   # Type into field
```

[More examples and workflows →](docs/quick-start.md)

The agent runs `get-skills` at the start of each session — gets environment state, browser list, and commands in one call:

```bash
browser-act get-skills core --skill-version 2.0.2
```

[How agents discover and use BrowserAct →](docs/skills.md)

---

## Compatibility

**OS:** Windows, macOS, Linux

**Agents:** Claude Code · Cursor · VS Code · OpenCode · OpenClaw · Codex · Gemini CLI — works with any agent that can execute shell commands and load Skills.

---

## Documentation

Want to understand how BrowserAct works under the hood? Full documentation covers architecture, commands, sessions, stealth, security, and advanced features.

[Read the full documentation →](docs/README.md)

---

## Also From BrowserAct

### Skill Forge — Generate a Skill for Any Website

Need to extract data from the same website repeatedly at scale? Don't write scrapers by hand. **Skill Forge** explores a site once, discovers its APIs and data patterns, generates a deploy-ready Skill package, then runs reliably without re-exploration — 500 or 5,000 records through the same stable path.

**Any website. Any data. One command to start:**

> Install browser-act-skill-forge Skill from https://github.com/browser-act/skills/tree/main/browser-act-skill-forge

Then tell your agent what you need:

> *"Forge a Skill that extracts job listings from LinkedIn — title, company, salary, URL."*

[Skill Forge documentation →](docs/skill-forge.md)

### Solutions Catalog

30+ pre-built Skills already generated by Skill Forge, ready to install and run. Covers Amazon, Google Maps, YouTube, Reddit, WeChat, Zhihu, and more.

[Browse the full Solutions Catalog →](solutions/README.md)

### Build Your Own

Can't find what you need above? Generate a custom Skill for **any website** in minutes — no coding required. Just describe what data you want or what action to perform, and Skill Forge handles the rest.

---

## 💖 Support the Project

BrowserAct Skills is **free and open source**. If it saves you time, please give us a ⭐ **Star** — it keeps the project alive and helps us ship more skills.

<a href="https://github.com/browser-act/skills/stargazers">
  <img src="https://img.shields.io/github/stars/browser-act/skills?style=social" alt="GitHub Stars">
</a>

🎁 **Bonus:** Once you star the repository, you can join our [Discord](https://discord.com/invite/UpnCKd7GaU) and post in the `#claim-500-credits` channel to receive **500 free credits**!

### 🤝 Community & Support
- 💬 [Join our Discord](https://discord.com/invite/UpnCKd7GaU)
- 📖 [Read the Docs](https://docs.browseract.com)
- 🐛 [Report an Issue](https://github.com/browser-act/skills/issues)
- 🌐 [BrowserAct Website](https://www.browseract.com)

<p align="center"><em>Built with ❤️ by the BrowserAct Team</em></p>
