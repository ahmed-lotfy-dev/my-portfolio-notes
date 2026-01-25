---
title: 🎯 The Definitive Guide to Image Privacy & Orientation in Laravel
date: 2026-01-24
tags:
  - development
  - backend
  - databases
  - postgresql
  - psql
  - env
  - docker
  - containers
  - dev
  - devops
  - security
  - authentication
  - ssl-tls
share: true
---
#postgresql #psql #env #docker #containers #dev #backend #devops #security #authentication #ssl-tls 
## ✅ Correct `psql` Command (Local Docker)

`psql -h 127.0.0.1 -p 5432 -U my-pg-user my-pg-db`

- - - 
### What each part means

- `-h 127.0.0.1` → host (Docker is exposing Postgres locally)
    
- `-p 5432` → port (omit if default)
    
- `-U my-pg-user` → database user
    
- `my-pg-db` → database name
    

You’ll then be prompted for the password.

- - -
## 🔐 If SSL Is Required (Docker SSL Image)

For SSL-enabled containers, explicitly require it:

```
psql "postgresql://my-pg-user:my-pg-password@127.0.0.1:5432/my-pg-db?sslmode=require"
```
## ✅ Full `psql` Command (Password Inline)

```
psql "postgresql://my-pg-user:my-pg-password@127.0.0.1:5432/my-pg-db"
```

- - - 
## 🔐 If SSL Is Required (your Docker SSL image)

`psql "postgresql://my-pg-user:my-pg-password@127.0.0.1:5432/my-pg-db?sslmode=require"`

---
## ⚠️ Quick Security Note (important but optional)

Putting passwords directly in the command:

- saves to shell history
    
- visible via `ps aux`
    

**Safer alternative (still non-interactive):**

`PGPASSWORD="my-pg-password" psql -h 127.0.0.1 -p 5432 -U my-pg-user my-pg-db`

- - -
## 🐳 Quick Docker Sanity Checks

Make sure Postgres is actually exposed:

`docker ps`

You should see something like:

`0.0.0.0:5432->5432/tcp`

And logs should show:

`docker logs postgres-db`
