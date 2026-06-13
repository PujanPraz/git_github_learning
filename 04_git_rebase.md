# 🔀 Git Rebase — Complete Notes

> **Level:** Intermediate | **Topic:** Rebase vs merge, how rebase works, golden rule

---

## 1. What is Rebase?

Rebase **picks up your branch's commits and replays them on top of another branch**. Instead of creating a merge commit, it rewrites history so your work looks like it was always built on the latest code.

### 🧒 Simple Analogy
> You wrote an essay last Monday. Your teacher changed the rules on Wednesday. Rebase is like rewriting your essay as if you knew the new rules from the very start — same ideas, just built on the new foundation.

---

## 2. The Problem Rebase Solves

After many merges, your history looks like a tangled mess:

```
  MESSY HISTORY (after many merges)
  ───────────────────────────────────

  main: ──●──●──M──●──●──M──●──●──M──●──►
               │        │        │
          ┌────┘   ┌────┘   ┌────┘
          ●──●     ●──●     ●──●
         feat1    feat2    feat3

  M = merge commit (extra noise!)
  Hard to read "what actually happened?"


  CLEAN HISTORY (after rebase + merge)
  ──────────────────────────────────────

  main: ──●──●──●──●──●──●──●──●──●──►
                 feat1   feat2  feat3

  One straight line — easy to read!
```

---

## 3. Merge vs Rebase — Side by Side

### Scenario: You branched from main at C3. Main got a new commit C6. You have C4, C5.

```
  STARTING POINT (both cases same):
  ───────────────────────────────────
  [C1]──[C2]──[C3]──[C6]           ← main (new commit C6 arrived)
                  \
                   [C4]──[C5]      ← your feature branch


  AFTER git merge main:             AFTER git rebase main:
  ─────────────────────             ──────────────────────
  [C1]──[C2]──[C3]──[C6]──[M]      [C1]──[C2]──[C3]──[C6]──[C4']──[C5']
                  \        /
                   [C4]──[C5]       C4' and C5' = your commits, replayed
                                    on top of C6 (new IDs, same changes)
          M = merge commit
          (extra, noisy)            No merge commit! Clean line.
```

---

## 4. Merge vs Rebase — Comparison Table

| Feature | `git merge` | `git rebase` |
|---------|-------------|--------------|
| History shape | Branching (realistic) | Linear (clean) |
| Extra commit | Yes — merge commit | No |
| Rewrites commits | No | Yes (new commit IDs) |
| Safety | Always safe | Careful with shared branches |
| Best for | Merging finished features into main | Updating your private feature branch |
| Command | `git merge feature` | `git rebase main` |

---

## 5. How to Use Rebase

**Common workflow:** You're on your feature branch and want to update it with latest commits from main.

```bash
# Step 1: Make sure main is up to date
git checkout main
git pull

# Step 2: Go back to your feature branch
git checkout my-feature

# Step 3: Rebase your work on top of latest main
git rebase main
```

**Expected output (no conflicts):**
```
Successfully rebased and updated refs/heads/my-feature.
```

---

## 6. How Rebase Replays Commits Step by Step

```
  git rebase main — what Git does internally:
  ─────────────────────────────────────────────

  Step 1: Save your commits to a temp area
          ┌────────────────────┐
          │  temp: [C4], [C5]  │  ← your commits saved here
          └────────────────────┘

  Step 2: Move your branch pointer to latest main
          [C1]──[C2]──[C3]──[C6]  ← my-feature now points here

  Step 3: Replay C4 on top of C6
          [C1]──[C2]──[C3]──[C6]──[C4']

  Step 4: Replay C5 on top of C4'
          [C1]──[C2]──[C3]──[C6]──[C4']──[C5']  ✅ Done!

  Note: C4' and C5' have NEW commit IDs but SAME code changes
```

---

## 7. Handling Conflicts During Rebase

If a conflict occurs while replaying a commit:

```bash
# Git stops and shows:
CONFLICT (content): Merge conflict in app.js
error: could not apply a3f2c1b... Add login form
hint: Resolve all conflicts manually, then run:
hint:         git rebase --continue

# Step 1: Fix the conflict in the file (same as merge conflict)
# (edit the file, remove <<<, ===, >>> markers)

# Step 2: Stage the fixed file
git add app.js

# Step 3: Continue replaying the remaining commits
git rebase --continue

# OR — cancel everything and go back to before rebase
git rebase --abort

# OR — skip this commit entirely (rare)
git rebase --skip
```

### The rebase conflict loop:

```
  git rebase main
       │
       ▼
  Replaying C4... ──► conflict? ──YES──► fix file ──► git add ──► git rebase --continue
       │                                                                      │
       │◄─────────────────────────────────────────────────────────────────────┘
       ▼
  Replaying C5... ──► conflict? ──NO──► auto-applied ──► Done! ✅
```

