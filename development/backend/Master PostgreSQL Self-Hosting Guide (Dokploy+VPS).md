---
title: Master PostgreSQL Self-Hosting Guide (Dokploy+VPS)
date: 2026-01-23
updated: 2026-01-25
tags:
  - development
  - backend
  - databases
share: true
image: https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/Pasted%20image%2020260125195930.png
---

![Pasted image 20260125195930](https://pub-49b2468145c64b14a4a172c257cf46b8.r2.dev/2026/01/Pasted%20image%2020260125195930.png)**Version:** Postgres **17.7 (Server)** | Postgres **18.1 (Local Client)**  
**Status:** ✅ SSL Enabled | ✅ WAL Logical Enabled | ✅ Multi-Tenant Ready

This guide documents the **complete setup and operation** of a production-ready, multi-tenant PostgreSQL instance hosted on **Oracle Cloud (OCI)**, deployed via **Dokploy**, and secured with **SSL**.

It also covers **proper schema-level permissions** required for modern stacks like **Next.js, Prisma, and migrations**.

---

## 0. Prerequisites

- OCI VM (Ubuntu/Debian recommended)
    
- Dokploy installed
    
- Cloudflare DNS
    
- Docker & Docker Compose
    
- PostgreSQL client locally (`psql`)
    

---

## 1. Cloudflare DNS Configuration 🌐

To expose PostgreSQL safely over a custom domain, **Cloudflare proxy must be disabled**.

### DNS Record

- **Type:** `A`
    
- **Name:** `pg`
    
- **IPv4:** `193.123.91.169`
    
- **Proxy Status:** ☁️ **DNS Only (Grey Cloud)**
    

> ⚠️ **Important**  
> Cloudflare’s Orange Cloud **blocks non-HTTP ports** like `5432`.

---

## 2. Firewall & Network Security 🔐

Postgres must be allowed at **both OCI level and OS level**.

### A. OCI Security List

**Networking → Security Lists → Default Security List**

Add **Ingress Rule**:

|Setting|Value|
|---|---|
|Source|`0.0.0.0/0`|
|Protocol|TCP|
|Port|`5432`|

---

### B. Server OS Firewall (Persistent)

Run **on the host VM** (not inside Docker):

```bash
sudo iptables -I INPUT 6 -p tcp --dport 5432 -j ACCEPT
sudo netfilter-persistent save
```

---

## 3. Dokploy – PostgreSQL with SSL & WAL 🐳

This setup uses a **PostgreSQL 17 image with SSL preconfigured** and enables **logical replication** (required for Prisma Pulse, CDC, etc.).

```yaml
services:
  postgres-db:
    image: ghcr.io/railwayapp-templates/postgres-ssl:17
    restart: always
    environment:
      POSTGRES_USER: ${POSTGRES_USER:-my-pg-user}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-my-pg-password}
      POSTGRES_DB: ${POSTGRES_DB:-my-pg-db}
    command:
      - "postgres"
      - "-c"
      - "wal_level=logical"
      - "-c"
      - "max_replication_slots=10"
      - "-c"
      - "max_wal_senders=10"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - dokploy-network

networks:
  dokploy-network:
    external: true

volumes:
  postgres_data:
```

---

## 4. Initial Connection & Verification 🔌

### Connect from Local Machine

```bash
psql postgresql://my-pg-user:my-pg-password@pg.ahmedlotfy.site:5432/my-pg-db
```

Expected:

- ✅ SSL connection
    
- ✅ Server responds (no timeout)
    

---

## 5. Multi-Tenant Architecture (Users & Databases) 🏗️

Each project gets:

- **One database**
    
- **One dedicated user**
    
- **Schema-level privileges**
    

This avoids cross-project access and supports Prisma migrations.

---

## 6. Create Users & Databases 👤📦

Run while connected as the **main Postgres admin user**.

### Example Projects Created

```sql
CREATE USER selftracker_user WITH PASSWORD 'secure_password';
CREATE DATABASE selftracker_db OWNER selftracker_user;
GRANT ALL PRIVILEGES ON DATABASE selftracker_db TO selftracker_user;

CREATE USER myportfolio_user WITH PASSWORD 'secure_password';
CREATE DATABASE myportfolio_db OWNER myportfolio_user;
GRANT ALL PRIVILEGES ON DATABASE myportfolio_db TO myportfolio_user;
```

> 🔐 **Best Practice**  
> Use **strong unique passwords** per user (later rotated).

---

## 7. REQUIRED: Schema-Level Permissions (Very Important ⚠️)

> Without this, **Prisma / migrations will fail** even if DB ownership is correct.

---

### Step 1: Connect to Target Database

```sql
\c zamalek_store_db
```

---

### Step 2: Grant Schema Access

```sql
GRANT USAGE ON SCHEMA public TO zamalek_store_user;
```

---

### Step 3: Grant Table & Sequence Permissions

```sql
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO zamalek_store_user;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO zamalek_store_user;
```

---

### Step 4: Auto-Grant for Future Tables (Migrations)

```sql
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT ALL ON TABLES TO zamalek_store_user;

ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT ALL ON SEQUENCES TO zamalek_store_user;
```

✅ This ensures **new tables created by Prisma automatically work**.

---

## 8. Connection String 🔗

Use this format **everywhere** (apps, Prisma, migrations):

```text
postgresql://user:password@pg.ahmedlotfy.site:5432/db_name?sslmode=require
```

---

## 9. Data Migration (Local → Server) 🚚

Safest way to migrate across Postgres versions:

```bash
docker run --rm -i postgres:17-alpine psql \
"postgresql://user:PASSWORD@pg.ahmedlotfy.site:5432/db_name?sslmode=require" \
< your_local_dump.sql
```

---

## 10. Verification Checklist ✅

Run these inside `psql`:

|Check|Command|Expected|
|---|---|---|
|WAL Enabled|`SHOW wal_level;`|`logical`|
|SSL Active|`SELECT ssl_is_used();`|`t`|
|Version|`SELECT version();`|`PostgreSQL 17.x`|

---

## ✅ Final Result

You now have:

- 🔐 SSL-secured PostgreSQL
    
- 🧱 Isolated multi-tenant databases
    
- 🔄 Prisma-ready permissions
    
- ☁️ OCI + Dokploy hardened setup
    
- 🧠 A reusable playbook for future projects
    

---

If you want, next we can:

- 🔁 Turn this into a **template for new projects**
    
- 🔐 Add **read-only users**
    
- 📊 Add **pgAdmin / monitoring**
    
- 🧪 Add **backup + restore automation**
    

Just say the word 🚀