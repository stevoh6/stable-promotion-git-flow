# Stable Promotion Git Flow

**A simple Git workflow for validated releases, clean history, and stable rollback.**

> Validate first. Promote cleanly. Roll back safely.

Stable Promotion Git Flow is a lightweight Git workflow for solo developers and small teams who want to validate release content before promotion, keep production history clean, and preserve a stable rollback path.

## Why This Is Written Down

Some of these ideas are common in practical release work as internal habits, conventions, or release routines. This exact combination, including the small operational details that make it repeatable, is often not written down as a compact, reusable workflow.

This document defines that specific variant and makes its trade-offs explicit. It combines release validation, clean production history, stable rollback, and selected operational details based on practical experience.

The goal is to make the workflow easier to communicate, evaluate, and apply consistently.

## Website

GitHub Pages version:

```text
https://stevoh6.github.io/stable-promotion-git-flow/
```

The website is generated from [`docs/`](docs/). The same documentation can be read directly on GitHub. Command examples are included in the operational workflow.

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

## Main Rules

1. Create `feature/*` branches from `main` / `master`.
2. Before release work, create/reset `release/stable` from stable `main` / `master`.
3. Merge features into `staging/vX.Y` for QA/UAT/release validation.
4. Keep fixes on the original feature branch.
5. Do not merge `staging/*` into `main` / `master`.
6. Promote only approved features into `main` / `master`.
7. Squash feature history by default before production promotion.
8. Split large features into multiple logical commits when that is safer.
9. Create release tags only from `main` / `master`.
10. Deploy the new release tag, or the updated `main` / `master` state, to a test environment and quick-check it before production.
11. Deploy the same verified release state to production.
12. Move `release/stable` only after production stabilization.
13. Reset or recreate `staging/*` for the next release cycle when needed.

## What This Solves

- Developers can commit freely on feature branches.
- QA can validate multiple features together on a release branch.
- `main` / `master` stays clean, readable, and production-focused.
- Each completed feature becomes one production commit by default.
- Large features can be split into multiple logical production commits when safer.
- The new release tag, or the updated `main` / `master` state, is tested before production.
- `release/stable` stays available as a predictable rollback target.

## Documentation

- [Workflow Guide](docs/workflow.md) — operational workflow, rules, and commands.
- [Design Rationale](docs/explained.md) — reasoning, trade-offs, and design principles.
- [Commands cheatsheet](docs/commands.md) — compact command reference.

## Author

Created by **stevoh6**, based on more than decade of practical development, release, and production workflow experience.

AI-assisted editing was used solely to improve the clarity and readability of this document. The underlying process model, workflow design, decisions, and release strategy were developed independently by the author and are based on real-world practice and experience.

## License

This repository uses dual licensing.

- Documentation and workflow descriptions are licensed under **CC BY-SA 4.0**.
- Source code, scripts, and automation tools are licensed under the **MIT License**.

See [LICENSE](LICENSE), [LICENSE-CC-BY-SA-4.0.md](LICENSE-CC-BY-SA-4.0.md), and [LICENSE-MIT](LICENSE-MIT).
