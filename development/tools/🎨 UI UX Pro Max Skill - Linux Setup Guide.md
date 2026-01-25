---
title: 🎨 UI UX Pro Max Skill - Linux Setup Guide
date: 2026-01-25
updated: 2026-01-25
tags:
  - development
  - tools
  - ai
share: true
featured: true
---
![Pasted image 20260125225902](https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/Pasted%20image%2020260125225902.png)

This guide covers the installation and integration of the **UI/UX Pro Max** skill for Linux environments, specifically optimized for **Antigravity**.

## 🚀 Installation

### 1. Prerequisite: Python 3.x

Ensure Python 3 is installed (needed for the search & reasoning engine):

```bash

# Ubuntu/Debian

sudo apt update && sudo apt install python3 -y

```

### 2. Install UI Pro CLI

Install the CLI globally using npm (as requested with sudo):

```bash

sudo npm install -g uipro-cli

```

### 3. Initialize for Antigravity

Navigate to your project directory and link the skill to your AI assistant:

```bash

cd /path/to/your/project

uipro init --ai antigravity

```

---

## 🛠️ Usage in Antigravity

Once installed, the skill activates automatically for UI/UX requests. You can also trigger it explicitly.

### Basic Prompting

> /ui-ux-pro-max Build a landing page for my SaaS product

### What happens behind the scenes:

1. **Request Analysis**: Your requirement is parsed.

2. **Design System Generation**: AI generates a complete style guide (Colors, Typography, Patterns).

3. **Execution**: Antigravity implements the code based on these rules.

---

## 💎 Advanced Design System (Master + Overrides)

Save your design system to files for persistent use across sessions.

### Generate & Persist Master File

This creates a `design-system/MASTER.md` file.

```bash

python3 .gemini/skills/ui-ux-pro-max/scripts/search.py "SaaS dashboard" --design-system --persist -p "MyAppName"

```

### Page-Specific Overrides

If you need a specific style for a sub-page (e.g., "Checkout"):

```bash

python3 .gemini/skills/ui-ux-pro-max/scripts/search.py "SaaS dashboard" --design-system --persist -p "MyApp" --page "checkout"

```

> [!TIP]

> **Context-Aware Retrieval**: When building a page, mention:

> _"I am building the Checkout page. Please check design-system/MASTER.md and design-system/pages/checkout.md."_

---

## ✅ Pre-Delivery Checklist

Before finishing a feature, ensure:

- [x] **No emojis as icons** (Use Lucide/Heroicons SVG).

- [ ] **Cursor-pointer** on all clickable elements.

- [ ] **Smooth transitions** (150-300ms) for hover states.

- [ ] **Accessibility**: Contrast ratio 4.5:1 minimum (WCAG AA).

- [ ] **Responsive check**: 375px, 768px, 1024px, 1440px.

---

## 🔍 Standalone Commands

Useful for quick reference without full code generation:

- **Style Search**: `python3 .gemini/skills/ui-ux-pro-max/scripts/search.py "glassmorphism" --domain style`

- **Typography Pairing**: `python3 .gemini/skills/ui-ux-pro-max/scripts/search.py "elegant serif" --domain typography`

- **Stack Guides**: `python3 .gemini/skills/ui-ux-pro-max/scripts/search.py "responsive layout" --stack html-tailwind`
