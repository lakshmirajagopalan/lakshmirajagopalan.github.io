# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a personal blog built with Jekyll using the [Chirpy theme](https://github.com/cotes2020/jekyll-theme-chirpy). It is hosted on GitHub Pages at `https://lakshmirajagopalan.github.io`.

## Commands

`bundle` is not on PATH by default. Use the full path (Homebrew Ruby 4.x at `/usr/local/opt/ruby/bin/`):

```bash
# Install dependencies (first time or after Gemfile changes)
/usr/local/opt/ruby/bin/bundle install

# Serve locally with live reload
/usr/local/opt/ruby/bin/bundle exec jekyll serve --livereload
# Site available at http://127.0.0.1:4000/

# Build only (no server)
/usr/local/opt/ruby/bin/bundle exec jekyll build
```

## Adding Content

### New blog post
Create `_posts/YYYY-MM-DD-title.md` with front matter:
```yaml
---
layout: post
title: "Post Title"
categories: [Category Name]
tags: [tag-one, tag-two]
---
```

**Always** update `tabs/tags.md` and `tabs/categories.md` when adding a new post (even if no new tags/categories are introduced — they aggregate all posts).

### New category
If the category is new, also create `categories/<lowercase-hyphenated>.html`:
```yaml
---
layout: category
title: Category Name
category: Category Name
---
```

### New tag
If the tag is new, also create `tags/<lowercase-hyphenated>.html`:
```yaml
---
layout: tag
title: tag name
tag: tag name
---
```

## Architecture

- **`_config.yml`** — Site-wide settings: author, social links, theme mode, pagination, permalink structure (`/posts/:title/`), and excluded paths.
- **`_posts/`** — Blog posts as Markdown. Filename format: `YYYY-MM-DD-slug.md`.
- **`_layouts/`** — Page templates. Posts use `post.html`, home uses `home.html`, tabs use `page.html`.
- **`_includes/`** — Reusable HTML partials (sidebar, topbar, footer, search, sharing, etc.).
- **`_data/`** — YAML config for tabs navigation (`tabs.yml`), date format, labels, and share buttons.
- **`categories/`** and **`tags/`** — Each category/tag requires a stub HTML file to generate its listing page.
- **`tabs/`** — Static nav pages (About, Archives, Categories, Tags).
- **`assets/`** — CSS (Sass), JS, and images. Theme CSS uses `@import` — Sass deprecation warnings during build are harmless.
- **`vendor/bundle/`** — Bundler installs gems here (excluded from Jekyll build via `_config.yml`).
