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
| `browser-use-skill.zip` | **How to drive a page reliably and know whether it worked** — the companion to `cloud-browser`: that one says which tools to call, this one says whether to believe them. Reading a page fails loudly; **writing to one fails silently**, so a success result proves keystrokes were delivered, never that the page changed (~1 write in 50 vanishes with a clean `ok`). Verify by artifact not by result; missed clicks type into the wrong element instead of erroring; selectors inside embedded editors shift between renders; typed values parse in the **page's** locale; and what changes when the document is somebody's record. Includes a reference for web spreadsheets (Excel for the web, Google Sheets). |
| `porkbun-skill.zip` | Work a **Porkbun** account through the logged-in cloud browser: list domains + expiry dates, and add/change DNS records (TXT/site-verification/A/CNAME/MX) — knows Porkbun's staged Add-Record → Submit-Records flow, its 2FA step-up walls, the TXT-textarea trap, and verifies records from a live DNS resolver. |
| `openai-invoices-skill.zip` | Pull **OpenAI invoices** (platform.openai.com) into the workspace through the logged-in cloud browser — all, a time window, or only the ones the user doesn't already have: dedups by invoice NUMBER against their PDFs/spreadsheet/notes, knows the Stripe invoicedata resolution (paced — it rate-limits), the 600s signed-URL expiry, and batch download; generalizes to any invoice.stripe.com-hosted vendor billing. |
| `forge-app-builder-skill.zip` | Build a **new ExternalSoul app** in the user's Forge: open a live workbench, write the source (events, tools, UI), watch it hot-reload in their frame, look at desktop/phone/dark screenshots, call its tools before install, run the checks, ship the pull request, merge and install — following the platform's design rules. |
| `forge-realtime-apps-skill.zip` | Build a **complete ExternalSoul app** in the Forge over MCP/SDK and get it installed: getting started from zero, every capability an app can declare (events and tools, background tasks on Inngest that update the UI live, server ops and streaming routes, webhooks, poll cadences, OAuth/API-key connections, files and file providers, cross-app grants, sharing), testing each one in the workbench, and both ways an app reaches a platform (its own GitHub repo → Settings → Apps by link, or a pull request). The companion to `forge-app-builder` for anything beyond a plain UI app. |
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
