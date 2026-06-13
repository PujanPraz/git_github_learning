# 📖 Git Basics — Complete Notes

> **Level:** Beginner | **Topic:** What Git is, setup, core commands

---

## 1. What is Git?

Git is a **distributed version control system** that tracks changes in your files over time. It lets you:
- Save snapshots of your work (called **commits**)
- Go back to any previous state
- Collaborate with others without overwriting each other's work

### 🧒 Simple Analogy
> Git is like a magical camera for your code. Every time you take a photo (commit), Git stores it forever. If you mess something up, look at an old photo and go back!

---

## 2. How Git Works — The Big Picture

```
┌─────────────────────────────────────────────────────────────────┐
│                        YOUR PROJECT                             │
│                                                                 │
│   ┌─────────────┐   git add   ┌──────────────┐  git commit    │
│   │  Working    │ ──────────► │   Staging    │ ─────────────► │
│   │  Directory  │             │   Area       │                 │
│   │             │             │  (Index)     │                 │
│   │ (your files │             │              │  ┌───────────┐  │
│   │  on disk)   │             │  Files ready │  │   Git     │  │
│   │             │ ◄────────── │  to be saved │  │  History  │  │
│   └─────────────┘  git restore└──────────────┘  │ (commits) │  │
│                                                  └───────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 3. Initial Setup

Before using Git, configure your identity **once**. Git attaches your name and email to every commit.

```bash
# Check if Git is installed
git --version

# Set your name
git config --global user.name "Your Name"

# Set your email
git config --global user.email "you@example.com"

# Verify your settings
git config --list
```

**Expected output of `git config --list`:**
```
user.name=Your Name
user.email=you@example.com
core.editor=vim
```

> 💡 `--global` means this applies to ALL projects on your computer. You only run this once ever.

---

## 4. `git init` — Start a Repository

Creates a new Git repository in your current folder. Git creates a hidden `.git` folder to store all its data.

```bash
# Navigate to your project folder
cd my-project

# Initialize Git
git init
```

**Expected output:**
```
Initialized empty Git repository in /home/user/my-project/.git/
```

### What happens visually:

```
BEFORE git init          AFTER git init
─────────────────        ──────────────────────
📁 my-project/           📁 my-project/
   📄 index.html            📄 index.html
   🎨 style.css             🎨 style.css
                            📁 .git/  ← Git's brain (hidden)
                               ├── config
                               ├── objects/
                               └── refs/
```

> ⚠️ Never manually edit or delete the `.git` folder — it's Git's entire memory for the project!

---

## 5. `git add` — Stage Your Changes

After editing files, you must **stage** them before committing. Staging means choosing which changed files to include in the next snapshot.

```bash
# Stage a single file
git add index.html

# Stage multiple specific files
git add index.html style.css

# Stage ALL changed files in current directory
git add .

# Stage ALL changes across entire repo
git add -A
```

### The Staging Area explained:

```
  Your Files          Staging Area         Git History
  ──────────          ────────────         ───────────
  index.html ──────►  index.html  ───┐
  (changed)  git add  (ready)        │
                                     │  git commit
  style.css           ╳              │  ──────────►  📷 Snapshot
  (changed)           (NOT staged)   │               saved forever
  not added                          │
                                  ───┘
```

> 💡 `git add .` — the dot means "everything in current directory." Most common thing you'll type!

---

## 6. `git commit` — Save a Snapshot

A commit permanently saves all staged changes with a descriptive message.

```bash
# Commit with a message
git commit -m "Add navigation bar to homepage"

# Stage AND commit tracked files in one step
git commit -am "Fix typo in header"
```

**Expected output:**
```
[main (root-commit) a3f2c1b] Add navigation bar to homepage
 2 files changed, 45 insertions(+)
 create mode 100644 index.html
 create mode 100644 style.css
```

### How commits build up over time:

```
  First commit        Second commit       Third commit
  ────────────        ─────────────       ────────────
  ┌──────────┐        ┌──────────┐        ┌──────────┐
  │ 1b2d4e5  │◄───────│ f0c3a22  │◄───────│ 9e812da  │  ← HEAD
  │          │        │          │        │          │    (you are here)
  │ "First   │        │ "Add nav │        │ "Fix     │
  │  commit" │        │  bar"    │        │  button" │
  └──────────┘        └──────────┘        └──────────┘

  Each commit points back to its parent — this is Git's history chain!
