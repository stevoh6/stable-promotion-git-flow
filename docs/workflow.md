---
layout: page
title: Operational Workflow
permalink: /workflow/
---

# Stable Promotion Git Flow

**Subtitle:** A simple Git workflow for validated releases, clean history, and stable rollback.  
**Tagline:** Validate first. Promote cleanly. Roll back safely.

**Author:** stevoh6  
**Version:** 2026.06.23

AI-assisted editing was used solely to improve the clarity and readability of this document. The underlying process model, workflow design, decisions, and release strategy were developed independently by the author and are based on real-world practice and experience.

## Purpose

This repository uses **Stable Promotion Git Flow**, a simple Git workflow for solo developers and small teams that need:

- clean history
- controlled release validation
- one production commit per feature by default
- simple rollback and release traceability
- branch-based deployment support

Core principle:

> Staging validates the release candidate. `main` / `master` documents approved production functionality. `release/stable` protects production rollback.

[Design Rationale]({{ '/explained/' | relative_url }})

---

## Branch Model

```text
feature/* ──► staging/vX.Y ──► QA/UAT validation
    │
    └──────► main/master ──► tag or updated branch ──► test environment ──► production ──► release/stable
```

Important rule:

> `staging/*` is never merged into `main` / `master`. Approved feature branches are promoted into `main` / `master` directly, usually as squashed commits.

---

## Branch Types

### `main` / `master`

Production history branch.

Characteristics:

- protected branch
- stable production branch before release promotion
- always deployable
- contains only approved production features
- receives release tags
- uses clean, business-readable commits
- one commit per feature by default

Example history:

```text
feat(login): implement authentication
feat(profile): add profile management
feat(payment): integrate payment gateway
```

### `release/stable`

Stable production recovery branch.

Purpose:

```text
main/master      = current production or release promotion line
release/stable   = last proven stable rollback
```

Characteristics:

- always deployable
- used as rapid rollback target
- never used for development
- never used for QA
- never used for feature integration
- never receives direct development commits
- updated only by explicit release operations

Before every new release cycle, create or reset this branch from the current proven `main` / `master` commit. This creates the rollback baseline before new release commits enter production history.

After the new release has operated successfully in production, move `release/stable` forward to the new proven release tag.

### `staging/<release>`

Temporary release candidate branch.

Examples:

```text
staging/v1.4
staging/v1.5
staging/v2.0
```

Characteristics:

- created from `main` / `master`
- exists only for one release cycle
- combines features planned for the release
- used for QA, UAT, smoke tests, and integration testing
- deleted, reset, or recreated after the release cycle
- never merged into `main` / `master`

Purpose:

```text
feature/* -> staging/vX.Y
```

`staging/*` validates the planned release content, not the final production history.

### `feature/*`

Feature development branch.

Characteristics:

- created from `main` / `master`
- contains development commits
- receives review fixes and QA fixes
- is merged repeatedly into `staging/*` during validation
- is promoted into `main` / `master` only after approval
- is usually squashed before production promotion

---

## Full Release Flow

```text
1. main/master is stable production
   │
   ├──► create/reset release/stable from main/master
   ├──► create staging/vX.Y from main/master
   └──► create feature/* branches from main/master

2. feature/*
   │
   ├──► development commits
   ├──► review fixes
   └──► QA fixes

3. staging/vX.Y
   │
   ├──► merge planned features
   ├──► QA / UAT / integration validation
   └──► approve or reject features

4. main/master
   │
   ├──► promote only approved features
   ├──► squash feature history by default
   └──► create release tag when tags are used

5. test environment
   │
   └──► deploy new release tag or updated main/master and quick-check it

6. production
   │
   └──► deploy same verified release state

7. release/stable
   │
   └──► move forward only after production stabilization

8. staging/vX.Y
   │
   └──► reset, recreate, or delete for the next release cycle when needed
```

---

# Commands

The examples below use `main`. If the repository still uses `master`, replace `main` with `master` consistently.

## 1. Prepare New Release Cycle

Before adding new release features, `main` / `master` must still represent the currently proven production state.

At that moment, always create or reset `release/stable` from `main` / `master`. This makes `release/stable` a clean rollback baseline before new release commits enter production history.

