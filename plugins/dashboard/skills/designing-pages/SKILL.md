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
  choices each offers and their defaults. Add `style: true` once for the Style tab's options every block has
  (margins and padding, also on phones, backgrounds, borders, widths); later calls can leave it out.

## 2. Look at the site first

- `list_pages`, then `get_page` for the home page and a page like the one asked for. Take the site's
  language from them: its colours, fonts, button styles, card looks, heading sizes and section spacing. A new
  page should look like it belongs, but not like a copy of the page next to it.
- **Images:** `search_uploads` (`mimeType: "image/"`, a search word) lists the file library with alt texts.
  You can't make photos: use the library's, add one the user gives you an https link to (`upload_from_url`,
  with an alt text; only files they may use), or ask them to upload some. An image option takes `u/<key>`.
  The library has folders: `search_uploads` with `folders: true` lists them, `folder` looks in one, `shape`
  (landscape, portrait, square) finds an image that fits a spot. When the page has a folder already (`folders:
  true`), put files you add there (`upload_from_url`'s `folderId`); make one (`organize_uploads`) only when the
  user wants that.
- **Videos:** a video option (the Video block, a Hero's background, a gallery item's video) takes an MP4 or
  WebM's key (`search_uploads` with `mimeType: "video/"` lists only those). Another video (an iPhone's `.mov`) is
  refused: ask the user to convert it with Convert to MP4 in the dashboard's file library (it recommends a
  preset; Background Loop for a Hero's background), then use the new file.

## 2b. Give it a design of its own

Belonging to the site isn't the same as being plain. A page made only of heading, text and three cards in a row
reads like a template; give it something to remember, within the site's language:

- **A rhythm of sections:** vary them down the page. A full-width image or a band in the brand's colour, then a
  calm text section, then a split layout (image beside text, alternating sides), then a grid. Not every section
  the same width or background. A Columns section's background (its Style tab) fills the whole screen as a band
  while its content keeps to its Width; its card look is a panel instead. Columns don't go inside Columns: a
  section is one row of columns, and the next section starts a new one.
- **A few strong moments:** a large photo with little text, a striking quote or testimonial, a number that
  matters (years in business, distance to the beach) set big, a gallery or slider where there's more than one
  good photo.
- **Movement, sparingly:** blocks don't animate in on scroll. What moves is a block's own option: a Stat
  counts up, a Slider plays by itself, a grid's cards come in when visitors filter it. Use them where they help.
