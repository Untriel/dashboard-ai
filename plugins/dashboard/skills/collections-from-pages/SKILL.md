---
name: collections-from-pages
description: Turn a set of similar pages on a client's website (one page per accommodation, offer, team member…) into a collection: its fields, a detail template every entry shares, the entries filled from the pages, and the old addresses redirected, as drafts until the user asks to switch over. Use when the user asks to turn pages into a collection, make a collection like an existing page, or give repeated pages one shared design.
---

# Turning pages into a collection

Websites often grow a page per item (each lodge, each offer) built by hand. A collection keeps the items as
entries with fields, and one **detail template** shows each of them: one design to change, consistent pages,
grids and filters for free. You do it through the dashboard connection, as drafts, and the user decides when
the website switches over.

Creating a collection type is for owners and admins of the organization (and platform admins).

## 0. Where, and what

- **Which environment.** `dashboard` is production, `dashboard-staging` the test copy. For a change this big,
  suggest trying it on staging first. Every change's answer names its `environment`: check it.
- **Which pages.** `list_pages`, `get_page` on each of them (or a few, when there are many): what they share
  (the facts each one has, the sections every page repeats) and what differs. Don't invent facts the pages
  don't have.

## 1. The fields: agree them first

Propose the fields from what the pages hold, and **ask the user to confirm them** before creating anything:
their `key`s stay once entries use them.

- One field per fact that differs per item: a name (the display field, which also makes each entry's address),
  numbers (guests, bedrooms, price), short texts, a gallery (`images`), features (`features` with options and
  icons), a select (a park, a category).
- Field types: `text`, `textarea`, `richtext`, `number`, `boolean`, `date`, `image`, `file`, `select`,
  `features`, `color`, `images`.
- A long free part that differs per item can stay a body of blocks (`usePagebuilder: true`, shown by an Entry
  Body block in the template).

## 2. Create the collection

`create_collection` with the agreed fields and `displayFieldKey`. Check it with `list_collections`.

## 3. The detail template

`create_page` with `collectionId` and the parent page (`parentPageId`: the page the items sit under, e.g. the
accommodation overview): every entry's page is the parent's address plus the entry's slug. Build it like any
page (the designing-pages skill), from the existing pages' design, with the entry's values through bindings
(`"dataBindings": { "<option path>": "<field key>" }`, e.g. a Heading's `text` → `name`, a Gallery's
`images` → `gallery`) and `{{ field }}` tokens in text. `check_blocks` with `collectionId` checks them.

## 4. The entries

One `create_entry` per page, with its values taken from that page (drafts). Agree the list first; at most 20
before reporting back; stop at a refusal you can't explain. Images: the `u/<key>` values the pages use.

Show the user a few with `preview_entry`: each renders inside the template. With a browser, open those links
yourself first (desktop and phone width, and their `previewDark` links when the answers have them) and compare them with the pages they came from; without one, give the
user the links and ask them to compare.

## 5. Switching over (only when the user asks)

In this order, telling the user each step:

1. Publish the template and the entries (`publish_item`, kind `page` and `entry`).
2. Redirect each old page's address to its entry's new one (`create_redirect`), unless they're the same.
3. Take the old pages back to drafts (`publish_item` with `published: false`); delete them later
   (`delete_item`, kept 30 days in Recently deleted) only when the user asks. Pages win over redirects, so the redirects only work once the old
   pages are drafts.
4. A grid on the parent page that lists the entries (a Collection Grid) replaces any hand-made list.

## Report

What was made (collection, template, entries with their editor links), what's still a draft, the redirects,
and what the user should check. What you save shows as the user's own work.