```bash
git checkout main
git pull --ff-only

git branch -f release/stable main
git push --force-with-lease origin release/stable
```

Then create a staging branch from the same production history.

```bash
git checkout main
git pull --ff-only
git checkout -b staging/v1.4
git push -u origin staging/v1.4
```

> Do not reset `release/stable` from `main` / `master` after new, unproven release commits are already there. After that point, move `release/stable` only after production stabilization.

## 2. Create Feature Branch

```bash
git checkout main
git pull --ff-only
git checkout -b feature/login
git push -u origin feature/login
```

## 3. Merge Feature Into Staging

```bash
git checkout staging/v1.4
git pull --ff-only
git merge --no-ff feature/login
git push origin staging/v1.4
```

## 4. Add QA Fixes

```bash
git checkout feature/login
# make fixes
git add .
git commit -m "fix(login): handle session timeout"
git push origin feature/login

git checkout staging/v1.4
git pull --ff-only
git merge --no-ff feature/login
git push origin staging/v1.4
```

## 5. Remove Rejected Feature From Staging

If a feature is rejected after it was already merged into `staging/*`, recreate the staging branch from `main` / `master` and merge only approved or still-planned features again.

```bash
git checkout main
git pull --ff-only

git branch -D staging/v1.4
git checkout -b staging/v1.4

git merge --no-ff feature/login
git merge --no-ff feature/profile
# Do not merge feature/payment

git push --force-with-lease origin staging/v1.4
```

## 6. Promote Approved Feature To Main / Master

Default approach: squash merge.

```bash
git checkout main
git pull --ff-only
git merge --squash feature/login
git commit -m "feat(login): implement authentication"
git push origin main
```

## 7. Large Feature Exception

One commit per feature is the default, not a hard rule.

Split a large feature into multiple production commits when separate commits make rollback, review, or deployment safer.

Example:

```text
feat(payment): add payment database schema
feat(payment): implement payment API
feat(payment): add checkout UI
```

Rule:

> Prefer one production commit per business feature. Split only when multiple logical production commits reduce operational risk.

## 8. Final Release Verification

Because `staging/*` is not merged into `main` / `master`, the final `main` / `master` state must be verified before production.

Preferred tag-based flow:

```text
main/master -> v1.4.0 -> test environment -> production
```

Branch-based CI/CD alternative:

```text
updated main/master -> test environment -> production
```

Rule:

> Production must receive the same release state that passed the test environment check. Do not test one state and deploy another.

## 9. Tag Release From Main / Master

```bash
git checkout main
git pull --ff-only
git tag -a v1.4.0 -m "Release v1.4.0"
git push origin v1.4.0
```

If your CI/CD workflow deploys branches instead of tags, deploy the updated `main` / `master` state that contains the same release commits.

## 10. Deploy To Production

Deploy the same verified release state that passed the test environment.

Preferred:

```text
v1.4.0 -> test environment -> production
```

Branch-based deployment alternative:

```text
updated main/master -> test environment -> production
```

## 11. Stabilization Period

After production deployment, keep `release/stable` pointing to the previous proven release until the new release has operated successfully.

```text
main             -> v1.4.0
release/stable   -> v1.3.0
```

## 12. Promote Release To Stable

After the stabilization period, move `release/stable` to the new proven release.

```bash
git fetch origin --tags
git branch -f release/stable v1.4.0
git push --force-with-lease origin release/stable
```

## 13. Close Or Reset Release Cycle

Delete completed staging branch:

```bash
git checkout main
git branch -d staging/v1.4
git push origin --delete staging/v1.4
```

Create next staging branch from current stable production branch:

```bash
git checkout main
git pull --ff-only
git checkout -b staging/v1.5
git push -u origin staging/v1.5
```

---

# Workflow Rules

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

---

# Summary

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

Result:

- flexible development
- clean history
- controlled release validation
- final release-state verification in test environment
- simple rejected-feature recovery
- predictable rollback target
- easy auditing and release traceability

---

## License

Documentation and workflow descriptions are licensed under **CC BY-SA 4.0**.  
Source code, scripts, and automation tools are licensed under the **MIT License**.