```

> 💡 Write commit messages like a story: **"Add login page"**, **"Fix broken button"**, **"Change text to blue"**. Future you will be grateful!

---

## 7. `git status` — See What's Changed

```bash
# Full status
git status

# Short version
git status -s
```

**Expected output:**
```
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   index.html

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        newfile.js

no changes added to commit (use "git add" and/or "git commit -a")
```

### Reading `git status -s`:

```
 M index.html    ← M = Modified (already tracked, now changed)
?? newfile.js    ← ?? = Untracked (brand new file Git doesn't know yet)
A  staged.html   ← A  = Added to staging area
```

---

## 8. `git log` — View History

```bash
# Full detailed log
git log

# Compact — one line per commit
git log --oneline

# Visual branch graph
git log --oneline --graph --all
```

**Expected output of `git log --oneline`:**
```
9e812da (HEAD -> main) Fix broken login button
f0c3a22 Add navigation bar
a3f2c1b Add homepage styles
1b2d4e5 First commit
```

**Visual timeline:**

```
  Oldest                                              Newest
  ──────                                              ──────
  [1b2d4e5] ──► [a3f2c1b] ──► [f0c3a22] ──► [9e812da]
  "First         "Add           "Add nav      "Fix button"
   commit"        styles"        bar"          ▲
                                               │
                                             HEAD
                                         (where you are now)
```

---

## 9. The Three Areas of Git

```
┌────────────────────┐     ┌────────────────────┐     ┌────────────────────┐
│                    │     │                    │     │                    │
│  Working Directory │     │   Staging Area     │     │    Repository      │
│                    │     │    (Index)         │     │    (.git folder)   │
│  Where you edit    │     │                    │     │                    │
│  files on your     │────►│  Files you've      │────►│  Permanent history │
│  computer          │     │  run git add on    │     │  of all commits    │
│                    │     │                    │     │                    │
│  git restore ◄─────│─────│◄── git restore     │     │                    │
│                    │     │    --staged        │     │                    │
└────────────────────┘     └────────────────────┘     └────────────────────┘
        ▲                                                       │
        └───────────────────── git checkout ───────────────────┘
                              (go back to old commit)
```

| Area | Command to move files IN | What it stores |
|------|--------------------------|----------------|
| Working Directory | _(just edit normally)_ | Your current unsaved edits |
| Staging Area | `git add` | Changes chosen for next commit |
| Repository | `git commit` | Permanent snapshots forever |

---

## 10. Complete Workflow Diagram

```
  1. Edit file         2. Stage it        3. Commit it       4. Push to GitHub
  ────────────         ───────────        ────────────       ─────────────────
  📝 index.html  ──►  git add .   ──►  git commit -m  ──►  git push
  (type code)         (choose what       "your message"      (share online)
                       to save)
```

---

## 11. Interview Questions

---

### Q1: What is Git and why do we use it?
**A:** Git is a distributed version control system. We use it to track changes in code over time, collaborate with teammates without overwriting each other's work, and revert to previous versions if something breaks. It's used by virtually every software company in the world.

---

### Q2: What is the difference between `git add` and `git commit`?
**A:** `git add` stages changes — it puts files in the "waiting room" ready to be saved. `git commit` takes everything in the staging area and permanently saves it as a snapshot with a message. You always need to `add` before you can `commit`.

---

### Q3: What does `git init` do?
**A:** It initialises a new Git repository in the current folder by creating a hidden `.git` directory. This is always the first command when starting a new project with Git. Without it, Git won't track anything.

---

### Q4: What is the difference between `git status` and `git log`?
**A:** `git status` shows the **current state** — what files are changed or staged right now. `git log` shows the **history** of past commits — what was saved previously. Status = present. Log = past.

---

### Q5: What happens if you run `git commit` without `git add` first?
**A:** Nothing gets committed. Git only commits what has been staged. If nothing is staged, Git will say `nothing to commit, working tree clean` (or show what's unstaged but not save it). You must `git add` first.

---

### Q6: What is HEAD in Git?
**A:** HEAD is a pointer that tells Git where you currently are in the commit history. It usually points to the latest commit on your current branch. When you make a new commit, HEAD moves forward automatically.

---

### Q7: What is the difference between `git add .` and `git add -A`?
**A:** `git add .` stages all changes in the current directory and subdirectories. `git add -A` stages all changes across the entire repository, including deletions. In most cases they behave identically when run from the project root.

---

*📌 Next topic: [02 - Branches & Merging](./02-branches-merging.md)*