- **Contrast you can read:** text and buttons stand out from their background (the browser check in 5b shows it).
- **Light and dark mode:** a site may have both (the catalogue's `colors.darkMode`: the visitor switches). Prefer
  the theme's colors (`colors.theme`, written as `var(--muted)`, `var(--primary)`…): they follow the mode. A fixed
  background color (`#f5f1e8`) stays that color in both modes, and the text and colors inside it follow it by
  themselves when it's clearly light or dark; set the Style tab's `surface` (`light`, `dark`) for a see-through
  color or a background image. A fixed text color without such a background is unreadable in one of the modes
  (the checker warns).
- Bolder is a choice the user can make: when they ask for something calmer or more striking, follow them.

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
  `true`/`false`, rich text simple HTML (`<p>`, `<h2>`–`<h4>`, `<strong>`, `<em>`, `<a href>`, lists, and an
  image from the file library on a line of its own: `<img src="u/<key>" alt="…" data-size="sm">`, `data-size`
  `sm`, `md`, `lg` or `full`, `data-align` `center` or `end`; a logo in a text, a photo in a blog post. No image
  from anywhere else: it's dropped).
- **List items** (a button, a slide, a line) are saved with their missing keys empty, not with the catalogue's
  `newItem`: write each item whole, starting from its `newItem`.
- **Links:** a page of the site as `page:<id>` (`list_pages`, `find_page_by_path`), so the link follows the page
  when it moves, with a query or anchor after it when needed (`page:<id>?type=lodge#rates`: a page opened on a
  filter). One collection entry's own page as `entry:<id>` (`list_entries`), which follows the entry too: in a
  link field only, not in rich text (there use the entry's address).
  Elsewhere a full https address. `<key>NewTab: true` opens a new tab.
- **Words over a photo** (an Image Tile, an Image Card): pick photos with a calm area where the title goes, and
  use the block's `overlay` where the photo is bright behind it.
- **Forms and collections** by id: `list_forms`, `list_collections` (with their fields' keys). A grid's
  `query` is JSON text: `{ "filters": [{ "field", "op", "value" }], "sortField", "sortDir" }`. The operator
  depends on the field's type: text `eq`, `neq`, `contains`; numbers and dates `eq`, `neq`, `gt`, `gte`, `lt`,
  `lte`; a select `eq`, `neq`; yes/no `eq`; features `has` (the entry has that feature's value; `eq` reads the same).
- **A newsletter signup:** the Newsletter block (pages, popups, the footer), not a form. Its texts and button are
  the block's; where the signups go (the website's own list or Mailchimp) and double opt-in are set on the
  dashboard's Newsletter page, which you can't read or change (the list is personal data, the Mailchimp key a
  secret). It takes signups only once that page is set up (Mailchimp connected, or the website's own email
  sender verified): when you place one, tell the user to check the Newsletter page.
- **A long page of sections** (activities, facilities, a list of rooms): a `SectionMenu` among the page's sections
  lets visitors jump between them (sticky under the header on wide screens, a bar at the bottom on phones). With
  `source: "collection"` it lists the entries of the page's Collection Grid of that collection: turn on the grid's
  `entryAnchors`, so each entry is a jump target. With `source: "links"` it takes `#anchor` links to blocks with
  that anchor (their Style tab). In a grid's item design, Media & Text's `alternate` swaps the image's side on every
  other entry, its `media: "gallery"` shows several photos one at a time (`images`, bound to an entry's images
  field), and a button's link can be one of the entry's fields (`{{ bookingUrl }}`): an entry without one has
  no button on the site.
- **One look per kind of thing** across the page; headings in order (one H1, then H2, H3); the Style tab for
  spacing and backgrounds, not empty Spacers.
- **A heading section with its content below** (a title, then a row of cards in the next section): give the
  content's section the Style tab's `spaceAbove` (e.g. `sm`), which replaces the page's space between sections
  there, so the two stay paired whatever the page's spacing; not a negative top margin. Margins and padding
  have phone values too (`marginTopMobile`, `paddingYMobile`…), for a section that needs less room on phones.
- Never the platform's name in client content: the client's visitors never see it.

## The same thing on several pages: a shared preset

When the user wants the same content on more than one page (a booking strip, opening hours, a call to action,
a contact block), don't paste copies: make it **one shared preset** and place its link on each page, so a later
change updates them all.

- `create_preset` with `kind: "block"`, `shared: true` and either the block (`blocks`, one block with its
  content) or a block that's already on a page (`fromPageId` and `blockId`, from `get_page`). Its answer has the
  link to place: `{ "type": "PresetLink", "data": { "presetId": "<id>" } }`.
- Put the link where the block would go (a layout preset at the page's top level, a content preset in a slot),
  with nothing else on it, and save each page as usual (`update_page`; a published page with `live: true` only
  when asked). Replace the copies the pages already have.
- `list_presets` shows the existing ones (`shared`): reuse one before making another.
- Changing a shared preset (`update_preset`) changes every page that links it. Where one of them is live (a
  published page, the header or footer, a popup), say which (its refusal lists them) and save with `live: true`
  only when the user asks; linked only from drafts, it saves as it is. A linked preset can't be
  deleted until its links are replaced.
- A preset that isn't shared is a starting point: `create_preset` saves a section for the user to reuse in the
  editor (inserted as a copy).

## 4. Check it

`check_blocks` with the tree (and the page's spacings). Fix every error; read the warnings. The saves run the
same check and refuse a tree with errors.

## 5. Save as a draft and show it

- `create_page` (title, slug, `parentPageId` to put it under another page): it's saved as a **draft** and the
  answer has a **preview link** (15 minutes) and the dashboard editor's link. Give the user both.
- Change it with `update_page` (send the `updatedAt` you last saw; `blocks` replaces the whole tree). A
  refusal that someone changed it meanwhile means: read it again (`get_page`) and redo your change on that.
- **Adding, moving or removing a few blocks** (a section, a block in a slot): `edit_blocks` with the item and its
  `updatedAt`, rather than the whole tree back; a new block's ids come back (leave its `id` out). A place is a
  parent's id with its slot (both, or neither for the top level) and an index; a move's index counts after the
  block was taken out. Many changes at once: `update_page`.
- **A small change to one block** (a text, a color, an option, its Style tab, hiding it on phones): `update_block`
  with its id from the tree you read, only the keys that change (`data`, `style`: null removes one; `visibility`),
  and the `updatedAt`. The rest of the tree stays exactly as stored.
- `preview_page` gives a fresh link when the old one has expired.

## 5b. Look at it

Check what you made the way a visitor sees it, before you call it done. The preview link needs no sign-in (it
carries its own token):

- **With a browser** (the Claude desktop app's built-in browser, Claude in Chrome): open the preview link at
  desktop width (about 1280 px) and phone width (about 390 px), scroll the whole page, and look for what the
  checker can't see: sections that don't match the rest of the site, spacing that's off, text running over or
  too long for a phone, images cropped badly (a focal point, `update_upload`) or missing, a contrast problem.
  On a site with a dark mode the answers also have a `previewDark` link: open it too (desktop width is enough),
  for text or colors that don't read in dark. Fix it with `update_page`, take a fresh preview, and look again. A screenshot of each width is a good thing to
  show the user.
- **Without one** (claude.ai in a web browser): say you can't see the page, give the user the link to look at
  on their computer and phone, and ask what to change. For designing pages, recommend the Claude desktop app:
  it has a browser, so it can check its own work.

## 6. Publishing, and changing a live page

- **Publish only when the user asks** (`publish_item`, kind `page`); `published: false` takes it back to a draft.
- **A published page changes on the live website when it's saved.** Show the change first with
  `preview_page` (the change, unsaved), and save it with `update_page` and `live: true` only after the user
  says so. For one block: `update_block` with `preview: true` first, then `live: true`. A new address for a published page adds a redirect from the old one (`addRedirect`, on by default).

## Afterwards

When the user asks: a redirect from an old address (`create_redirect`, or many with `import_redirects`),
publishing at a set time (`schedule_item`), going back to an earlier version (`list_revisions`, `get_revision`,
`restore_revision`), a copy to start from (`duplicate_item`), leaving a page out of the sitemap or saving it for
offline use (`update_page`), better alt texts, a focal point or a new name for a file (`update_upload`; a new name
moves every use along), or deleting a page
(`delete_item`: Recently deleted keeps it 30 days, and `restore_revision` brings it back).

## The header, the footer and popups

- **Header and footer** (`get_header_footer`, `update_header_footer`; owners and admins): one block at the root,
  the client's frame (`get_block_catalogue`: `frame: "header"` or `"footer"`), its areas filled with blocks. Start
  from the current tree, change what was asked, keep the rest. It's on every page at once: save only when the
  user asks, with the `updatedAt` you read (`get_header_footer` with `region` reads one of them). One block's
  options there (kind `header` or `footer`): `update_block`, not the whole tree back. The same for a popup's or a
  preset's block.
- **Mega menus** (a Menu item that opens a mega menu: its blocks in the Menu's slot `mega-<id>`): on phones they
  open in the menu drawer, where Columns keep their phone layout, so design each to read well as a single column
  or two as well. The Menu's `backdrop` dims (or dims and blurs) the page behind an open one.
- **Popups and banners** (`list_popups`, `create_popup`, `update_popup`, `preview_popup`, `publish_item`, `schedule_item`): the
  kind's frame at the root (modal, bar, slide-in, fullscreen) with the content in its slot, and settings for when,
  where and for whom. New ones are drafts; give the user the preview link (it opens the popup at once), and
  publish or schedule only when they ask. A published one changes live only with `live: true`.

## Detail templates

A collection's detail page (every entry gets it, like `/accommodation/<lodge>`) takes the entry's values
through bindings (`"dataBindings": { "<option path>": "<field key>" }`) and `{{ field }}` tokens in text.
`check_blocks` with the collection's id checks them against its fields; `update_page` on the template checks
the same. New collection types: the collections-from-pages skill.

## Report

Say what you made, the preview and editor links, what you checked in the browser (or that you couldn't),
what's a placeholder, and what the user should decide.
What you save shows as the user's own work in the dashboard's history: never mark content as written by AI.
