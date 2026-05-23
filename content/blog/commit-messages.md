+++
title = "On Writing Good Commit Messages"
date = 2024-02-10
draft = false
slug = "commit-messages"
description = "A commit message is a letter to your future self. Make it worth reading."
+++

A commit message is a letter to your future self. Make it worth reading.

## The Problem with Bad Commits

We've all seen them:

```
fix
wip
asdf
final final FINAL
```

These tell you nothing. Six months later, when a bug surfaces and you're spelunking through `git log`, these are useless noise.

## The Rule of Thumb

The subject line of a commit should complete this sentence:

> *If applied, this commit will...*

So: "Add input validation to login form" rather than "validation stuff".

## Structure

A good commit:

1. **Subject** — 50 chars or less, imperative mood, no period
2. **Blank line**
3. **Body** (optional) — explain *what* and *why*, not *how*

```
Add rate limiting to the auth endpoint

Without this, a single IP could spam login attempts indefinitely.
Uses a sliding window of 10 requests per minute per IP, backed by Redis.
```

## Why It Matters

The diff shows *what* changed. The commit message explains *why*. Both together are what make a codebase navigable years later.

Treat your commit history like documentation — because it is.
