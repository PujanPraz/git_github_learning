# 🔍 Pull Requests — Complete Notes

> **Level:** Intermediate | **Topic:** Code review workflow, opening and merging PRs on GitHub

---

## 1. What is a Pull Request?

A **Pull Request (PR)** is a way of saying: *"I finished some work on my branch — can someone review it before we add it to main?"*

It's a **GitHub feature** (not a Git command) that opens a conversation around your code changes before they are merged.

### 🧒 Simple Analogy
> You wrote an essay and want to hand it in. But first you give it to a friend: "Can you read this before I give it to the teacher?" Your friend reads it, leaves some notes, you fix them, then the teacher gets a polished final version. That whole process is a pull request!

> 💡 On **GitLab** it's called a **"Merge Request (MR)"**. On **Bitbucket** it's also called a **"Pull Request"**. Same concept, different name.

---

## 2. PR vs Direct Merge — Why Bother?

```
  WITHOUT Pull Requests:               WITH Pull Requests:
  ──────────────────────               ──────────────────
  Dev writes code                      Dev writes code
         │                                    │
         ▼                                    ▼
  git merge → main ← immediately      Opens a Pull Request
                                              │
  Nobody reviews it!                          ▼
  Bugs go straight to main 🐛         Teammates review code
                                              │
                                             bugs caught!
                                              │
                                             fixes made
                                              │
                                              ▼
                                       Approved → Merge
                                       main stays clean ✅
```

---

## 3. Why Teams Use Pull Requests

```
  ✅  Catch bugs before they reach main
      A second pair of eyes spots mistakes you missed

  📚  Learn from each other
      Reviewers suggest better approaches — you get smarter each PR

  📝  Written record of decisions
      The PR discussion explains WHY code was written a certain way
      Future you in 6 months will be so grateful

  🔒  Quality gates
      Teams require 1 or 2 approvals before any merge

  🤖  Automated testing
      CI/CD tools (GitHub Actions) run tests on every PR automatically
```

---

## 4. The Complete PR Workflow

```
  ┌─────────────────────────────────────────────────────────────────┐
  │                    FULL PR LIFECYCLE                            │
  │                                                                 │
  │  1. Branch    2. Code     3. Push      4. Open PR              │
  │     ───────      ────        ────         ─────────            │
  │  git checkout   write     git push     GitHub UI:              │
  │  -b feature     code      origin       Compare &               │
  │                            feature     pull request            │
  │       │            │          │              │                 │
  │       ▼            ▼          ▼              ▼                 │
  │  5. Review    6. Fix      7. Approve   8. Merge                │
  │     ──────       ────        ──────       ─────                │
  │  Teammate     push more    teammate     GitHub:                 │
  │  comments     commits      clicks       Merge PR               │
  │  on code      to fix       Approve      button                 │
  │               (same                          │                 │
  │               branch)                        ▼                 │
  │                                         9. Clean up            │
  │                                            ────────            │
  │                                         git pull main          │
  │                                         git branch -d feature  │
  └─────────────────────────────────────────────────────────────────┘
```

---

## 5. Step-by-Step With Commands

### Step 1 — Create a branch
```bash
git checkout -b add-login-page
# Now write your code...
```

### Step 2 — Commit your changes
```bash
git add .
git commit -m "Add login page with email and password form"
```

### Step 3 — Push your branch to GitHub
```bash
git push -u origin add-login-page
```

**Expected output:**
```
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
remote: Create a pull request for 'add-login-page' on GitHub by visiting:
remote:      https://github.com/you/project/pull/new/add-login-page
To https://github.com/you/project.git
 * [new branch]      add-login-page -> add-login-page
```

### Step 4 — Open PR on GitHub
```
Go to GitHub → your repo
Yellow banner appears: "add-login-page had recent pushes"
Click: "Compare & pull request"
Fill in title + description → Click "Create pull request"
```

### Step 5 — Respond to review comments
```bash
# After fixing what reviewer asked:
git add .
git commit -m "Add forgot password link (reviewer suggestion)"
git push     # automatically updates the open PR!
```

### Step 6 — After approval, merge on GitHub
```
Click "Merge pull request" → "Confirm merge"
```

### Step 7 — Clean up locally
```bash
# Update your local main
git checkout main
git pull

# Delete the now-merged branch
git branch -d add-login-page
```

