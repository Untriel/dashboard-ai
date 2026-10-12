---
name: designing-pages
description: Design and build a page or a section for a client's website through the dashboard connection, from the site's own blocks, checked by the editor's rules, saved as a draft with a preview link and published only when the user asks. Use when the user asks to create, design, draft, rework or lay out a page, landing page or section on one of their websites.
---

# Designing a page

You build pages from the website's own blocks, through the dashboard connection (`dashboard`, or
`dashboard-staging`). Every save is checked by the editor's rules, new pages are drafts, and the user keeps the
final say: they look at the preview, and nothing goes live until they ask.

## 0. Where, and what

- **Which environment.** `dashboard` is production: the real websites. `dashboard-staging` is a test copy.
  Use production unless the user says staging or test. Every change's answer names its `environment`: check
  it's the one you meant. An id from one environment isn't the same item in the other: look it up again.
- **Which website:** `list_organizations` (use its slug as `organization`). A platform admin works in a
  client's organization after joining it as support: when they ask, `list_all_organizations` and
  `join_as_support` (and `leave_support` when the work is done, if they want).
- **Ask** what the page is for, who reads it, what it must say and link to, and which images to use, unless
  the brief says so. **Never invent facts** about the client (prices, opening times, awards, distances): ask,
  or leave a clearly marked placeholder ("[price per night]") and say so.

## 1. Learn the blocks

- `get_block_catalogue` without `blocks`: every block in brief, where it may go and its slots, and a page's
  own settings (its spacings).
- Then `get_block_catalogue` with the blocks you'll use (at most 30 at a time): their options with the
  choices each offers, their defaults, and the Style tab's options every block has (margins, padding,
  backgrounds, borders, widths).

## 2. Look at the site first

- `list_pages`, then `get_page` for the home page and a page like the one asked for. Match what you see:
  section spacing, card looks, heading sizes and levels, button styles, how images are used. A new page
  should look like it belongs.
- **Images:** `search_uploads` (`mimeType: "image/"`, a search word) lists the file library with alt texts.
  You can't make photos: use the library's, add one the user gives you an https link to (`upload_from_url`,
  with an alt text; only files they may use), or ask them to upload some. An image option takes `u/<key>`.

## 3. Write the block tree

```json
[
    {
        "type": "Hero",
        "data": { "image": "u/<key>" },
        "slots": { "content": [{ "type": "Heading", "data": { "text": "…", "level": "h1" } }] }
    },
    {
        "type": "Columns",
        "data": { "count": 2 },
        "style": { "paddingY": "lg" },
        "slots": { "col1": [{ "type": "Heading", "data": { "text": "…" } }], "col2": [] }
    }
]
```

- **Structure:** the top level holds only layout blocks (Hero, Columns…); content goes in their slots. Each
  block is `{ type, data, slots?, style?, visibility? }`. Leave out ids and any option at its default: they're
  filled in.
- **Values:** a choice or select takes one of its listed values, a slider a one-number list (`[40]`), a toggle
  `true`/`false`, rich text simple HTML (`<p>`, `<h2>`–`<h4>`, `<strong>`, `<em>`, `<a href>`, lists).
- **List items** (a button, a slide, a line) are saved with their missing keys empty, not with the catalogue's
  `newItem`: write each item whole, starting from its `newItem`.
- **Links:** a page of the site as `page:<id>` (`list_pages`, `find_page_by_path`), so the link follows the page
  when it moves; elsewhere a full https address. `<key>NewTab: true` opens a new tab.
- **Forms and collections** by id: `list_forms`, `list_collections` (with their fields' keys). A grid's
  `query` is JSON text: `{ "filters": [{ "field", "op", "value" }], "sortField", "sortDir" }`.
- **One look per kind of thing** across the page; headings in order (one H1, then H2, H3); the Style tab for
  spacing and backgrounds, not empty Spacers.
- Never the platform's name in client content: the client's visitors never see it.

## 4. Check it

`check_blocks` with the tree (and the page's spacings). Fix every error; read the warnings. The saves run the
same check and refuse a tree with errors.

## 5. Save as a draft and show it

- `create_page` (title, slug, `parentPageId` to put it under another page): it's saved as a **draft** and the
  answer has a **preview link** (15 minutes) and the dashboard editor's link. Give the user both.
- Change it with `update_page` (send the `updatedAt` you last saw; `blocks` replaces the whole tree). A
  refusal that someone changed it meanwhile means: read it again (`get_page`) and redo your change on that.
- `preview_page` gives a fresh link when the old one has expired.

## 6. Publishing, and changing a live page

- **Publish only when the user asks** (`publish_page`); `published: false` takes a page back to a draft.
- **A published page changes on the live website when it's saved.** Show the change first with
  `preview_page` (the change, unsaved), and save it with `update_page` and `live: true` only after the user
  says so. A new address for a published page adds a redirect from the old one (`addRedirect`, on by default).

## Afterwards

When the user asks: a redirect from an old address (`create_redirect`), publishing at a set time
(`schedule_page`), or going back to an earlier version (`list_revisions`, `get_revision`, `restore_revision`).

## Detail templates

A collection's detail page (every entry gets it, like `/accommodation/<lodge>`) takes the entry's values
through bindings (`"dataBindings": { "<option path>": "<field key>" }`) and `{{ field }}` tokens in text.
`check_blocks` with the collection's id checks them against its fields; `update_page` on the template checks
the same. Creating new collection types isn't possible through the connection yet: ask the platform's owner.

## Report

Say what you made, the preview and editor links, what's a placeholder, and what the user should decide.
What you save shows as the user's own work in the dashboard's history: never mark content as written by AI.
