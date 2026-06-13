# 🌿 Branches & Merging — Complete Notes

> **Level:** Beginner | **Topic:** Creating branches, switching, merging

---

## 1. What is a Branch?

A branch is an **independent line of development**. When you create a branch, you get your own copy of the project to work in safely — the main branch stays untouched while you experiment.

### 🧒 Simple Analogy
> Imagine your project is a tree. The main trunk is always stable. When you want to try something new, you grow a new branch. If you like the result, attach it to the trunk. If not, cut off the branch — the trunk is perfectly fine!

---

## 2. Why Use Branches?

```
WITHOUT branches (dangerous ❌)        WITH branches (safe ✅)
──────────────────────────────         ──────────────────────
Everyone edits main directly           Each person gets their own branch

main: ──●──●──●──●──●──●──►           main: ──●──●──────────────────●──►
         ▲  ▲  ▲  ▲  ▲  ▲                              ▲            ▲
         │  │  │  │  │  │                              │            │
    Alice Bob Alice Bob ← conflicts!               ┌──●──●──┐     merge
                                                   │ Alice  │
                                               ┌──●──●──┐  │
                                               │  Bob   │◄─┘
                                               └────────┘
```

---

## 3. Branch Commands

```bash
# List all local branches (* = current branch)
git branch

# Create a new branch
git branch feature-login

# Switch to a branch
git checkout feature-login

# Create AND switch in one command (preferred ✅)
git checkout -b feature-login

# Modern way (Git 2.23+)
git switch -c feature-login

# Delete a branch (safe — only if already merged)
git branch -d feature-login

# Force delete (even if NOT merged)
git branch -D feature-login

# Rename current branch
git branch -m new-name

# List ALL branches including remote
git branch -a
```

**Expected output of `git branch`:**
```
* main              ← * means you are currently here
  feature-login
  bugfix-header
```

---

## 4. How Branches Work Internally

```
  Start: both point to same commit
  ─────────────────────────────────
  [c1]──[c2]──[c3]
                ▲
                │
          main & feature
          (same place)


  After 2 commits on feature branch:
  ────────────────────────────────────
  [c1]──[c2]──[c3]──────────────────────►  main (no new commits)
                  \
                   [c4]──[c5]              feature-login
                              ▲
                             HEAD
                       (you are here)


  After 1 new commit on main too:
  ────────────────────────────────
  [c1]──[c2]──[c3]──[c6]                  main
                  \
                   [c4]──[c5]             feature-login
```

---

## 5. What is Merging?

Merging brings the changes from one branch **into** another. When your feature is complete, you merge it into main so everyone gets your changes.

```bash
# Step 1: Switch to the branch you want to merge INTO
git checkout main

# Step 2: Merge your feature branch into main
git merge feature-login
```

---

## 6. Types of Merges

### Type 1: Fast-Forward Merge

Happens when main has **no new commits** since you branched. Git simply moves the pointer forward — no extra commit created.

```
BEFORE merge:
  [c1]──[c2]──[c3]                   ← main (no new commits)
                  \
                   [c4]──[c5]        ← feature-login

AFTER git merge feature-login (fast-forward):
  [c1]──[c2]──[c3]──[c4]──[c5]      ← main moved forward
                                ▲
                               main
                           (clean! no extra commit)
```

**Expected output:**
```
Updating f0c3a22..a3f2c1b
Fast-forward
 login.html | 25 +++++++++++++++++++++++++
 1 file changed, 25 insertions(+)
```

---

### Type 2: 3-Way Merge (Merge Commit)

Happens when **both** main and feature have new commits. Git creates a special merge commit to join them.

```
BEFORE merge:
  [c1]──[c2]──[c3]──[c6]            ← main (has new commit c6)
                  \
                   [c4]──[c5]       ← feature-login

AFTER git merge feature-login:
  [c1]──[c2]──[c3]──[c6]──────[M]  ← M = merge commit
                  \           /
                   [c4]──[c5]
                                ▲
                               main
```

