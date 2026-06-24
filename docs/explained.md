---
layout: page
title: Design Rationale
permalink: /explained/
---

# Stable Promotion Git Flow Explained

## TL;DR

This repository uses a custom **Stable Promotion Git Flow**.

The goal:

> Develop freely, validate safely, promote cleanly, verify the release state, and keep a stable recovery point.

Main flow:

```text
feature/*
    │
    ├──► staging/vX.Y        # release candidate validation
    │
    └──► main/master         # clean approved production history
            │
            ▼
      tag or updated branch  # release state
            │
            ▼
      test environment       # quick verification
            │
            ▼
        production
            │
            ▼
      release/stable         # known-good rollback target
```

## Why This Workflow Exists

Many small teams need more structure than direct commits to `main`, but less complexity than full Git Flow.

This workflow separates concerns:

```text
feature/*        = development history
staging/*        = release validation
main/master      = clean history
tag/branch state = verified release state
release/stable   = known-good rollback target
```

## Stable Baseline Before Release

This is the most important operational rule.

```text
main/master is stable production
        ↓
create/reset release/stable from main/master
        ↓
merge approved new features into main/master
        ↓
tag or use updated main/master as release state
        ↓
test environment quick check
        ↓
production
        ↓
after production stabilization, move release/stable to the new tag/state
```

`release/stable` must not be recreated from an unproven release.

## Why Staging Is Not Merged Into Main / Master

If staging were merged into `main` / `master`, production history could include merge commits, QA fixes, rejected feature traces, and integration noise.

Instead:

```text
feature/* -> staging/vX.Y   # validation
feature/* -> main/master    # approved production promotion
```

This keeps production history clean.

## Final Verification Protects Against Drift

Because staging is not merged into `main` / `master`, the final release state must be checked.

Safe sequence:

```text
1. Validate feature set on staging/vX.Y
2. Promote approved features into main/master
3. Tag main/master when tags are used for releases
4. Deploy the new release tag, or updated main/master, to test environment
5. Quick smoke/regression check that release state
6. Deploy the same verified release state to production
7. Move release/stable only after production stabilization
8. Reset or recreate staging for the next release cycle when needed
```

This ensures production receives the same release state that was tested after promotion.

## Rejected Features Are Removed By Rebuilding Staging

Preferred approach:

```text
1. Recreate staging/vX.Y from main/master
2. Merge only approved or still-planned features
3. Leave rejected feature out
4. Force-push staging/vX.Y with lease
```

This works because staging is temporary.

## One Commit Per Feature Is Default, Not Dogma

Default rule:

```text
one completed feature -> one production commit
```

But large or risky features may be split when it is safer.

Example:

```text
feat(payment): add payment database schema
feat(payment): implement payment API
feat(payment): add checkout UI
```

## Final Summary

This workflow gives each branch one job:

```text
feature/*        develops features
staging/*        validates release candidates
main/master      records approved production functionality
tag/branch state identifies verified release state
release/stable   protects rollback
```

Core result:

- flexible development
- controlled QA/UAT
- clean production commits
- final release-state verification
- safe rollback path
- simple release traceability

---

## License

Documentation and workflow descriptions are licensed under **CC BY-SA 4.0**.  
Source code, scripts, and automation tools are licensed under the **MIT License**.
