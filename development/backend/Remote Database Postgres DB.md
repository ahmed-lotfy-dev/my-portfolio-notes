---
title: Remote Database Postgres DB
date: 2026-01-22
updated: 2026-01-25
tags:
  - development
  - backend
  - databases
share: true
image: https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/Pasted%20image%2020260125195930.png
---
## 1. Context

Connecting a local Node.js application (or a deployed Serverless function) to a **Remote** PostgreSQL instance. Unlike local Docker setups, remote connections require strict handling of **SSL/TLS** encryption and **Network Security** (Firewalls/Whitelisting).

---

## 2. Security Prerequisites (Before Code)

**1. IP Whitelisting (The #1 Blocker)** Remote databases usually block all incoming traffic by default.

- **AWS RDS/EC2:** Update "Security Groups" to allow Inbound traffic on port `5432` from your IP.
    
- **Vercel/Netlify:** You may need to "Allow all" `0.0.0.0/0` (risky) or use a static IP integration if available.
    
- **Supabase/Neon:** Usually open by default, but check "Network Restrictions".
    

**2. SSL/TLS** Production traffic must be encrypted. You cannot connect via plain text.

---

## 3. Database Setup (CLI Workflow)

_Execute via terminal using `psql`. Use this exact flow to avoid syntax errors._

### Step A: Connect as Root/Admin

Bash