# 🗄️ Git Stash — Complete Notes

> **Level:** Beginner-Intermediate | **Topic:** Saving work temporarily without committing

---

## 1. What is Git Stash?

`git stash` temporarily saves your uncommitted changes and gives you a **clean working directory** — without creating a commit. You can restore your saved changes any time later.

### 🧒 Simple Analogy
> You're halfway through building a LEGO castle on your desk. Your mum asks you to clear the desk RIGHT NOW for dinner. Stash is a magic drawer — you sweep all your LEGO pieces into it, eat dinner, then pull them back out and continue exactly where you left off!

---

## 2. The Problem Stash Solves

```
  THE SITUATION:
  ──────────────
  You are on feature branch, halfway through work...

  ┌─────────────────────────────────┐
  │  feature-login branch          │
  │                                │
  │  login.html ← half written     │
  │  style.css  ← broken CSS       │
  │                                │
  │  NOT ready to commit! ❌       │
  └─────────────────────────────────┘

  Suddenly: "URGENT BUG on main — fix it NOW!"

  Problem: Git won't let you switch branches with messy uncommitted changes
  ────────────────────────────────────────────────────────────────────────
  $ git checkout main
  error: Your local changes to the following files would be overwritten:
          login.html
  Please commit your changes or stash them before you switch branches.

  Solution: git stash 🎉
```

---

## 3. How Stash Works — Visual

```
  BEFORE git stash:                    AFTER git stash:
  ─────────────────                    ────────────────

  Your Desk (working dir)              Your Desk (working dir)
  ┌─────────────────────┐              ┌─────────────────────┐
  │ login.html ✏️ messy │              │ login.html ✅ clean  │
  │ style.css  ✏️ broken│   git stash  │ style.css  ✅ clean  │
  │                     │ ──────────► │                     │
  └─────────────────────┘              └─────────────────────┘

  Stash Drawer (empty)                 Stash Drawer (full!)
  ┌─────────────────────┐              ┌──────────────────────────────┐
  │                     │              │ stash@{0}: half-done login   │
  │   (nothing here)    │              │   login.html 💾              │
  │                     │              │   style.css  💾              │
  └─────────────────────┘              └──────────────────────────────┘

  Now you can freely switch branches! ✅
```

---

## 4. Core Stash Commands

```bash
# Save all uncommitted changes to the stash
git stash

# Save with a descriptive label (RECOMMENDED — always do this!)
git stash push -m "half-done login feature"

# Restore the most recent stash AND remove it from list
git stash pop

# Restore a stash but KEEP it in the list
git stash apply

# See all saved stashes
git stash list

# Delete one specific stash
git stash drop stash@{1}

# Delete ALL stashes at once
git stash clear
```

---

## 5. Expected Outputs

**After `git stash`:**
```
Saved working directory and index state WIP on main: a3f2c1b Add login page
```

**After `git stash list`:**
```
stash@{0}: On feature-login: half-done login feature   ← newest (on top)
stash@{1}: On main: navbar colour experiment
stash@{2}: WIP on main: f0c3a22 Add navigation bar    ← oldest
```

**After `git stash pop`:**
```
On branch feature-login
Changes not staged for commit:
        modified:   login.html
        modified:   style.css

Dropped stash@{0} (a1b2c3d...e5f6)    ← automatically deleted from list
```

---

## 6. The Full Rescue Workflow

```
  1. You're mid-feature        2. Stash your work         3. Fix the bug
  ─────────────────────        ──────────────────         ────────────────
  (messy changes)              git stash push             git checkout main
                               -m "login WIP"             # fix the bug
                                                          git add .
                               Your desk is clean ✅      git commit -m "Fix bug"
                                                          git push


  4. Come back to your work    5. Restore your stash      6. Continue working
  ─────────────────────────    ─────────────────────      ────────────────────
  git checkout feature-login   git stash pop              # back to exactly
                                                          # where you left off!
                               Your messy files are       login.html ✏️
                               back on your desk ✅       style.css  ✏️
```

---

## 7. `pop` vs `apply` — Key Difference

```
  git stash pop                    git stash apply
  ─────────────                    ───────────────

  Stash Drawer          Desk       Stash Drawer          Desk
  ┌──────────┐                     ┌──────────┐
  │ stash{0} │ ──restore──►  ✏️    │ stash{0} │ ──copy──►  ✏️
  │          │                     │          │
  └──────────┘ ← DELETED           └──────────┘ ← KEPT
                                   (stash stays!)

  Use pop when: Done with it.      Use apply when: Want to apply
  Won't need it again.             same changes to multiple branches.
```

---

## 8. Stash Stack — Multiple Stashes

Stash works like a stack of drawers. Newest is always on top (`stash@{0}`):

