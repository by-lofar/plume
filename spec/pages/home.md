# spec: /

> Scope: structure, content model, and rendering rules for the home page.

---

## 1. Purpose

The home page establishes identity at a glance and provides immediate access to contact and social links. It also surfaces the most recent veille entries to signal active curation.

All content on this page is written in French.

---

## 2. Content model

The home page has no content collection. All content is static and defined directly in the page or in a dedicated configuration file `src/data/home.json`.

| Field         | Type     | Required | Description                               |
|---------------|----------|----------|-------------------------------------------|
| `name`        | `string` | yes      | Full name                                 |
| `title`       | `string` | yes      | Professional title (e.g. `Développeur craft & IA`) |
| `description` | `string` | yes      | Short pitch — a few sentences             |
| `contact.email` | `string` | yes    | Contact email address                     |
| `social.github` | `string` | yes    | GitHub profile URL                        |
| `social.linkedin` | `string` | yes  | LinkedIn profile URL                      |
| `social.bluesky` | `string` | yes   | Bluesky profile URL                       |
| `social.mastodon` | `string` | yes  | Mastodon profile URL                      |
| `social.rss`  | `string` | yes      | RSS feed URL — points to `/veille/rss.xml` |

---

## 3. Page structure

The page renders three sections in order:

1. **Hero** — identity and contact
2. **Liens** — social and contact links
3. **Veille** — preview of the most recent entries

---

## 4. Hero rendering

The hero renders:

- **Name** — primary heading
- **Title** — professional title, visually secondary to the name
- **Description** — short pitch, a few sentences
- **CTA** — a mailto link to `contact.email`, labelled `Me contacter`

No image, no background, no decorative elements.

---

## 5. Social links rendering

A flat list of icon-free links. Each link opens in a new tab with `rel="noopener noreferrer"`.

Links rendered:

- GitHub
- LinkedIn
- Bluesky
- Mastodon
- RSS — links to `/veille/rss.xml`

Labels are the platform name. No icons unless they can be rendered in pure CSS or inline SVG without an icon library dependency.

---

## 6. Veille preview rendering

Displays the **`VEILLE_PREVIEW_COUNT`** most recent veille entries, sorted by `date` descending. Fetched at build time from the veille content collection. `VEILLE_PREVIEW_COUNT` is defined in `src/config.ts` and adjusted based on visual fit.

Each entry renders:

- **Title** — links to the external `url`, opens in a new tab, `rel="noopener noreferrer"`
- **Source** — `source.name` as a link to `source.url`
- **Date** — formatted as `DD MMM YYYY`

Tags and comments are not shown in the preview. The section includes a link to `/veille` to access the full feed.

---

## 7. Out of scope

- Contact form — a mailto link is sufficient
- Featured or pinned veille entries
- Animation or scroll-triggered effects