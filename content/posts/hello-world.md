---
title: "Hello World"
date: 2026-07-06T09:00:00+10:00
draft: false
tags: [meta]
summary: "First post — what this blog is and how it's built."
---

Welcome. This blog documents homelab, Linux, and self-hosted infrastructure
tinkering.

## How this site works

The site is built with [Hugo](https://gohugo.io/) using the
[PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme, hosted on
GitHub Pages, and deployed automatically by GitHub Actions on every push to
`main`.

## Front matter conventions

This post doubles as a template. Every post starts with:

```yaml
---
title: "Post Title"
date: 2026-07-06T12:00:00+10:00
draft: true          # flip to false (or delete) to publish
tags: [homelab, linux]
summary: "One-line description shown in lists and RSS."
---
```

Code blocks get syntax highlighting and a copy button:

```bash
hugo new posts/my-next-post.md
hugo server -D   # preview with drafts at http://localhost:1313
```

That's it. More to come.
