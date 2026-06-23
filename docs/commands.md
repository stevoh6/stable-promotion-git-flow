---
layout: page
title: Commands Cheatsheet
permalink: /commands/
---

# Stable Promotion Git Flow Command Cheatsheet

This cheatsheet uses `main`. If your repository still uses `master`, replace `main` with `master` consistently.

## Create Or Reset Stable Rollback Target

Run this every release cycle before adding new release features. At this moment, `main` / `master` must still be the currently proven production state.

```bash
git checkout main
git pull --ff-only

git branch -f release/stable main
git push --force-with-lease origin release/stable
```

## Prepare Release Staging Branch

```bash
git checkout main
git pull --ff-only
git checkout -b staging/v1.4
git push -u origin staging/v1.4
```

## Create Feature Branch

```bash
git checkout main
git pull --ff-only
git checkout -b feature/login
git push -u origin feature/login
```

## Merge Feature Into Staging

```bash
git checkout staging/v1.4
git pull --ff-only
git merge --no-ff feature/login
git push origin staging/v1.4
```

## Remove Rejected Feature From Staging

```bash
git fetch origin
git checkout main
git pull --ff-only

git branch -D staging/v1.4 2>/dev/null || true
git checkout -b staging/v1.4

git merge --no-ff feature/login
git merge --no-ff feature/profile
# Do not merge rejected feature/payment.

git push --force-with-lease origin staging/v1.4
```

## Promote Approved Feature To Main As One Commit

```bash
git checkout main
git pull --ff-only
git merge --squash feature/login
git commit -m "feat(login): implement authentication"
git push origin main
```

## Tag Release From Main

```bash
git checkout main
git pull --ff-only
git tag -a v1.4.0 -m "Release v1.4.0"
git push origin v1.4.0
```

Deploy this release tag to the test environment. If your CI/CD uses branch deployments, deploy the updated `main` / `master` state instead. If it passes quick smoke/regression checks, deploy the same verified release state to production.

## Move Stable Branch After Production Stabilization

```bash
git fetch origin --tags
git branch -f release/stable v1.4.0
git push --force-with-lease origin release/stable
```

## Reset Or Recreate Staging For Next Release

Delete completed staging branch:

```bash
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

## License

Documentation and workflow descriptions are licensed under **CC BY-SA 4.0**.  
Source code, scripts, and automation tools are licensed under the **MIT License**.
