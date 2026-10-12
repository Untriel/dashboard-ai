---
name: filling-collections
description: Add or update a collection's entries (accommodations, team members, offers, FAQs…) on a client's website through the dashboard connection, as drafts with preview links, published only when the user asks. Use when the user asks to add, fill in, import, update, correct or publish items of a collection.
---

# Filling a collection

Collections hold a website's repeated items (each accommodation, each offer). Their entries have the
collection's fields, and some also a body of blocks. You add and change them through the dashboard connection
(`dashboard`, or `dashboard-staging`); new entries are drafts, and nothing goes live until the user asks.

## 0. Where, and what

- **Which environment.** `dashboard` is production: the real websites. `dashboard-staging` is a test copy.
  Use production unless the user says staging or test. Every change's answer names its `environment`: check
  it's the one you meant. Ids differ between them: look things up in the one you work in.
- **Which website:** `list_organizations` (its slug is `organization`). A platform admin joins a client's
  organization as support first, when they ask (`list_all_organizations`, `join_as_support`).
- **Never invent facts** (prices, sizes, features, opening times): use what the user gives you, or leave a
  marked placeholder and say so.

## 1. Learn the collection

- `list_collections`: each collection's fields (`key`, `label`, `type`, `required`, a select's or features
  field's `options`), its display field (the entry's name and address) and whether entries have a body.
- `list_entries`, then `get_entry` for one or two existing entries: how long texts are, how images and
  features are used. New entries should read like the others.

## 2. Field values

By the field's key:

| Type | Value |
| --- | --- |
| `text`, `textarea` | text |
| `richtext` | simple HTML: `<p>`, `<strong>`, `<em>`, `<a href>`, lists |
| `number` | a number |
| `boolean` | `true` / `false` |
| `date` | `YYYY-MM-DD` |
| `image` | `u/<key>` from `search_uploads` (or `upload_from_url` for a file the user links) |
| `images` | `[{ "image": "u/<key>", "alt": "…", "caption": "…" }]` |
| `file` | a file's key from `search_uploads` |
| `select` | one of its options' values |
| `features` | a list of its options' values |
| `color` | `#rrggbb` |

Required fields only need values to publish: a draft may be incomplete. An entry's body (when the collection
has one) is a block tree, written as the designing-pages skill says and checked the same way.

## 3. Add and change entries

- `create_entry`: saved as a **draft**, with a preview link and the editor's link. Its address comes from the
  display field. With a browser, open the preview (desktop and phone width) and check the entry looks right in
  its page; without one, give the user the link.
- `update_entry`: `data` changes only the fields you send (null empties one); send the `updatedAt` you last
  saw. Someone changed it meanwhile: read it again and redo your change on that.
- **Several at once:** agree the list with the user first, then go one entry at a time, at most 20 before you
  report back. Stop at the first refusal you can't explain, and say which ones were done.
- `duplicate_entry` copies one as a draft (a quick start for a similar one). In a hand-ordered collection
  (`manualOrder`), `move_entry` puts one after another (or first); grids showing that order change live, so only
  when asked. `delete_entry` only when asked: Recently deleted keeps it 30 days (`restore_revision`).

## 4. Publishing, and changing live entries

- **Publish only when the user asks** (`publish_entry`; all required fields filled). `published: false` takes
  one back to a draft.
- **A published entry changes on the live website when it's saved.** Show the change first with
  `preview_entry`, and save it with `update_entry` and `live: true` only after the user says so.

## Report

Which entries were added or changed (with their editor links), what's still a draft, which placeholders
are left. What you save shows as the user's own work: never mark content as written by AI.
