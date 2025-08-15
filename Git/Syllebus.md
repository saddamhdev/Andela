Awesome—let’s knock out Git first. Here’s a tight, practical path to “job-ready” Git in a week, plus a cheat sheet, mini-labs, and interview-style questions.

# What you’ll be able to do

* Use Git confidently for solo + team work (branching, PRs, code reviews).
* Recover from mistakes (revert/reset/reflog), handle conflicts, and keep a clean history (rebase, squash).
* Ship with a standard GitHub flow.

---

# 7‑Day Sprint Plan (2–3 hrs/day)

**Day 1 – Foundations**

* Install/setup: `git config --global user.name "Your Name"`; `user.email`; `core.autocrlf input` (mac/linux) or `true` (windows).
* Concepts: repo, commit, HEAD, working tree, index.
* Practice: `git init`, `status`, `add`, `commit`, `.gitignore`.
* Mini-lab: Create a repo; add README; ignore `target/`, `.env`.

**Day 2 – Branching & Merging**

* `git branch`, `switch`/`checkout`, `merge`, fast-forward vs 3‑way merges.
* Conflicts: resolve + `git add` + `git commit`.
* Mini-lab: Branch `feature/login`, change a file on both `main` and feature, merge, resolve conflict.

**Day 3 – Remote & Collaboration**

* `git remote add origin`, `push -u`, `pull`, `fetch`, tracking branches.
* GitHub Flow: feature branch → PR → review → merge → delete branch.
* Mini-lab: Create a private GitHub repo, push local repo, open a PR from `feature/ui`.

**Day 4 – Undo & Recovery**

* `git restore` (files), `git reset` (index/HEAD), `git revert` (safe undo), `git reflog` (time machine).
* When to use which:

  * Public history: **revert**.
  * Local WIP: **reset** (soft/mixed/hard).
* Mini-lab: Make 3 commits; `reset --soft` to previous; amend message with `git commit --amend`.

**Day 5 – Rebase & Clean History**

* `git rebase main` (update feature), `git rebase -i` (squash, reorder), `--autosquash`.
* Golden rule: don’t rebase shared/public branches.
* Mini-lab: Create messy feature (3 tiny commits); squash to 1 before PR.

**Day 6 – Advanced Tools**

* `stash` (save WIP), `cherry-pick` (port specific commit), `bisect` (find bad commit), tags & releases.
* Mini-lab:

  * `git stash -u`, switch branch, pop.
  * Cherry-pick a bugfix to `release/1.0`.
  * Use `git bisect` on a simple bug (see lab below).

**Day 7 – Team Scenarios & Hooks**

* Handle force-push incidents with `reflog`.
* Pre-commit hooks (lint/tests), conventional commits, semantic versioning basics.
* Mock interview questions + timed PR exercise.

---

# Git Cheat Sheet (most-used)

## Daily driver

```bash
git status
git add -p                 # stage hunks interactively
git commit -m "feat: ..."
git log --oneline --graph --decorate --all
git diff                   # unstaged
git diff --staged          # staged
```

## Branching & merging

```bash
git switch -c feature/x    # new branch
git merge main             # merge main into current
git rebase main            # rewrite feature on top of main (local-only)
git push -u origin feature/x
```

## Remotes

```bash
git remote -v
git fetch origin
git pull --rebase          # prefer linear history for local work
git push --force-with-lease  # safe force push for your branch
```

## Undo / fix-ups

```bash
git restore file.java                # discard unstaged changes in file
git restore --staged file.java       # unstage
git commit --amend                   # fix last commit msg/content
git revert <sha>                     # safe undo (public)
git reset --soft <sha>               # keep changes staged
git reset --mixed <sha>              # keep changes unstaged (default)
git reset --hard <sha>               # discard changes (careful)
git reflog                           # recover anything
```

## Stash / cherry-pick / bisect

