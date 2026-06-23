---
layout: page
title: Index
permalink: /
---

# Stable Promotion Git Flow

**Subtitle:** A simple Git workflow for validated releases, clean history, and stable rollback.

**Tagline:** Validate first. Promote cleanly. Roll back safely.

Stable Promotion Git Flow is for solo developers and small teams who want **validated releases**, **clean history**, and **stable rollback**.

Created by **stevoh6**, based on more than decade of practical development, release, and production workflow experience.

AI-assisted editing was used solely to improve the clarity and readability of this document. The underlying process model, workflow design, decisions, and release strategy were developed independently by the author and are based on real-world practice and experience.

## Core Flow

```text
main/master is stable production
        ↓
create/reset release/stable from main/master
        ↓
feature/* -> staging/vX.Y
        ↓
QA / UAT / release validation
        ↓
approved feature/* -> main/master
        ↓
tag release or use updated main/master
        ↓
test environment quick check
        ↓
production
        ↓
after stabilization, move release/stable to new tag
        ↓
reset/recreate staging for next release if needed
```

## Rules

- `feature/*` branches are created from `main` / `master`.
- `release/stable` is captured before release work.
- `staging/*` validates the release candidate.
- `staging/*` is never merged into `main` / `master`.
- Approved features are promoted directly into `main` / `master`.
- Features are squashed by default before production promotion.
- Large features may be split when that is safer.
- The new release tag, or updated `main` / `master` state, is tested before production.
- `release/stable` is moved only after production stabilization.

## Full Documentation

- [Workflow Guide]({{ '/workflow/' | relative_url }}) — practical release flow, branch rules, and operational process.
- [Design Rationale]({{ '/explained/' | relative_url }}) — why the workflow works this way and what trade-offs it makes.
- [Commands]({{ '/commands/' | relative_url }}) — copy-paste Git commands for daily use.

## License

This project uses dual licensing:

- Documentation and workflow descriptions: **CC BY-SA 4.0**
- Source code, scripts, and automation tools: **MIT License**