---

## 8. The Golden Rule of Rebase 🚨

```
  ╔══════════════════════════════════════════════════════════════╗
  ║                                                              ║
  ║   NEVER rebase a branch that other people are using!        ║
  ║                                                              ║
  ╚══════════════════════════════════════════════════════════════╝

  WHY? Rebase rewrites commit IDs. If teammates pulled your old
  commits, their copy now conflicts with your rewritten history.

  SAFE ✅                          DANGEROUS 🚫
  ──────────────────               ────────────────────────────
  Rebase your own                  Rebase main branch
  private feature branch

  Rebase before opening            Rebase any branch teammates
  a Pull Request                   have already pulled

  Rebase to update your            Force-push after rebase to
  local branch                     a shared branch without warning
```

### Visual of what goes wrong:

```
  You rebase and push --force:
  ────────────────────────────
  GitHub:  [C1]──[C2]──[C4']──[C5']   ← your new rewritten history

  Teammate's local (old):
  ──────────────────────
  local:   [C1]──[C2]──[C4]──[C5]     ← old commit IDs

  When teammate does git pull:
  ────────────────────────────
  💥 "Your branch and 'origin/feature' have diverged"
  Massive confusion. Bad day for everyone.
```

---

## 9. After Rebasing — Push with Force

Because rebase rewrites history, if you've already pushed the branch, a normal push will be rejected. You need to force push:

```bash
# After rebasing your private feature branch
git push --force-with-lease origin my-feature

# --force-with-lease is SAFER than --force
# It fails if someone else pushed while you were rebasing
```

> ⚠️ Never use `--force` on main. Only `--force-with-lease` on your own feature branches.

---

## 10. Interactive Rebase (Cleaning Up History)

Interactive rebase lets you **edit, squash, reorder, or delete commits** before merging. Very useful for cleaning up a messy branch before opening a PR.

```bash
# Interactively edit the last 3 commits
git rebase -i HEAD~3
```

**This opens your editor showing:**
```
pick a3f2c1b Add login form
pick 9e812da Fix typo in label
pick f0c3a22 Fix another typo

# Commands:
# pick   (p) = use this commit as-is
# reword (r) = use commit but change the message
# squash (s) = combine this into the previous commit
# drop   (d) = delete this commit entirely
```

### Example — Squash 3 commits into 1:

```
  BEFORE (messy):                     AFTER squash (clean):
  ───────────────                     ────────────────────
  [Add login form]                    [Add login form with validation]
  [Fix typo in label]      ──────►    (one single clean commit)
  [Fix another typo]

  Change to:
  squash a3f2c1b Add login form
  squash 9e812da Fix typo in label
  squash f0c3a22 Fix another typo
```

---

## 11. Interview Questions

---

### Q1: What is the difference between `git merge` and `git rebase`?
**A:** `git merge` joins two branches by creating a new merge commit, preserving the full history of when each branch existed. `git rebase` moves your branch's commits on top of another branch, rewriting history for a cleaner, linear result. Merge is safer for shared branches; rebase is better for cleaning up private branches before merging.

---

### Q2: What is the golden rule of rebase?
**A:** Never rebase a branch that other people are using. Since rebase rewrites commit IDs, it will cause conflicts and confusion for anyone who has already pulled those commits. Only rebase your own private branches that nobody else has pulled from.

---

### Q3: What does `git rebase --abort` do?
**A:** It cancels an in-progress rebase and restores your branch to exactly the state it was in before you started the rebase. It's the safe escape hatch if rebase goes wrong or the conflicts are too complex to handle right now.

---

### Q4: When would you use interactive rebase (`git rebase -i`)?
**A:** When you want to clean up your commit history before merging or submitting a pull request. Common uses: squashing 10 "WIP" commits into one clean commit, rewording unclear commit messages, or dropping commits that were mistakes. It makes your PR history much easier to review.

---

### Q5: Why do you need `--force` when pushing after a rebase?
**A:** Because rebase rewrites commits (they get new IDs), the remote branch and your local branch now have different histories. A normal `git push` is rejected because Git thinks you're behind the remote. You need `git push --force-with-lease` to overwrite the remote with your rebased history.

---

### Q6: What is `--force-with-lease` and why is it better than `--force`?
**A:** `--force-with-lease` is a safer version of `--force`. It pushes successfully only if nobody else has pushed to the remote branch since you last pulled. If someone else has pushed, it fails with an error, protecting their work. `--force` blindly overwrites regardless.

---

### Q7: Does rebase change your actual code?
**A:** No. Rebase only changes the **position** in history where your commits sit. The actual file changes inside each commit remain identical. Only the parent commit pointer (and therefore the commit ID) changes.

---

*📌 Previous: [03 - Merge Conflicts](./03-merge-conflicts.md) | Next: [05 - Git Stash](./05-git-stash.md)*