---

## 6. What a PR Looks Like on GitHub

```
  ┌───────────────────────────────────────────────────────────────┐
  │  [Open] Add login page with email and password form  #47     │
  ├───────────────────────────────────────────────────────────────┤
  │  👩 alice wants to merge 3 commits into main from             │
  │     add-login-page                                            │
  │                                                               │
  │  ┌─────────────────────────────────────────────────────────┐  │
  │  │ Description:                                            │  │
  │  │ Added a login page at /login.                           │  │
  │  │ Includes email + password, basic validation.            │  │
  │  │ Closes #42.                                             │  │
  │  └─────────────────────────────────────────────────────────┘  │
  │                                                               │
  │  Files changed (3)   Commits (3)   Checks (✅ passing)       │
  │                                                               │
  │  login.html                                                   │
  │  ┌─────────────────────────────────────┐                     │
  │  │ + <form class="login-form">         │  ← green = added    │
  │  │ +   <input type="email">            │                     │
  │  │ +   <input type="password">         │                     │
  │  │ +   <button>Log in</button>         │                     │
  │  │ + </form>                           │                     │
  │  └─────────────────────────────────────┘                     │
  │                                                               │
  │  💬 Bob: Can we add a "Forgot password?" link?               │
  │  💬 Alice: Great idea! Just pushed a new commit ✅           │
  │                                                               │
  │  ✅ Bob approved these changes                               │
  │                                                               │
  │  [ Merge pull request ▼ ]                                    │
  └───────────────────────────────────────────────────────────────┘
```

---

## 7. Three Merge Options on GitHub

```
  ┌─────────────────────────────────────────────────────────────────────┐
  │                       MERGE OPTIONS                                 │
  ├─────────────────┬───────────────────────────────────────────────────┤
  │  MERGE COMMIT   │  History: ──●──●──●──M──►                        │
  │  (default)      │                       │                           │
  │                 │           All commits + merge commit              │
  │                 │           Full history preserved                  │
  ├─────────────────┼───────────────────────────────────────────────────┤
  │  SQUASH &       │  History: ──●──●──●──●──►                        │
  │  MERGE          │                          ▲                        │
  │  (popular ⭐)   │           All PR commits squashed into ONE        │
  │                 │           Clean main history                      │
  ├─────────────────┼───────────────────────────────────────────────────┤
  │  REBASE &       │  History: ──●──●──[A']──[B']──[C']──►            │
  │  MERGE          │                                                   │
  │                 │           Commits replayed on main, no merge      │
  │                 │           commit, linear history                  │
  └─────────────────┴───────────────────────────────────────────────────┘

  Beginner? Just use the default Merge Commit. ✅
  Most teams? Use Squash & Merge for cleaner history.
```

---

## 8. How to Write a Great PR Description

```markdown
## What changed
Added a login page at /login with email + password form,
basic validation, and link to the signup page.

## Why
Part of the user authentication feature.
Closes #42.

## How to test
1. Run the app locally (npm start)
2. Visit http://localhost:3000/login
3. Try logging in with valid and invalid credentials
4. Check the error messages appear correctly

## Screenshots
[paste before/after screenshots here for UI changes]
```

---

## 9. PR Best Practices

```
  ✅  DO THIS                          ❌  AVOID THIS
  ──────────────                       ──────────────
  Keep PRs small (< 400 lines)         Giant PRs with 50+ files
  One feature per PR                   Mixing unrelated changes
  Write clear description              "fixed stuff" as description
  Test before opening                  Submitting broken code
  Respond to all comments              Ignoring review feedback
  Add screenshots for UI               Making reviewers guess the UI
  Link related issues "Closes #42"     No context for the change
  Request specific reviewers           Opening to everyone vaguely
```

---

## 10. Draft Pull Requests

When your work isn't ready for review but you want visibility or early feedback:

```
  DRAFT PR:
  ─────────
  Title shows: [Draft] Add login page
  Status:      Draft (cannot be accidentally merged)
  Purpose:     "Work in progress — don't merge yet, but please look"


  How to open a draft PR:
  ──────────────────────
  GitHub: Click the ▼ arrow next to "Create pull request"
         → Select "Create draft pull request"


  When ready for real review:
  ──────────────────────────
  Click "Ready for review" button on the PR page
  Status changes from Draft → Open
  Reviewers are notified
```