```bash
git stash -u
git stash pop
git cherry-pick <sha>
git bisect start
git bisect bad
git bisect good <sha_of_known_good>
# run tests until git tells you the first bad commit
git bisect reset
```

## Tags & releases

```bash
git tag -a v1.0.0 -m "First release"
git push origin v1.0.0
```

---

# Real-World Scenarios (with decisions)

1. **Undo a merged PR that broke production (public repo)**

* Use `git revert -m 1 <merge_commit_sha>` (keeps history, safe).

2. **You accidentally committed a secret**

* Rotate secret immediately (outside Git).
* Use `git filter-repo` or BFG to scrub history (private), force-push, and invalidate leaked tokens.

3. **Keep feature branch up-to-date with main**

* Team policy A: `git merge main` regularly (keeps merge commits).
* Team policy B: `git rebase main` before PR (clean linear history). Don’t rebase after sharing.

4. **Bring one bugfix to release branch**

* `git cherry-pick <sha>` to `release/x.y`.

5. **Huge conflict**

* Use `git mergetool` (or IDE), resolve per file, `git add`, `git commit`.

---

# Mini-Labs (hands-on)

## Lab 1: Conflict & Resolution

1. `git init demo && cd demo`
2. Create `app.txt` with “v1”, commit.
3. Make `feature/a`, change line to “feature change”, commit.
4. Switch `main`, change same line to “main change”, commit.
5. Merge `feature/a` → resolve conflict → commit.

## Lab 2: Clean History with Rebase

1. On `feature/b`, make 3 tiny commits (`print 1`, `print 2`, fix typo).
2. `git rebase -i main` → squash to 1 commit with a clear message.
3. Push with `--force-with-lease`.

## Lab 3: Stash & Cherry-pick

1. Edit files without committing → `git stash -u`.
2. Switch branches for a quick hotfix → commit → cherry-pick to `release`.
3. Return and `git stash pop`.

## Lab 4: Bisect (bug hunter)

1. Create 8 commits; introduce a bug (failing test) in commit 5.
2. `git bisect start`, mark `bad` on HEAD and `good` on commit 3.
3. Run your test manually each step (or script) until Git finds first bad commit.

---

# Suggested Workflow for Your Java/Spring Projects

* **Branch naming**: `feat/`, `fix/`, `chore/`, `refactor/`, `docs/`.
* **Commit style** (Conventional Commits):
  `feat(api): add /recipient/update endpoint`
  `fix(ui): handle duplicate modal render`
* **PR rules**:

  * Small PRs (<300 lines).
  * Include summary, screenshots for UI, “How to test”.
  * Rebase onto `main` just before merge, squash if PR has many small commits.

---

# Common Interview Q\&A (quick hits)

* **`git merge` vs `git rebase`?**
  Merge: preserves history with a merge commit; safe for shared branches.
  Rebase: rewrites commits on top of a new base; cleaner history; avoid on shared branches.

* **Recover a deleted branch?**
  `git reflog` → find last commit → `git branch <name> <sha>`.

* **When to use `revert` vs `reset`?**
  Public/shared history: `revert`. Local/unpushed work: `reset`.

* **`--force` vs `--force-with-lease`?**
  Prefer `--force-with-lease`; it refuses to overwrite others’ newer work.

* **What’s HEAD?**
  A pointer to the current commit (and usually to a branch ref).

---

# Helpful `.gitconfig` Aliases (optional)

```ini
[alias]
  st = status -sb
  co = checkout
  sw = switch
  br = branch
  lg = log --oneline --graph --decorate --all
  last = log -1 HEAD
  unstage = restore --staged --
  amend = commit --amend --no-edit
```

---

If you want, I can turn this into a **checklist PDF** and a **30‑question Git quiz** you can use before interviews—or we can jump into a **timed Git mock test** now (conflicts, revert vs reset, rebase, bisect). What would you like next: quiz, mock test, or a live mini-project workflow?