**Expected output:**
```
Merge made by the 'ort' strategy.
 login.html | 25 +++++++++++++++++++++++++
 1 file changed, 25 insertions(+)
```

---

## 7. Merge Conflicts

A merge conflict happens when **two branches change the exact same line** of the same file.

```
Alice changes line 2:          Bob changes line 2:
─────────────────────          ───────────────────
"Today is wonderful."          "Today is amazing."
        │                              │
        └──────────── CONFLICT ────────┘
                    Git can't decide!
```

**What Git puts in your file:**
```
Hello, world!
<<<<<<< HEAD (your branch)
Today is a wonderful day.
=======
Today is an amazing day.
>>>>>>> feature-login
Written by: someone
```

**How to resolve:**
```
Step 1: Open the file, delete the markers, keep what you want

Step 2: git add the fixed file
        git add greeting.txt

Step 3: Complete the merge
        git commit -m "Resolve merge conflict in greeting.txt"
```

---

## 8. Full Branch Lifecycle

```
  1. Create branch          2. Do work              3. Merge back
  ─────────────────         ──────────              ──────────────

  git checkout -b           git add .               git checkout main
  feature-login             git commit -m           git merge feature-login
                            "Add login"             git branch -d feature-login

  main: ──●──────────────────────────────────────●──►
               \                                /
                ●──────────●──────────●─────────
                (c4)       (c5)       (c6)
                  feature-login branch
```

---

## 9. Useful Branch Commands Summary

| Command | What it does |
|---------|-------------|
| `git branch` | List all local branches |
| `git checkout -b name` | Create + switch to new branch |
| `git checkout main` | Switch to main branch |
| `git merge branch-name` | Merge a branch into current branch |
| `git branch -d name` | Delete merged branch |
| `git branch -D name` | Force delete branch |
| `git log --oneline --graph` | Visual history of all branches |

---

## 10. Interview Questions

---

### Q1: What is a Git branch and why is it useful?
**A:** A branch is an independent copy of the codebase where you can work without affecting the main code. It's useful for developing features, fixing bugs, and experimenting safely. Once done, you merge it back. This means main always stays stable and deployable.

---

### Q2: What is the difference between `git checkout` and `git switch`?
**A:** Both switch branches. `git switch` is newer (Git 2.23+) and only handles branch switching — it's clearer and less confusing. `git checkout` is older and can also restore files, which made it confusing for beginners. Both work, but `git switch` is the modern recommended way.

---

### Q3: What is a merge conflict and when does it occur?
**A:** A merge conflict occurs when two branches have changed the **same line** in the same file. Git cannot automatically decide which version to keep, so it marks the file with `<<<`, `===`, `>>>` markers and asks the developer to manually pick the correct version.

---

### Q4: What is a fast-forward merge?
**A:** A fast-forward merge happens when the target branch (e.g. main) has **no new commits** since the feature branch was created. Git simply moves the main pointer forward to include the feature commits — no extra merge commit is created. The history looks perfectly linear.

---

### Q5: How do you delete a branch safely?
**A:** Use `git branch -d branch-name`. The lowercase `-d` only deletes if the branch has already been merged, preventing accidental data loss. Use `-D` (uppercase) to force-delete even if unmerged — but be careful, unmerged work will be lost.

---

### Q6: Can you merge a branch into itself?
**A:** No, that doesn't make sense and Git will throw an error. You merge one branch **into** another: switch to the target branch first (`git checkout main`), then pull in the source (`git merge feature-branch`).

---

### Q7: What is `git branch -a` used for?
**A:** It lists **all** branches — both local branches on your machine and remote branches on GitHub. Useful for seeing what branches teammates have pushed to the remote that you haven't yet fetched locally.

---

*📌 Previous: [01 - Git Basics](./01-git-basics.md) | Next: [03 - Merge Conflicts](./03-merge-conflicts.md)*
