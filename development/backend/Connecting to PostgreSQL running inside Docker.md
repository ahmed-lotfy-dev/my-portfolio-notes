---
title: Connecting to PostgreSQL running inside Docker
date: 2026-01-24
updated: 2026-01-25
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
featured: true
---
Connecting to PostgreSQL running inside Docker is simple once you understand what is actually happening under the hood. Most problems come from mixing container networking concepts with local machine assumptions. Let’s straighten it out step by step.

## The basic idea

When PostgreSQL runs inside a Docker container and you expose its port, Docker acts like a bridge. Your local machine talks to Docker, and Docker forwards traffic into the container.

That means you do not connect to the container IP.  
You connect to your own machine, usually `127.0.0.1`, on the port Docker exposed.

## The correct local `psql` command

`psql -h 127.0.0.1 -p 5432 -U my-pg-user my-pg-db`

This is the most common and correct setup for local development.

What each part means in plain terms:

- `-h 127.0.0.1`  
    You are connecting to your local machine. Docker is listening here and forwarding the connection to Postgres inside the container.
    
- `-p 5432`  
    The port exposed by Docker. If you mapped `5432:5432`, this is correct. If you used another port, use that one instead.
    
- `-U my-pg-user`  
    The database role you created when initializing Postgres.
    
- `my-pg-db`  
    The database name you want to connect to.
    

After running this command, `psql` will ask for the password interactively.

## Using a connection URL instead

Postgres also supports a single connection string. This is useful for scripts and tooling.

`psql "postgresql://example_user:example_password@127.0.0.1:5432/my-pg-db"`

This format is common in ORMs, environment variables, and CI pipelines. It encodes the same information as the flags, just in one string.

## When SSL is required

Some Docker images or production-like setups enforce SSL connections. In that case, Postgres will reject plain connections unless you explicitly request SSL.

You do that with `sslmode=require`.

`psql "postgresql://example_user:example_password@127.0.0.1:5432/my-pg-db?sslmode=require"`

If SSL is mandatory and you forget this flag, the error message is usually confusing. Knowing this upfront saves time.

## Avoiding passwords in command history

Putting passwords directly in commands has two problems:

- They are saved in your shell history.
    
- They are visible to other processes via tools like `ps aux`.
    

A safer non-interactive approach is to use an environment variable for just that command:

`PGPASSWORD="example_password" psql -h 127.0.0.1 -p 5432 -U example_user example-db`

This avoids typing the password and avoids storing it permanently.

For long-term setups, `.pgpass` is even better, but that is a separate topic.

## Docker sanity checks when things fail

If the connection does not work, check Docker before touching Postgres configs.

First, verify the container is running and exposing the port:

`docker ps`

You should see something like:

`0.0.0.0:5432->5432/tcp`

If you do not see this mapping, Postgres is not reachable from your machine.

Next, inspect the logs:

`docker logs postgres-db`

You want to see messages indicating Postgres started successfully and is listening on the expected port.

If Postgres is healthy and the port is exposed, `psql` will work. If it does not, the issue is almost always credentials, database name, or SSL mode.

## Mental model to keep

Local machine  
→ Docker port mapping  
→ Postgres inside container

Once that chain is clear, connecting to Postgres in Docker stops feeling mysterious and starts feeling boring. Boring is good in backend work.