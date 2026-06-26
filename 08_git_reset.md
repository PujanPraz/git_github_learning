# ⏪ Git Reset — Complete Notes

> **Level:** Intermediate | **Topic:** Undoing commits, the 3 modes, reset vs revert, recovering lost work

---

## 1. What is Git Reset?

`git reset` moves your branch pointer (HEAD) backward to an earlier commit. It's the main way to **undo commits**. The tricky part: it comes in 3 modes that decide what happens to your files and staging area.

### 🧒 Simple Analogy
> Imagine your commits are pages in a notebook stapled together. `git reset` rips out the staple at a certain page and says "pretend everything after this page never happened." But what happens to the torn-out pages? That depends on which mode you use!

### ⚠️ Important Warning
> `git reset` rewrites history. Like rebase, **never use it on commits that have already been pushed and pulled by teammates** — only use it on your own local, unpushed work.

---

## 2. The Three Modes — Overview

```
  git reset --soft     git reset --mixed (default)    git reset --hard
  ────────────────     ────────────────────────────    ─────────────────
  Moves HEAD only       Moves HEAD + clears staging    Moves HEAD +
                                                        clears staging +
                                                        WIPES your files
```

| Mode | Working Directory | Staging Area | Danger Level |
|------|-------------------|---------------|--------------|
| `--soft` | Untouched ✅ | Untouched ✅ (still staged) | Safe |
| `--mixed` (default) | Untouched ✅ | Cleared 🔄 (back to "modified") | Safe |
| `--hard` | **WIPED** ⚠️ | **WIPED** ⚠️ | Dangerous! |

---

## 3. Visual: Starting Point

Let's say you have 4 commits and want to undo the last one (c4 — "Add broken feature"):

```
BEFORE:
[c1]──[c2]──[c3]──[c4] ← HEAD, main
```

```bash
git reset --soft HEAD~1
# or
git reset --mixed HEAD~1   (or just: git reset HEAD~1)
# or
git reset --hard HEAD~1
```

```
AFTER (all three modes):
[c1]──[c2]──[c3] ← HEAD, main   (c4's commit is gone from history)
```

**The difference is what happens to c4's actual code changes:**

---

## 4. Mode 1: `--soft`

```bash
git reset --soft HEAD~1
```

**What happens:** c4's code changes are still sitting in your **staging area** — ready to re-commit with a better message or split into multiple commits.

```
  Working Directory          Staging Area              Git History
  ──────────────────         ─────────────              ───────────
  (unchanged)                 c4's changes               [c1]──[c2]──[c3] ← HEAD
                              still staged ✅            (c4 removed from history)
```

**When to use:** "I committed too early — let me add more changes before committing again."

```bash
git reset --soft HEAD~1
# ... make more edits ...
git add .
git commit -m "Better, more complete commit"
```

---

## 5. Mode 2: `--mixed` (the default)

```bash
git reset --mixed HEAD~1
# --mixed is the default, so this also works:
git reset HEAD~1
```

**What happens:** c4's code changes are back in your **working directory** as unstaged edits — you can review them, fix them, then re-add and re-commit.

```
  Working Directory          Staging Area              Git History
  ──────────────────         ─────────────              ───────────
  c4's changes are            cleared 🔄                [c1]──[c2]──[c3] ← HEAD
  here as "modified"          (empty)                   (c4 removed from history)
```

**When to use:** "I want to uncommit AND unstage, but keep my edits to review them."

```bash
git reset HEAD~1
# files are back to "modified", review them
git status
```

---

## 6. Mode 3: `--hard` ⚠️

```bash
git reset --hard HEAD~1
```

**What happens:** c4's code changes are **GONE**. Your files now look exactly like they did at commit c3. This cannot be undone with reset alone.

```
  Working Directory          Staging Area              Git History
  ──────────────────         ─────────────              ───────────
  WIPED ⚠️                    WIPED ⚠️                   [c1]──[c2]──[c3] ← HEAD
  (matches commit c3)        (empty)                    (c4 removed from history)
```

**When to use:** "I want to completely throw away the last commit and its changes — I'm sure I don't need any of it."

```bash
git reset --hard HEAD~1
```

> 🚨 **Always run `git status` before `--hard` to double check what you're about to lose.**

---

## 7. Other Common Uses

### Unstage a single file (no commit undone)
```bash
git reset HEAD index.html

# Modern alternative syntax (Git 2.23+)
git restore --staged index.html
```

```
  BEFORE                          AFTER
  ──────                          ─────
  Staging: index.html ✅ staged   Staging: (empty)
                                  Working: index.html ← back to "modified"
```

### Reset to a specific commit (not just N commits back)
```bash
# Using a commit hash
git reset --hard a3f2c1b

# Using relative references
git reset --hard HEAD~3    # go back 3 commits
git reset --hard HEAD^     # go back 1 commit (same as HEAD~1)
```

---

## 8. git reset vs git revert

These are easy to confuse but do very different things:

```
  git reset                              git revert
  ──────────                             ───────────
  Erases commits from history.           Creates a NEW commit that undoes
  Rewrites the timeline.                 a previous one. History stays intact.

  [c1]──[c2]──[c3]                       [c1]──[c2]──[c3]──[c4]──[c5: "Revert c4"]
  (c4 erased completely)                 (c4 still visible, c5 cancels it out)

  🚫 NEVER use on shared/pushed          ✅ SAFE for shared/pushed branches
     branches
```

