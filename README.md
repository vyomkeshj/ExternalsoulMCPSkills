# ExternalSoul MCP Skills

Claude skills for driving an [ExternalSoul](https://externalsoul.com) workspace over MCP —
from Claude Code, Claude Desktop, or any MCP-capable client.

An ExternalSoul workspace is an event-sourced canvas of apps (a real cloud Chrome, a Python
sandbox, PDF/Word/Excel viewers, spreadsheets, email, agents…). These skills teach Claude how
to drive those apps well: the right tool order, the safety gates, the cost model, and the
honest-reporting rules.

## Skills

| Zip | What it does |
|---|---|
| `cloud-browser-skill.zip` | Drive the workspace's **cloud browser**: browse and read sites, click/fill/submit with the built-in approval gate, work the user's logged-in sites (they log in by hand — you never touch credentials), tabs and page-waits, **download files (even behind logins) into the workspace**, process them in the code sandbox, and open the result in the right app. |
| `porkbun-skill.zip` | Work a **Porkbun** account through the logged-in cloud browser: list domains + expiry dates, and add/change DNS records (TXT/site-verification/A/CNAME/MX) — knows Porkbun's staged Add-Record → Submit-Records flow, its 2FA step-up walls, the TXT-textarea trap, and verifies records from a live DNS resolver. |
| `math-explainer-video-skill.zip` | Make a narrated, manim-animated maths explainer video end-to-end in the workspace (sandbox render → video editor assembly → one mp4 delivered). |

## Install

**Claude Code** — unzip into your skills directory and the skill autoloads:

```bash
unzip cloud-browser-skill.zip -d ~/.claude/skills/          # user-wide
# or per-project:
unzip cloud-browser-skill.zip -d .claude/skills/
```

**Claude Desktop** — Settings → Capabilities → Skills → upload the zip.

## Connect ExternalSoul over MCP

Two ways; the skills work with either:

1. **Your own account (full access):** `pip install esoul[mcp]`, then register `esoul-mcp`
   with your ExternalSoul access token (create one under Sharing → Access Tokens; use a
   read+write token scoped to the workspaces you want driven).
2. **A storefront connection:** any ExternalSoul account can expose workspaces at
   `https://mcp.externalsoul.com/<handle>` — connect that URL as a remote MCP server. Public
   or OAuth-gated depending on how the vendor exposed it; app tools work through the generic
   `get_app_tools` / `call_app_tool` pair.

The skills prefer the generic pair on purpose — it is served by the platform and always
matches the deployed apps, so they work even when a client's MCP tool snapshot is stale.

## Costs, honestly

The cloud browser bills the workspace owner per browser-minute while a session is awake
(parked = free; idle sessions park themselves after ~5 minutes; tabs and logins survive
parking). Sandbox compute and AI actions bill the owner's credit balance. The skills are
written to be spend-aware: status checks are free, and every irreversible act on a website
goes through an approval gate.

## Repo layout

Skills are authored in the main ExternalSoul codebase (`scripts/skills/desktop/`) and shipped
here as zips — this repo is the distribution point.