```
  STASH STACK
  ───────────
  ┌──────────────────────────────────────┐  ← stash@{0} NEWEST
  │  "half-done login feature"           │  (git stash pop gets this one)
  ├──────────────────────────────────────┤
  │  "navbar colour experiment"          │  ← stash@{1}
  ├──────────────────────────────────────┤
  │  "WIP footer redesign"               │  ← stash@{2} OLDEST
  └──────────────────────────────────────┘
```

```bash
# Apply a specific stash (not just the top one)
git stash apply stash@{1}

# Delete a specific stash
git stash drop stash@{2}
```

---

## 9. Stashing Untracked Files

By default, `git stash` only saves tracked files (files Git already knows about):

```bash
# Include NEW untracked files (not yet git add-ed)
git stash -u
# or
git stash --include-untracked

# Include untracked AND ignored files (e.g. .env)
git stash -a
# or
git stash --all
```

```
  Default git stash:           git stash -u:
  ──────────────────           ─────────────
  login.html ✏️  ──► SAVED    login.html ✏️  ──► SAVED
  style.css  ✏️  ──► SAVED    style.css  ✏️  ──► SAVED
  newfile.js ❓  ──► IGNORED  newfile.js ❓  ──► SAVED ✅
  (untracked)                 (untracked)
```

---

## 10. Move Stashed Work to a Different Branch

Stashes are not tied to any branch — you can apply them anywhere:

```bash
# You accidentally started working on main instead of feature branch

git stash                      # save your work
git checkout -b feature-login  # create and switch to the right branch
git stash pop                  # restore your work here instead
```

---

## 11. ⚠️ Important Warning

```
  ┌────────────────────────────────────────────────────┐
  │  ⚠️  Stashes are LOCAL ONLY                        │
  │                                                    │
  │  git push  ──────────────►  GitHub                 │
  │  (commits go to GitHub)                            │
  │                                                    │
  │  git stash ──────────────►  stays on YOUR machine  │
  │  (stashes NEVER go to GitHub)                      │
  │                                                    │
  │  Don't use stash as long-term backup!              │
  │  Always commit important work eventually.          │
  └────────────────────────────────────────────────────┘
```

---

## 12. Quick Reference

| Command | What it does |
|---------|-------------|
| `git stash` | Save all changes, clean the desk |
| `git stash push -m "msg"` | Save with a label (recommended) |
| `git stash list` | See all stashes |
| `git stash pop` | Restore newest + delete from list |
| `git stash apply` | Restore newest + keep in list |
| `git stash apply stash@{2}` | Restore a specific stash |
| `git stash drop stash@{1}` | Delete a specific stash |
| `git stash clear` | Delete ALL stashes |
| `git stash -u` | Include untracked files |

---

## 13. Interview Questions

---

### Q1: What is `git stash` and when would you use it?
**A:** `git stash` temporarily saves your uncommitted changes and cleans your working directory, without making a commit. You use it when you need to urgently switch context — like fixing a bug on another branch — but aren't ready to commit your current half-done work. It's like a clipboard for your in-progress changes.

---

### Q2: What is the difference between `git stash pop` and `git stash apply`?
**A:** Both restore stashed changes to your working directory. `git stash pop` also removes the stash from the list after applying it — use this when you're done with it. `git stash apply` keeps the stash in the list — useful if you want to apply the same changes to multiple branches.

---

### Q3: Can you stash untracked files?
**A:** By default, `git stash` only stashes tracked modified files. To include untracked files (new files not yet `git add`-ed), use `git stash -u`. To include both untracked and ignored files, use `git stash -a`.

---

### Q4: How do you apply a specific stash, not just the most recent one?
**A:** Use `git stash list` to see all stashes and their indices (`stash@{0}`, `stash@{1}`, etc.). Then run `git stash apply stash@{2}` to apply a specific one by its index number.

---

### Q5: Are stashes pushed to GitHub when you do `git push`?
**A:** No. Stashes are completely local. They live only on your machine and are never pushed to any remote repository. This is why stashes should only be used for short-term context switching, not as a backup strategy for important work.

---

### Q6: What happens if you drop a stash by accident?
**A:** Stashes that are dropped are very difficult to recover (unlike commits, which have a safety net called `git reflog`). You can try `git fsck --unreachable` to find dangling objects, but it's not guaranteed. This is why naming your stashes with `-m` is important — it signals they're valuable enough to be careful with.

---

### Q7: What is the difference between stashing and committing?
**A:** A commit is a permanent, shareable snapshot in Git history with a proper message — it's for completed, meaningful chunks of work. A stash is a temporary, local scratch pad for in-progress work you're not ready to commit. You can push commits to GitHub; stashes never leave your machine.

---

*📌 Previous: [04 - Git Rebase](./04-git-rebase.md) | Next: [06 - .gitignore](./06-gitignore.md)*