```bash
# Revert a specific commit (safe — adds a new commit, doesn't erase history)
git revert a3f2c1b
```

**Expected output:**
```
[main 9f3a2c1] Revert "Add broken feature"
 1 file changed, 5 deletions(-)
```

> 💡 **Rule of thumb:** Use `reset` for your own local mess-ups (before pushing). Use `revert` for anything already pushed and shared with others.

---

## 9. The Safety Net: git reflog

Made a mistake with `git reset --hard` and think your work is gone forever? Don't panic yet!

### 🧒 Simple Analogy
> `git reflog` is like a security camera that records every move your HEAD has made — even "deleted" commits are still floating around in Git's memory for a while. You can rewind the footage and find your lost work.

```bash
# Show a log of everywhere HEAD has been recently
git reflog
```

**Expected output:**
```
a3f2c1b HEAD@{0}: reset: moving to HEAD~1
9e812da HEAD@{1}: commit: Add broken feature      ← your "lost" commit!
f0c3a22 HEAD@{2}: commit: Add navigation bar
1b2d4e5 HEAD@{3}: commit: First commit
```

**Recover the lost commit:**
```bash
git reset --hard 9e812da
```

```
  Recovery flow:
  ───────────────
  1. git reset --hard HEAD~1   →  c4 "disappears" (but isn't really gone)
  2. git reflog                →  find c4's hash (9e812da) in the log
  3. git reset --hard 9e812da  →  jump back to c4 — fully recovered!
```

> 💡 Commits are not deleted immediately — they hang around for about 30 days before Git's garbage collection removes them for good. `git reflog` is your safety net during that window.

---

## 10. Full Decision Guide

```
  "I want to undo my last commit..."
  ────────────────────────────────────

  ...but keep the changes staged, ready to re-commit
      → git reset --soft HEAD~1

  ...but keep the changes as unstaged edits to review
      → git reset HEAD~1  (or --mixed, the default)

  ...and throw the changes away completely
      → git reset --hard HEAD~1

  "I already pushed this commit and others have pulled it..."
  ──────────────────────────────────────────────────────────────
      → DON'T use reset! Use: git revert <commit-hash>

  "I think I deleted something important with --hard..."
  ─────────────────────────────────────────────────────────
      → git reflog   (find the lost commit hash)
      → git reset --hard <found-hash>
```

---

## 11. Quick Reference

| Command | What it does |
|---------|--------------|
| `git reset --soft HEAD~1` | Undo commit, keep changes staged |
| `git reset HEAD~1` | Undo commit + unstage (default mixed mode) |
| `git reset --hard HEAD~1` | Undo commit + delete changes ⚠️ |
| `git reset HEAD file.txt` | Unstage one specific file |
| `git restore --staged file.txt` | Modern way to unstage one file |
| `git reflog` | See HEAD history, recover "lost" commits |
| `git revert <hash>` | Safe undo for PUSHED/shared commits |

---

## 12. Interview Questions

---

### Q1: What is the difference between `git reset --soft`, `--mixed`, and `--hard`?
**A:** All three move the HEAD pointer back to an earlier commit, but they differ in what happens to your files. `--soft` keeps changes staged. `--mixed` (the default) unstages changes but keeps them in your working directory. `--hard` deletes the changes completely from both the staging area and working directory.

---

### Q2: What is the difference between `git reset` and `git revert`?
**A:** `git reset` moves the branch pointer backward and erases commits from history — it rewrites the timeline, so it should only be used on local, unpushed commits. `git revert` creates a brand new commit that undoes the changes of a previous commit, leaving history intact — this makes it safe to use on commits that have already been pushed and shared with others.

---

### Q3: You ran `git reset --hard` by mistake and lost a commit. How do you recover it?
**A:** Run `git reflog` to see a log of every place HEAD has recently pointed to, including the "lost" commit. Find its commit hash in the log, then run `git reset --hard <hash>` to jump back to it. Commits aren't deleted immediately — they stay recoverable for about 30 days before garbage collection.

---

### Q4: How do you unstage a single file without undoing any commits?
**A:** Use `git reset HEAD filename` or the more modern `git restore --staged filename`. Both remove the file from the staging area while leaving your actual edits in the working directory untouched.

---

### Q5: Why is it dangerous to use `git reset` on a branch that's already been pushed?
**A:** Because `git reset` rewrites commit history. If teammates have already pulled the commits you're erasing, their local history will diverge from yours, causing confusing errors when they try to push or pull. For shared branches, `git revert` is the safe alternative since it adds new history instead of erasing old history.

---

### Q6: What does `HEAD~1` mean in a reset command?
**A:** `HEAD~1` refers to the commit one step before the current HEAD — i.e., the parent of your latest commit. `HEAD~2` would be two commits back, and so on. You can also use a specific commit hash instead, like `git reset --hard a3f2c1b`.

---

### Q7: If you want to completely discard uncommitted changes (not even committed yet), is `git reset` the right command?
**A:** Not exactly — `git reset` is for undoing commits. To discard uncommitted changes in your working directory, use `git checkout -- filename` (older syntax) or `git restore filename` (modern syntax). `git reset --hard` will also wipe uncommitted changes as a side effect, but it's typically used when you also want to move HEAD to a different commit.

---

*📌 Previous: [07 - Pull Requests](./07-pull-requests.md)*
