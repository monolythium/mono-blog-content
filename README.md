# mono-blog-content

Markdown source for the Monolythium blog at [monolythium.com/blog](https://monolythium.com/blog).

This repository is consumed by the Monolythium website as a git submodule. Pushing to `master` triggers a website rebuild and the new content goes live.

## Structure

```
.
├── README.md
├── posts/
│   ├── <slug>.md        ← one file per post
│   └── <slug>.md
└── covers/              ← cover images referenced from post frontmatter
    └── <filename>.png
```

The filename slug (without `.md`) becomes the URL: `monolythium.com/blog/<slug>/`.

## Frontmatter

Every post starts with a YAML frontmatter block:

```yaml
---
title: "Your post title"
date: 2026-05-24
excerpt: "One- or two-sentence summary used on the index page, in OG/Twitter cards, and for SEO."
cover: "/blog-covers/your-cover.png"   # optional; reference an image you placed in covers/
coverAlt: "Short alt text for the cover image"  # optional
category: "Ecosystem"                  # optional, one of: Ecosystem, Engineering, Community, Mono Core, MonoHub, MonoPlay, MonoCard
tags: ["testnet", "release"]           # optional
author: "Monolythium Foundation"       # optional
updated: 2026-05-25                    # optional
draft: false                           # optional; set true to hide
---
```

Fields:

| Field | Required | Notes |
|---|---|---|
| `title` | yes | The post title, plain text. |
| `date` | yes | ISO date or `YYYY-MM-DD`. The published date. |
| `excerpt` | yes | Used on the index page, OG cards, and SEO meta description. ~140–200 chars. |
| `cover` | no | Path to a cover image. Must start with `/blog-covers/` — the website copies `covers/*` to `public/blog-covers/` at build time. |
| `coverAlt` | no | Alt text for accessibility. |
| `category` | no | Singular category label. |
| `tags` | no | Array of tag labels. |
| `author` | no | Defaults to "Monolythium Foundation" in display. |
| `updated` | no | Updated date if different from `date`. |
| `draft` | no | If `true`, the post won't appear on the live site. |
| `archive` | no | Optional archive label (e.g. `monolythium-v1`). Posts with this set get a v1 pill on the card and a banner at the top of the post explaining the content predates the current chain. |

## Writing posts

Posts are plain markdown. Standard CommonMark plus GFM (tables, strikethrough, task lists, fenced code).

```markdown
## A section heading

Body paragraph with **bold**, *italic*, and [links](https://monolythium.com).

- Bullet
- Bullet

```rust
fn anchor() {}
```
```

Code blocks support syntax highlighting via Astro's built-in Shiki integration. Use a language identifier on the fence (e.g. ` ```rust `, ` ```bash `, ` ```js `).

## Cover images

Drop new cover images into `covers/`, then reference them in the post's frontmatter as `/blog-covers/<filename>`.

The website's build step copies `covers/*` to its `public/blog-covers/` directory; the public URL is `https://monolythium.com/blog-covers/<filename>`.

## Editing flow

1. Pull or edit a `.md` file (locally or via the GitHub web UI).
2. Push to `master`.
3. Website CI rebuilds with the latest content; post is live in ~1 minute.

## License

Content licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/).
