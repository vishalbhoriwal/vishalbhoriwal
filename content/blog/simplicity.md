+++
title = "The Simplest Thing That Could Possibly Work"
date = 2024-03-20
lastmod = 2024-03-20
draft = false
slug = "simplicity"
description = "A reminder to reach for complexity only when simplicity has genuinely failed."
tags = ["software-engineering", "design", "engineering-philosophy"]
featured = true
author = "Vishal Bhoriwal"
cover = "https://images.unsplash.com/photo-1517816743773-6e0fd518b4a6?w=1200&q=80&fit=crop"
+++

There's a question from Extreme Programming that I keep returning to:

> *What is the simplest thing that could possibly work?*

It sounds almost naive. But it's one of the most useful filters I've found when designing systems.

## The Complexity Trap

Engineers — myself included — tend to over-engineer. We anticipate scale, future requirements, and edge cases that may never materialize. We reach for distributed systems before we've saturated a single machine. We add abstraction layers before we have two concrete cases to abstract.

The result is systems that are hard to understand, hard to change, and hard to debug.

## Start Simple

Starting simple doesn't mean being naive. It means:

- Choosing the boring technology that you understand
- Building the thing that solves *today's* problem
- Deferring complexity until the cost of not having it is clear

A SQLite database might be all you need. A single server might handle your load for years. A simple function might be better than a class hierarchy.

## When to Add Complexity

Complexity earns its place when:

1. The simple thing has actually failed (not "might fail")
2. You understand *why* it failed
3. The added complexity solves exactly that problem

Not before.

The goal isn't to stay simple forever. It's to let the problem teach you what complexity it actually needs.
