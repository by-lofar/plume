# spec: /veille

> Scope: content model, page behaviour, and rendering rules for the watch feed.

---

## 1. Purpose

`/veille` is a chronological feed of curated links. Each entry points to an external resource with an optional comment from the author. Tags are decorative — they provide context at a glance but do not drive navigation or filtering.

---

## 2. Content model

Entries are MDX files in an Astro content collection. The collection schema is defined and validated at build time.

### Frontmatter

| Field          | Type       | Required | Description                        |
|----------------|------------|----------|------------------------------------|
| `title`        | `string`   | yes      | Title of the linked resource       |
| `url`          | `string`   | yes      | URL of the linked resource         |
| `date`         | `date`     | yes      | Publication date of the entry      |
| `tags`         | `string[]` | no       | Empty array if no tags             |
| `source.name`  | `string`   | yes      | Display name of the source (e.g. `Journal du Hacker`) |
| `source.url`   | `string`   | yes      | URL of the source                  |

### Body

The MDX body is optional. When present, it is the author's comment on the linked resource. No length constraint, but the spirit of the format is brief — a few sentences at most.

---

## 3. Page behaviour

### Sorting

Entries are sorted by `date` descending. The most recent entry appears first.

### Pagination

Entries are paginated statically. Each page displays **20 entries**.

- Page 1: `/veille`
- Page N: `/veille/${n}` (N ≥ 2)

The total number of pages is determined at build time. Empty pages are not generated. If the total number of entries is ≤ 20, no pagination UI is rendered.

### Tags

Tags are displayed on each entry. They are not interactive — no click, no filter, no linked page.

---

## 4. Entry rendering

Each entry renders the following elements:

- **Title** — links to the external `url`, opens in a new tab, `rel="noopener noreferrer"`
- **Source** — displays `sourceName` as a link to `sourceUrl`, opens in a new tab, `rel="noopener noreferrer"`
- **Date** — formatted as `DD MMM YYYY` (e.g. `12 Apr 2025`), using the `date` frontmatter value
- **Tags** — displayed as a flat list of labels
- **Comment** — rendered only if the MDX body is present; visually distinct from the title and metadata

No entry has its own detail page. The feed is the only surface.

---

## 5. Pagination UI

The pagination component renders only when the total number of entries exceeds 20.

It must display:

- A link to the previous page (disabled or absent on page 1)
- The current page number and total page count
- A link to the next page (disabled or absent on the last page)

Pagination is static HTML — no JavaScript.

---

## 6. RSS feed

An RSS feed is generated at build time and exposed at `/veille/rss.xml`.

Each entry in the feed includes:

- `title` — the entry title
- `link` — the external `url`
- `pubDate` — the entry `date`
- `description` — the comment body if present, empty otherwise
- `source` — `source.name` linking to `source.url`

The feed is generated using `@astrojs/rss`. Tags are not included in the feed.

---

## 7. Out of scope

- Tag filtering or tag pages
- Search
- Comments or reactions
- Entry detail pages