---

## 11. Code Review Etiquette

### As the Author:
```
  ✅ Keep PRs small and focused
  ✅ Explain your decisions in the description
  ✅ Respond to every comment — even just "Fixed!" or "Good point!"
  ✅ Don't take feedback personally — it's about the code, not you
  ✅ Thank reviewers for their time
```

### As the Reviewer:
```
  ✅ Be specific: "I'd suggest X because Y" not just "this is wrong"
  ✅ Distinguish blocking vs non-blocking: "Nit:" = optional suggestion
  ✅ Approve when you're happy — don't leave people hanging
  ✅ Acknowledge good work too: "This is a really elegant solution!"
  ✅ Review promptly — blocked PRs block people's work
```

---

## 12. Full Command Reference

```bash
# Start your feature
git checkout -b feature-name

# Do your work, commit
git add .
git commit -m "Meaningful message"

# Push to GitHub
git push -u origin feature-name

# After PR is merged — clean up
git checkout main
git pull
git branch -d feature-name

# If you need to update your branch with latest main
# (while PR is open and main got new commits)
git checkout feature-name
git rebase main     # or: git merge main
git push --force-with-lease origin feature-name
```

---

## 13. Interview Questions

---

### Q1: What is a Pull Request and what is it used for?
**A:** A Pull Request is a GitHub feature (not a Git command) that lets you propose merging your branch into another branch (usually main). It creates a space for code review — teammates can read your changes, leave comments, request changes, and approve before the merge happens. It's the standard way teams collaborate on code without breaking the main branch.

---

### Q2: What is the difference between a Pull Request and `git merge`?
**A:** `git merge` is a local Git command that immediately combines branches — no review, no discussion. A Pull Request is a collaborative review process on GitHub that wraps the merge in a conversation. A PR eventually results in a merge, but only after review and approval. PRs are a workflow; merge is a command.

---

### Q3: What are the three merge options on a GitHub Pull Request?
**A:** **Merge commit** — keeps all commits and adds a merge commit, preserving full history. **Squash and merge** — combines all PR commits into one single commit on main, giving a cleaner history (most popular). **Rebase and merge** — replays your commits on top of main one by one, no merge commit, completely linear history.

---

### Q4: What should a good PR description include?
**A:** What changed and a brief explanation of the changes. Why — the motivation or business reason. How to test the changes manually. A link to the related issue (e.g. "Closes #42"). Screenshots if there are UI changes. A good description helps reviewers understand the context without reading every line of code.

---

### Q5: What is a Draft Pull Request?
**A:** A Draft PR signals that the work is still in progress and not ready for review or merging. It gives visibility to the team about what you're working on and allows early feedback without the risk of accidental merge. When ready, you click "Ready for review" to convert it to a regular open PR.

---

### Q6: What happens to the PR if you push more commits to the branch after opening it?
**A:** The new commits automatically appear in the PR. GitHub updates the PR in real time whenever you push to the same branch. This is how you respond to reviewer feedback — fix the issue, push the fix, and the PR updates without you needing to close and reopen it.

---

### Q7: Why should PRs be kept small?
**A:** Small PRs (under 400 lines) are reviewed faster, have fewer bugs, are easier to understand, and are less likely to cause merge conflicts. Studies show the quality of code review drops sharply after 400+ lines — reviewers start rubber-stamping rather than genuinely reviewing. One feature = one PR is the golden rule.

---

*📌 Previous: [06 - .gitignore](./06-gitignore.md) | Start over: [01 - Git Basics](./01-git-basics.md)*

---

## 🎓 You've completed all 7 Git notes!

```
  Topics covered:
  ───────────────
  01 ✅ Git Basics         — init, add, commit, status, log
  02 ✅ Branches & Merging — branch, checkout, merge
  03 ✅ Merge Conflicts    — resolve, abort, prevent
  04 ✅ Git Rebase         — rebase vs merge, golden rule
  05 ✅ Git Stash          — stash, pop, apply
  06 ✅ .gitignore         — patterns, security, the trap
  07 ✅ Pull Requests      — full PR workflow, best practices

  Next things to learn:
  ─────────────────────
  → git reset (undo mistakes)
  → git reflog (recover lost commits)
  → GitHub Actions (automate testing on PRs)
  → git cherry-pick (copy a commit from another branch)
```
