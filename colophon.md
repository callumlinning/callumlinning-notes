---
layout: default
title: Colophon
description: How this site is made.
permalink: /colophon/
---

<article class="post colophon">

  <header class="post-header">
    <a class="back-link" href="{{ '/' | relative_url }}">← Notes</a>

    <h1>Colophon</h1>

    <p class="post-intro">{{ page.description }}</p>
  </header>

<div class="post-content" markdown="1">

This site is plain HTML and CSS, generated from Markdown files by a small Jekyll setup. There are no frameworks, no analytics, no cookies and no third-party requests. I built it with Claude, starting in November 2026.

## Type

| Typeface | [Geist](https://vercel.com/font), self-hosted as a single variable font file |
| Code | The system monospace: SF Mono, Consolas or Liberation Mono |

## Colour

| Background | `#fafafa` |
| Text | `#333333` |
| Muted | `#6f6f6f` |
| Lines | `#e8e8e8` |

## Build

| Writing | Markdown, one file per note |
| Generator | [Jekyll](https://jekyllrb.com), with kramdown and Rouge for code |
| Hosting | GitHub Pages, deployed by GitHub Actions on every push |
| Schedule | A daily rebuild at 06:00 so notes dated in the future go live on the day |

## Scripts

There's a little JavaScript, mostly on notes: image zoom, copy buttons on code blocks and links to section headings. Every page also says hello in the browser console. Everything still reads fine without it.

</div>

</article>

{% include footer.html %}
