# Setting up the website dashboard in Claude Code

This page is written for Claude. Someone asked you to set them up with the website dashboard: follow these
steps with them, one at a time, in plain words. They don't need to know what a plugin or MCP is. Ask before
you change anything on their computer, and never ask for their password: they sign in on the dashboard's own
page.

## 1. Add the plugin to their Claude Code settings

Their user settings are `~/.claude/settings.json` (on Windows `%USERPROFILE%\.claude\settings.json`). Read the
file first (it may not exist yet: then create it with just this). Add these two entries, **keeping everything
already in it** (merge into existing `extraKnownMarketplaces` and `enabledPlugins` objects, never replace
them):

```json
{
    "extraKnownMarketplaces": {
        "dashboard-ai": {
            "source": { "source": "github", "repo": "Untriel/dashboard-ai" }
        }
    },
    "enabledPlugins": {
        "dashboard@dashboard-ai": true,
        "dashboard-staging@dashboard-ai": true
    }
}
```

Show them what you'll add and ask before writing it. Check the file is still valid JSON afterwards.

Two connections: `dashboard` is production (the real websites, the one to use unless they say otherwise) and
`dashboard-staging` is staging, a test copy where new work is tried first. If they say they only need the real
websites, leave out `dashboard-staging@dashboard-ai`.

## 2. Restart

Tell them to quit Claude completely and open it again (closing the window isn't always enough: quit it from
the menu or the tray). Claude Code installs the plugin while it starts. Ask them to come back to this
conversation, or start a new one and say "continue the dashboard setup".

## 3. Sign in

After the restart, each connection needs a one-time sign-in, which only they can do: `dashboard`, then
`dashboard-staging`. In the Claude desktop app they show up among the connections as needing sign-in; in a
terminal, `/mcp` and then `plugin:dashboard:dashboard` (and `plugin:dashboard-staging:dashboard-staging`).
Their browser opens that dashboard's sign-in: they sign in with their own account there and allow it, with
**Make changes** (it saves drafts, and publishes only when they ask). Production and staging are separate
dashboards with separate accounts: if they have no staging account yet, the platform's owner makes one.

## 4. Check it works

Call `list_organizations` on each connection: `dashboard` answers `"environment": "production"`,
`dashboard-staging` answers `"environment": "staging"`, each with the organizations they belong to there.
Tell them the difference in one line: staging is for trying things, production is the real websites. Tell them which websites you can now work on, and what they can ask:

- "Make a new page about … on [website]" (a draft with a preview link; nothing goes live until they say so);
- "Add these accommodations / offers / team members …";
- "How many visitors did [website] have this month?".

If the list is empty, they aren't a member of any organization yet: the platform's owner adds them. A platform
admin can ask you to join a client's organization as support (`list_all_organizations`, `join_as_support`).

If a step fails, say what you saw; don't work around it. Their skills (`designing-pages`,
`filling-collections`, `collections-from-pages`) come with the plugin: nothing else to install.
