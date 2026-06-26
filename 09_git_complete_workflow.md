# 🏁 Complete Git Workflow — A Real Project From Scratch

> **Level:** Capstone | **Topic:** Tying together init, .gitignore, branches, stash, rebase, conflicts, pull requests, and reset into one real workflow

---

## The Scenario

You and your friend Bob are building a small todo-list web app together. This note walks through setting up the repo, building a feature on a branch, getting it reviewed, merging it, and handling a real merge conflict along the way — using **every command from the previous 8 notes**, in the order you'd actually use them on a real project.

```
  Commands used in this note (in order of first appearance):
  ────────────────────────────────────────────────────────────
  git init · .gitignore · git config · git add · git commit ·
  git push · git checkout -b · git stash · git rebase ·
  git rebase -i · Pull Request · git pull (conflict) ·
  Squash and merge · git branch -d · git reset --soft
```

---

## Phase 1 — Setup

### Step 1: Create the project

Initialize Git and immediately set up `.gitignore` — **before** your first commit, so secrets and junk never get tracked in the first place.

```bash
# Create and enter the project folder
mkdir todo-app && cd todo-app

# Initialize Git
git init

# Create .gitignore FIRST, before any commits
echo "node_modules/" >> .gitignore
echo ".env" >> .gitignore
echo ".DS_Store" >> .gitignore

# Configure your identity (if not done globally already)
git config user.name "Alice"
git config user.email "alice@example.com"
```

> 💡 Why .gitignore first? If you commit `.env` before adding it to `.gitignore`, Git keeps tracking it forever (see Note 6 — "The Already Tracked Trap"). Setting it up on day one avoids the problem entirely.

---

### Step 2: First commit

```bash
git add .
git commit -m "Initial commit: project structure"

# Push to GitHub for the first time
git remote add origin https://github.com/alice/todo-app.git
git push -u origin main
```

**Expected output:**
```
[main (root-commit) 1b2d4e5] Initial commit: project structure
 3 files changed, 42 insertions(+)
```

```
  Project state after Phase 1:
  ─────────────────────────────
  main: ──●(1b2d4e5: "Initial commit")
                                ▲
                              HEAD
```

---

## Phase 2 — Build the Feature

### Step 3: Start a feature branch

You're building the "add todo item" feature. **Never work directly on main** — create a branch.

```bash
git checkout -b add-todo-feature
```

```
  main: ──●(1b2d4e5)──────────────────────►
                    \
                     ●  ← add-todo-feature (you are here)
```

You start writing the HTML form and JS logic for adding a todo item...

---

### Step 4: Urgent interruption — stash!

> 😱 **Scenario:** Halfway through your feature, Bob messages: *"The homepage title has a typo in production — can you fix it RIGHT NOW?"* Your todo feature isn't ready to commit.

```bash
# Save your half-done work without committing
git stash push -m "WIP: add todo feature"

# Switch to main, fix the urgent typo
git checkout main
# ... fix the typo in index.html ...
git add .
git commit -m "Fix typo in homepage title"
git push

# Go back to your feature branch and restore your work
git checkout add-todo-feature
git stash pop
```

You're back exactly where you left off — but **main now has a new commit ahead of where you originally branched**. This matters later.

---

### Step 5: Finish the feature

```bash
git add .
git commit -m "Add todo input form"

# ... more work ...
git add .
git commit -m "Add delete todo button"

# ... fix a small bug ...
git add .
git commit -m "Fix typo in button label"
```

```
  main: ──●──────────●(typo fix)─────────►
                    \
                     ●──●──●  ← add-todo-feature (3 commits)
```

---

### Step 6: Clean up history with rebase

Before opening a PR, update your branch with main's typo fix AND clean up that messy "Fix typo in button label" commit by squashing it into the previous one. **This is your own private branch — safe to rebase.**

```bash
# Step 1: bring in main's latest commit (the typo fix)
git fetch origin
git rebase origin/main

# Step 2: squash messy commits interactively
git rebase -i HEAD~3
# mark "Fix typo in button label" as "squash" in the editor
```

```
  BEFORE:  ●(form)──●(delete btn)──●(typo fix)
  AFTER:   ●(form)──●(delete button, with typo fix squashed in)
```

