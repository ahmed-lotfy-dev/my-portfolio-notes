---
title: Building a Zero-Effort Obsidian to Portfolio Workflow
date: 2026-01-25
updated: 2026-01-25
image: '""'
tags:
  - automation
  - obsidian
  - cloudflare-r2
  - github
  - nextjs
share: true
---

# Building a Zero-Effort Obsidian to Portfolio Workflow

Turning local notes into a professional portfolio blog is easy in theory, but making it **truly automated** and **scalable** is an engineering challenge. Here is how I built a "Headless CMS" using Obsidian, GitHub, and Cloudflare R2.

## The Goal
I wanted to write notes in Obsidian and have them appear on my portfolio website with zero manual work—no copy-pasting, no manual uploads, and perfect metadata.

---

## 🛑 Problem 1: The Metadata Mess
**The Problem**: My 80+ existing notes didn't have consistent frontmatter. Some were missing titles, others didn't have dates or categories. For a blog to work, every file needs a standardized YAML schema.

**The Solution**: We built a custom **Bun processing script**. 
- It scanned the entire vault.
- Extracted titles from filenames.
- Generated hashtags from the folder hierarchy and content.
- Standardized the schema to: `title`, `date`, `updated`, `tags`, and `share`.

## 🛑 Problem 2: Repository "Ghost" Files
**The Problem**: Initially, the sync was pushing "flat" files to the root of GitHub. When we switched to a structured folder approach, the old files stayed at the root, creating a duplicate mess.

**The Solution**: We enabled the **Autoclean** feature in the Enveloppe (MKDocs Publisher) plugin. This ensures that GitHub is always a 1:1 mirror of the vault. If a file is moved or "unshared" in Obsidian, it vanishes from GitHub automatically.

## 🛑 Problem 3: Avoiding GitHub Bloat
**The Problem**: Images. Storing screenshots in Git is fine at first, but it makes the repository "heavy" and slow to clone over time.

**The Solution**: The **Cloudflare R2 + Image Upload Toolkit** strategy.
- We configured an S3-compatible bucket on R2.
- Set up a toolkit that "hooks" into the notes.
- When an image is added, it's uploaded to R2, and the local path is instantly swapped for a blazing-fast CDN URL.
- This keeps the GitHub repo restricted to **100% text**, making it ultra-lightweight.

## 🛑 Problem 4: Internal Link Brokenness
**The Problem**: Obsidian uses `[[Wikilinks]]`, but web browsers and blog frameworks expect standard Markdown `![image](path)`. Bringing Obsidian notes to a blog usually means broken images and links.

**The Solution**: We configured the sync plugin to perform **on-the-fly conversion**. During the push to GitHub, it translates Obsidian-specific syntax into standard Markdown, making the files "web-ready" without me having to change how I write.

---

## The Result: The "Pro" Workflow
Now, my workflow is invisible:
1. **Write**: I use my standardized `Blog Template`.
2. **Standardize**: I drag an image in, and it's optimized and uploaded to R2 automatically.
3. **Publish**: I set `share: true` and hit the "Share" command.

My portfolio (built with Next.js) simply fetches these clean, structured files from GitHub and renders them as beautiful, SEO-optimized pages.

**Verdict**: Don't build a database for your blog. Build a pipeline for your notes. 🚀
