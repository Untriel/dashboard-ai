# dashboard-ai

Private. The client-website dashboard for Claude: one plugin, `dashboard`, with

- **the connections** (`.mcp.json`): `dashboard` is production (the real websites), `dashboard-staging` the
  test copy. Each signs in on its own dashboard as you, with your access and nothing more.
- **the skills:** `designing-pages` (a page or section from the site's own blocks) and `filling-collections`
  (adding and updating a collection's entries).

The house rules come from the server itself (its instructions): drafts by default, the live website only when
you ask, no personal data, your work shows as your own. The design is `docs/decisions/ai-access.md` in the
platform's repository.

## Claude Code (the desktop app or the terminal)

You need access to this repository (the owner invites you on GitHub) and GitHub signed in on your computer
(`gh auth login`). Then, once:

```bash
claude plugin marketplace add Untriel/dashboard-ai
```

```bash
claude plugin install dashboard@dashboard-ai
```

Restart Claude Code. The first time Claude uses a connection, your browser opens the dashboard's sign-in: sign
in and allow it ("Make changes" lets it save drafts, and publish when you ask).

Updates: `claude plugin marketplace update dashboard-ai`, then restart.

## claude.ai

- **The connection:** Settings → Connectors → Add custom connector, with
  `https://api.bookingmanager.app/mcp` (production). Staging, for testing only:
  `https://bookingmanager-api-staging.royal-frog-60bb.workers.dev/mcp`.
- **The skills:** an organization admin adds each folder of `plugins/dashboard/skills/` as an organization
  skill (zip the folder, so the zip holds `<name>/SKILL.md`).

## Changing it

Skills are plain Markdown (`plugins/dashboard/skills/<name>/SKILL.md`). Bump `version` in
`plugins/dashboard/.claude-plugin/plugin.json` and `.claude-plugin/marketplace.json` with every change, so
installs pick it up. Keep to what the server's tools do; the server's instructions and its tool descriptions
are the reference for what's allowed.