> 💡 This is exactly the golden rule from Note 4 in action: rebase is only safe here because nobody else has pulled this branch yet.

---

## Phase 3 — Review

### Step 7: Open a Pull Request

Since you rebased, you need `--force-with-lease` to push (only safe because nobody else has this branch):

```bash
git push --force-with-lease origin add-todo-feature
```

Then on GitHub:
```
Click "Compare & pull request"
Title: "Add todo item feature"
Description: explains what + why + how to test
Click "Create pull request"
```

> 💬 **Bob reviews:** "Looks great! Can you also clear the input field after adding a todo?"

```bash
# Fix the requested change, push again
git add .
git commit -m "Clear input field after adding todo"
git push
# PR updates automatically — Bob approves ✅
```

---

### Step 8: Merge — but a conflict appears!

> ⚠️ **While your PR was open:** Bob merged his own PR that also edited the same line in `style.css`. Now your PR shows a conflict.

```bash
# Update your branch with the latest main
git checkout add-todo-feature
git pull origin main
# CONFLICT (content): Merge conflict in style.css
```

**Git shows the conflict markers in style.css:**
```css
<<<<<<< HEAD
.button { color: blue; }
=======
.button { color: green; }
>>>>>>> main
```

```bash
# You decide blue fits the design better — resolve it
# (edit the file, remove markers, keep blue)
git add style.css
git commit -m "Resolve conflict — keep blue button"
git push
```

> 💡 This is the exact resolution flow from Note 3 — open the file, choose a version, delete the markers, `add` + `commit`.

---

### Step 9: Merge the Pull Request

Conflict resolved, checks pass, Bob re-approves. Time to merge on GitHub:

```
On GitHub: click "Squash and merge"
All your feature commits become ONE clean commit on main
```

```
  main: ──●──●(typo)──────────────────────●(Add todo feature, squashed)
                    \                                    /
                     ●──●──●──●  (your branch — now merged in)
```

main now has your complete, working todo feature as one tidy commit.

> 💡 Why squash here? Your branch had several small, messy commits ("Add form", "Add delete button", "Clear input field", "Resolve conflict"). Squashing turns all of that into one clean, meaningful entry in main's history — exactly the use case described in Note 7.

---

## Phase 4 — Cleanup

### Step 10: Clean up

Every good workflow ends the same way — sync up and tidy your branches.

```bash
# Switch back to main and pull the merged changes
git checkout main
git pull

# Delete your now-merged feature branch locally
git branch -d add-todo-feature

# (GitHub usually offers to delete the remote branch too)
```

Clean slate — ready for the next feature!

---

### Step 11: Bonus — undo a mistake

> 😬 **One more thing:** You start a new branch and accidentally commit a `console.log` debug line straight to it before realizing.

```bash
# Oops — undo the last commit, keep the changes staged
git reset --soft HEAD~1

# remove the console.log line
git add .
git commit -m "Add search feature"
```

Because this was a **local, unpushed** commit on your own branch, `reset --soft` was perfectly safe to use — exactly the safe-use case from Note 8.

---

## The Full Picture — Every Command, In Order

```
  SETUP
  ─────
  git init → .gitignore → git config → git add → git commit → git push

  BUILD
  ─────
  git checkout -b → git stash push → git stash pop →
  git commit (x3) → git fetch → git rebase → git rebase -i

  REVIEW
  ──────
  git push --force-with-lease → [Pull Request] →
  git pull (conflict!) → resolve conflict → git add → git commit →
  [Squash and merge]

  CLEANUP
  ───────
  git checkout main → git pull → git branch -d → git reset --soft
```

---

## Why Each Decision Was Made

| Moment | What happened | Why this tool was the right choice |
|--------|---------------|-------------------------------------|
| Day 1 | Created `.gitignore` before first commit | Prevents secrets/junk from ever being tracked (Note 6) |
| Mid-feature interruption | Used `git stash` instead of a messy commit | Keeps history clean — no "WIP please ignore" commits (Note 5) |
| Before opening PR | Used `git rebase`, not `git merge` | Branch was private and unpushed — safe to rewrite history for a clean PR (Note 4) |
| PR open, main moved ahead | Hit a real merge conflict on `git pull` | Two people edited the same line — normal, expected, resolved manually (Note 3) |
| Final merge | Used "Squash and merge" not "Merge commit" | Many small messy commits → one clean entry in main's history (Note 7) |
| After accidental local commit | Used `git reset --soft`, not `--hard` | Wanted to keep the changes, just undo the commit wrapper, and it was never pushed (Note 8) |

