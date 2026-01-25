---
title: Building a Zero-Effort Obsidian to Portfolio Workflow
date: 2026-01-25
updated: 2026-01-25
image: https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/unnamed.jpg
tags:
  - automation
  - obsidian
  - cloudflare-r2
  - github
  - nextjs
share: true
featured:
---

![unnamed](https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/unnamed.jpg)

# Building a Zero-Effort Obsidian to Portfolio Workflow

Turning local notes into a professional portfolio blog is easy in theory, but making it **truly automated** and **scalable** is an engineering challenge. Here is how I built a "Headless CMS" using Obsidian, GitHub, and Cloudflare R2, and the technical hurdles I overcame to make it production-ready.

## The Goal

I wanted to write notes in Obsidian and have them appear on my portfolio website with zero manual work—no copy-pasting, no manual uploads, and perfect metadata.

---

## �️ Phase 1: The Core Infrastructure

### �🛑 Problem 1: The Metadata Mess

**The Problem**: My 80+ existing notes didn't have consistent frontmatter. Some were missing titles, others didn't have dates or categories.

**The Solution**: We built a custom **Bun processing script**. It scanned the entire vault, extracted titles from filenames, and standardized the schema to: `title`, `date`, `updated`, `tags`, and `share`.

### 🛑 Problem 2: Avoiding GitHub Bloat

**The Problem**: Storing screenshots in Git makes the repo "heavy" and slow to clone.

**The Solution**: The **Cloudflare R2** strategy. By using a toolkit that "hooks" into Obsidian, images are uploaded to R2 instantly, and local paths are swapped for CDN URLs. This keeps the GitHub repo restricted to **100% text**.

---

## 🚀 Phase 2: The Portfolio Integration

### 🛑 Problem 3: The "Date Object" Runtime Crash

**The Problem**: The `gray-matter` library parses YAML dates as JavaScript `Date` objects. React components cannot render these directly, leading to "Objects are not valid as a React child" errors.

**The Solution**: Updated the GitHub service to automatically format every date as a standardized string (`YYYY-MM-DD`) before sending it to the frontend.

### 🛑 Problem 4: URL Inconsistency (Special Characters)

**The Problem**: Filenames like `Master PostgreSql (Dokploy+VPS).md` resulted in slugs with `+` and `()`. Browsers encode these inconsistently, leading to "Post Not Found" errors on the detail page.

**The Solution**: Refined the slug generation logic to use a **Strict Alphanumeric Rule**. All special characters are now stripped and replaced with dashes, ensuring bulletproof URL matching.

---

## 🎨 Phase 3: The Premium UX & Stability

### 🛑 Problem 5: React Hydration & HTML Nesting

**The Problem**: Our MDX renderer was wrapping images in `<div>` tags, which the Markdown parser then tried to nest inside `<p>` tags. In HTML, this is illegal and causes a React hydration mismatch.

**The Solution**: Added a "Smart Paragraph" component that detects block-level children (like images) and dynamically switches the outer tag from `<p>` to `<div>`.

### 🛑 Problem 6: The `next/image` Configuration Trap

**The Problem**: Next.js requires every external image hostname to be explicitly whitelisted in `next.config.ts`. With a dynamic blog, adding new CDNs or R2 buckets became a configuration bottleneck, and stale caches often triggered "Invalid src prop" errors even after updates.

**The Solution**: Refactored the entire blog to use **native `<img>` tags** with `loading="lazy"`. This removed the configuration friction entirely while maintaining high performance and 100% stability for Cloudflare R2 images.

### 🛑 Problem 7: Aggressive Development Caching

**The Problem**: Next.js production builds were working, but development (`next dev`) kept showing old data due to an aggressive 1-hour fetch cache, making it look like fixes weren't working.

**The Solution**: Implemented a **Cache-Busting Strategy** using a timestamp query parameter (`?t=now`) for GitHub API calls and a strict **Image Guard** sanitizer that blocks placeholder hostnames before they reach the UI.

---

## 🏁 Phase 4: The "Sync Station" (Ultimate Stability)

### 🛑 Problem 8: The GitHub Rate Limit Crisis

**The Problem**: Our "Pure Git" approach fetched notes live from GitHub for every visitor. GitHub's "Guest" API limits users to only **60 requests per hour**. As the blog grew, even simple development refreshes began triggering 403 Forbidden errors, crashing the entire site.

**The Solution**: Transitioned to a **Database-Backed Sync** architecture.

- **Storage**: Posts are now stored in a local PostgreSQL database (via Drizzle).

- **Sync Service**: A new `blogSync.ts` service fetches from GitHub **once** (using an authenticated `GITHUB_TOKEN` to unlock 5,000 req/hr) and UPSERTS the data into the DB.

- **Frontend**: The website now reads from the local DB repository (`blogRepo.ts`), achieving sub-millisecond load times with **zero external dependencies** for the end-user.

### 🛑 Problem 9: The Ghost Data & Caching Trap

**The Problem**: During development, Next.js's aggressive fetch cache kept displaying old/deleted notes even after the GitHub repo was cleaned.

**The Solution**: Implemented a two-pronged attack:

1. **Clear Scripts**: Built a `clear-ghost-posts.ts` utility to purge old test data from the DB.

2. **Hard Refresh**: Switched to a manual/webhook trigger system (`bun run sync:blog`) that bypasses the Next.js fetch cache and ensures a clean data slate.

---

## The Final Result: The "Sync Station" Workflow

My "Pro" workflow is now divided for maximum stability:

1. **Write (Obsidian)**: I write naturally using my `Blog Template`.

2. **Publish (Git)**: I push to GitHub. Only `share: true` notes are exported.

3. **Automate (Webhook)**: GitHub sends a "Ping" to `/api/blog/sync`, which triggers the database refresh instantly.

**The Verdict**: A blog shouldn't just be pretty—it must be **resilient**. By moving from live-fetching to a high-performance DB cache, the site is now immune to rate limits and faster than ever. 🚀
