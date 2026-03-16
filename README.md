<p align="center">
  <strong>Workspace Bootstrap</strong><br>
  <em>AI Workspace Setup Wizard for Claude Code</em>
</p>

<p align="center">
  <a href="#install">Install</a> ·
  <a href="#quick-start">Quick Start</a> ·
  <a href="#how-it-works">How It Works</a> ·
  <a href="#commands">Commands</a>
</p>

---

## What is Workspace Bootstrap?

**Workspace Bootstrap** is an interactive setup wizard that builds your personal AI workspace for Claude Code.

Instead of manually editing config files, you answer a few questions — and the wizard generates:

- A **config file** with all your services, URLs, and accounts
- A **personal slash command** (e.g., `/my-workspace`) that opens everything in one shot

Next time you start working, just run your command. All your AI tools open in the right Chrome profile, with the right accounts, ready to go.

## Why?

Most developers waste 5–10 minutes every morning opening tabs, switching accounts, and getting their AI tools ready.

Workspace Bootstrap solves this:

```
❌ Before: Open Chrome → Navigate to 8 sites → Switch accounts → Arrange tabs → Start working
✅ After:  /my-workspace → Start working
```

The key insight: **don't sync Chrome to Google**. Instead, use a dedicated profile where each site stays logged in to its optimal account via Cookies.

## Install

```
/plugin install workspace-bootstrap
```

Or via SkillPort marketplace:

```
/plugin marketplace add skillport
/plugin install workspace-bootstrap@skillport
```

## Quick Start

### 1. Run the setup wizard

```
/workspace-bootstrap-setup
```

### 2. Answer the questions

The wizard asks you about each service:

```
🚀 Workspace Bootstrap — Setup Wizard

Step 1: Chrome profile name?  → AI-Workspace
Step 2: Service name?         → ChatGPT
Step 3: URL?                  → https://chat.openai.com (auto-suggested)
Step 4: Account?              → you@gmail.com
Step 5: Category?             → AI
Step 6: Add another?          → yes / no
```

It knows 40+ popular services and auto-suggests URLs:

| Service | Auto-suggested URL |
|---------|-------------------|
| ChatGPT | `https://chat.openai.com` |
| Claude | `https://claude.ai` |
| GitHub | `https://github.com` |
| Midjourney | `https://www.midjourney.com` |
| Notion | `https://www.notion.so` |
| Vercel | `https://vercel.com/dashboard` |
| ... | 40+ services built-in |

### 3. Get your personal command

At the end, you choose a name:

```
🎯 Choose a name for YOUR workspace command:
   /my-workspace
```

Done. Your personal command is ready.

### 4. Use it

```
/my-workspace
```

Every service opens in the right tab, with the right account, every time.

## How It Works

```
/workspace-bootstrap-setup          ← Run once (interactive wizard)
        │
        ├── ~/.claude/workspace-bootstrap/
        │   ├── sites.yaml              ← Your services config
        │   └── accounts.yaml           ← Your account mappings
        │
        └── ~/.claude/commands/
            └── my-workspace.md         ← YOUR personal command
                    │
                    ▼
            /my-workspace               ← Run daily (opens everything)
```

### Account Strategy

This plugin uses a dedicated Chrome profile **without Google sync**:

- Each site keeps its own login via Cookies
- Different accounts for different services (e.g., personal Gmail for ChatGPT, work Gmail for GitHub)
- First time: log in manually to each site
- After that: Cookies remember you — one command restores everything

### Under the Hood

When your personal command runs:

1. Reads `~/.claude/workspace-bootstrap/sites.yaml`
2. Connects to Chrome via Claude in Chrome MCP
3. Opens tabs by category order (AI → Creation → Dev → ...)
4. Reports status (opened / redirected / blocked)

## Commands

| Command | Description |
|---------|-------------|
| `/workspace-bootstrap-setup` | Interactive setup wizard |
| `/workspace-bootstrap-setup --reset` | Clear config and start over |
| `/{your-command}` | Launch your workspace |
| `/{your-command} ai` | Launch only one category |
| `/{your-command} --dry-run` | Preview without opening |

## Requirements

- [Claude Code](https://claude.ai/claude-code)
- [Claude in Chrome](https://chromewebstore.google.com/) extension
- Chrome or Chromium

## File Structure

```
workspace-bootstrap/
├── .claude-plugin/
│   └── plugin.json                    # Plugin manifest
├── .claude/skills/
│   ├── workspace-bootstrap-setup/
│   │   └── SKILL.md                   # Setup wizard (interactive)
│   └── workspace-bootstrap-run/
│       └── SKILL.md                   # Run engine
├── templates/
│   ├── sites.yaml.template            # Config template
│   ├── accounts.yaml.template         # Accounts template
│   └── command.md.template            # Command template
├── README.md
└── LICENSE
```

## FAQ

**Q: Can I add more services later?**
A: Yes. Run `/workspace-bootstrap-setup` again — it detects existing config and lets you add to it.

**Q: Can I have multiple workspaces?**
A: Yes. Run setup again with `--reset` and choose a different command name.

**Q: What if a site is blocked?**
A: Some sites (like Stripe) are blocked by Claude in Chrome safety restrictions. The wizard skips them gracefully and tells you to open them manually.

**Q: Does it store my passwords?**
A: No. It only stores email addresses (for account mapping). Passwords are never saved — login relies entirely on browser Cookies.

## License

MIT

---

<p align="center">
  Built for the Claude Code community · Distributed via <a href="#">SkillPort</a>
</p>