---

## Interview Questions

---

### Q1: Walk me through your typical Git workflow when building a new feature.
**A:** Start by pulling the latest main, then create a feature branch with `git checkout -b feature-name`. Make small, focused commits as I work. If I need to update my branch with the latest main before opening a PR, I rebase (since it's still my private branch). I push, open a Pull Request, respond to review comments by pushing more commits, and once approved, merge — usually with squash-and-merge to keep main's history clean. Finally, I delete the merged branch and pull main locally.

---

### Q2: Why would you use `git stash` instead of just committing unfinished work?
**A:** Committing unfinished or broken code pollutes the history with meaningless "WIP" commits and risks accidentally pushing broken code if you forget to clean it up later. `git stash` lets you save the work-in-progress without a commit, switch context for something urgent, and restore it exactly later with `git stash pop` — no permanent record, no risk.

---

### Q3: In this workflow, why was it safe to rebase before opening the PR, but not after?
**A:** Before opening the PR (or pushing), the branch is entirely private — no one else has pulled those commits, so rewriting history with rebase causes no harm. After pushing and especially after others (like reviewers or CI) have referenced specific commits, rebasing would rewrite shared history and cause confusing errors for anyone who already has the old commits. The rule of thumb: rebase before sharing, merge or careful force-push after.

---

### Q4: Why did the merge conflict happen even though you were the only one editing your file?
**A:** Merge conflicts depend on what both branches change, not just what you personally touch. In this case, Bob's separate PR also modified `style.css` on the same line, and his PR merged into main while yours was still open. When you pulled the updated main into your branch, Git found two different versions of the same line and couldn't auto-merge them.

---

### Q5: What's the benefit of squashing commits before merging a Pull Request?
**A:** Squashing combines all the small, often messy commits in a feature branch (typo fixes, WIP commits, review feedback fixes) into a single clean commit on main. This makes main's history much easier to read and `git log` much more useful — each line represents one complete feature or fix, not a developer's stream-of-consciousness process.

---

### Q6: At the very end, why was `git reset --soft` the right choice instead of `--hard` or `git revert`?
**A:** The mistake (a stray `console.log`) was in a commit that had never been pushed — it only existed locally. `--soft` undoes the commit but keeps the changes staged, letting you quickly remove the debug line and recommit. `--hard` would have been overkill since it would also delete the actual code changes, not just the commit wrapper. `git revert` is for already-pushed, shared commits — unnecessary here since nothing had been shared yet.

---

### Q7: What would you have done differently if Bob had already pulled your `add-todo-feature` branch before you rebased it?
**A:** I wouldn't have rebased at all. Once a branch is shared and others have it locally, rewriting its history with rebase causes their copies to diverge and creates confusing merge errors. In that case, I would either leave the messy commits as-is, or use `git merge` to bring in main's changes instead of rebase — preserving history rather than rewriting it.

---

*📌 This note ties together: [01 - Git Basics](./01-git-basics.md), [02 - Branches & Merging](./02-branches-merging.md), [03 - Merge Conflicts](./03-merge-conflicts.md), [04 - Git Rebase](./04-git-rebase.md), [05 - Git Stash](./05-git-stash.md), [06 - .gitignore](./06-gitignore.md), [07 - Pull Requests](./07-pull-requests.md), [08 - Git Reset](./08-git-reset.md)*

---

## 🎓 You've completed the full Git curriculum!

```
  01 ✅ Git Basics           06 ✅ .gitignore
  02 ✅ Branches & Merging   07 ✅ Pull Requests
  03 ✅ Merge Conflicts      08 ✅ Git Reset
  04 ✅ Git Rebase           09 ✅ Complete Workflow (this note)
  05 ✅ Git Stash

  You now understand the entire loop a professional developer
  uses every single day — from a fresh idea to shipped, reviewed code.
```
