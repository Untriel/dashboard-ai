# dashboard-ai

The client-website dashboard for Claude Code: plugins with the connection and the workflows.

## Getting started

In the Claude desktop app (Code) or Claude Code in a terminal, say:

> Set me up with the website dashboard: follow https://github.com/Untriel/dashboard-ai/blob/main/SETUP.md

Claude adds the plugins (asking you first), asks you to restart, and walks you through signing in on the
dashboards (production and staging, an account on each). You need a dashboard account; the platform's owner adds you to the websites you work on.

## What's in it

- **`dashboard`:** the connection to the dashboard (production, the real websites), signed in as you, with
  your access and nothing more, and three skills: `designing-pages` (a page or section from the site's own
  blocks), `filling-collections` (adding and updating a collection's entries) and `collections-from-pages`
  (similar pages turned into a collection with one detail template).
- **`dashboard-staging`:** the connection to staging, a test copy where new work is tried first (set up
  alongside by default).

The house rules come from the server itself: drafts by default, the live website only when you ask, no
personal data, your work shows as your own.

## By hand

```bash
claude plugin marketplace add Untriel/dashboard-ai
```

```bash
claude plugin install dashboard@dashboard-ai
```

Restart Claude Code, then sign in when the `dashboard` connection asks (`/mcp` in a terminal).

**Updates:** turn on auto-update for the marketplace (`/plugin` → Marketplaces → dashboard-ai → Enable
auto-update, or `"autoUpdate": true` on its entry in `extraKnownMarketplaces`, as SETUP.md does): a while after a
session starts Claude Code fetches new versions, and the next start uses them. By hand:
`claude plugin marketplace update dashboard-ai`, then `claude plugin update dashboard@dashboard-ai`.

## claude.ai (in the browser)

Settings → Connectors → Add custom connector: `https://api.bookingmanager.app/mcp`. The skills can be added as
organization skills by an admin (zip each folder in `plugins/dashboard/skills/`). Claude in a web browser can't
open the previews itself, so for designing pages the Claude desktop app is better: its built-in browser lets
Claude look at its own work.

## Changing it

Skills are plain Markdown (`plugins/dashboard/skills/<name>/SKILL.md`). Bump `version` in the plugin's
`.claude-plugin/plugin.json` and in `.claude-plugin/marketplace.json` with every change, so installs pick it
up. Keep to what the server's tools do: its instructions and tool descriptions are the reference.
The server also offers these skills as its own prompts (for claude.ai and other clients without the plugin):
after changing one, run `pnpm mcp:prompts` in the platform's repository and ship that with it.
