---
name: moving-content
description: Move pages, collections and entries, presets, popups, the header and footer, forms, redirects or files from one environment to another (staging to production, or back) or between organizations, through the dashboard connections, with a plan shown before anything changes. Use when the user asks to copy, move, bring over, promote or sync content from staging to production (or the other way), or from one website to another.
---

# Moving content between environments

Staging is where content is tried; production is where it goes live. Moving it is one tool on each side: you
carry only a short code, never the blocks or the files.

## 0. Which way, which items

- **Source and target.** `dashboard-staging` is staging, `dashboard` is production. Ask the user which way if
  it's not clear. Between two websites in the same environment, the source and the target are the same
  connection with different `organization`s.
- **Find the items on the source:** `list_pages` (`find_page_by_path` for an address), `list_collections`,
  `list_entries`, `list_presets`, `list_popups`, `list_forms`. The header and footer, all redirects and single
  files (by their key, from `search_uploads`) can go too.
- **What never moves:** customers, form submissions, form recipients, email settings, domains, privacy, app and
  analytics settings. Say so if the user asks for those: they're set on each side.

## 1. Make the transfer on the source

`create_transfer` with the items:

- a page (`kind: "page"`, `id`), with the pages under it (`withChildren: true`);
- a collection (`kind: "collection"`, `id`), with its entries (`withEntries: true`), or entries one by one
  (`kind: "entry"`, `id`, `collectionId`);
- `preset`, `popup`, `form` by `id`; `header`, `footer`, `redirects` without one; a file (`kind: "upload"`,
  `key`).

It answers a `code` (valid 24 hours) and `from` (the source's environment). It changes nothing.

## 2. Show the plan from the target

`import_transfer` on the target's connection with `organization`, `code` and `from`, without `apply`. Show the
user its plan in plain words:

- **Items:** each one is `create` (new here, arrives as a draft), `update` (it replaces the matching item here;
  `replacesTheseBlocks` lists what's on it now), or `blocked` (its `problems`: usually a block this website's
  build doesn't have yet, so a deploy has to come first). Items marked `live` change the live website when
  applied.
- **References** the items point at (a linked page, a grid's collection, a form, a shared preset): matched here
  by address, slug or name, or by an earlier transfer. Without a `match`, their uses are emptied when applied
  (a link to nowhere, a grid with no collection), and `usedBy` names the blocks. Ask the user what they want:
  pick an item here (`map: { "<reference id>": "<id here>" }`), leave those blocks out
  (`leaveOutBlocks: [<block ids>]`), or accept the emptied uses.
- **Files:** how many are copied, and how many this website has already (found by their content).

Matched items keep their title and address here, unless the user wants otherwise (change those afterwards with
the update tools).

## 3. Apply when the user agrees

The same `import_transfer` call with `apply: true`, plus `map`, `leaveOutBlocks` or `leaveOut` (item refs from the
plan) from the user's answers.

- **Files first.** Large transfers copy their files over several calls: while the answer says `progress`, call
  again with the same arguments. Nothing else changes until the files are there.
- **Live.** New items arrive as drafts. A published item, a popup that's live, a preset shown live, the header,
  footer and redirects change the live website: without `live: true` the published pages, entries and popups
  come back as preview links (show them), and the rest is refused. Use `live: true` only when the user asks.
- **Look at it.** Open the preview links and the new drafts' pages at desktop and phone width before calling it
  done, and their `previewDark` links too where the answers have them (the site has a dark mode: a fixed color
  that reads well in light mode may not in dark). The apply answer's `lookAt` says the same. Publishing new drafts is its own step (`publish_item`), when the user asks.

Moving the same items again later (after more work on staging) updates what the earlier transfer made, even if
it was renamed here.
