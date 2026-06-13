# 💥 Merge Conflicts — Complete Notes

> **Level:** Intermediate | **Topic:** Understanding, resolving, and preventing merge conflicts

---

## 1. What is a Merge Conflict?

A merge conflict happens when **two people (or two branches) change the exact same line** in the same file. Git cannot automatically decide which version to keep — so it stops and asks YOU to decide.

### 🧒 Simple Analogy
> Two friends editing the same Google Doc. Alice changes line 3 to "The sky is blue." Bob changes line 3 to "The sky is purple." When you try to save both versions, Google asks: which one do I keep? That's a merge conflict!

---

## 2. When Conflicts DO and DON'T Happen

```
  CONFLICT ❌ — Same line changed by both branches:
  ─────────────────────────────────────────────────
  File: greeting.txt

  Alice's branch           Bob's branch
  ──────────────           ────────────
  Line 1: Hello            Line 1: Hello
  Line 2: wonderful  ◄──►  Line 2: amazing    ← SAME LINE = CONFLICT!
  Line 3: Goodbye          Line 3: Goodbye


  NO CONFLICT ✅ — Different lines changed:
  ──────────────────────────────────────────
  File: greeting.txt

  Alice's branch           Bob's branch
  ──────────────           ────────────
  Line 1: Hi there!  ◄──► Line 1: Hello       ← different lines
  Line 2: great day        Line 2: great day
  Line 3: Goodbye   ◄───► Line 3: See ya!     ← Git merges BOTH fine
```

> 💡 **Key insight:** Git is NOT broken when a conflict happens. It is protecting you from silently losing someone's work!

---

## 3. The Story: Alice and Bob

```
  ORIGINAL FILE (greeting.txt)         ALICE edits line 2        BOB also edits line 2
  ─────────────────────────────        ──────────────────        ─────────────────────
  Hello, world!                        Hello, world!             Hello, world!
  Today is a great day.       ──►      Today is a wonderful day. Today is an amazing day.
  Written by: someone                  Written by: someone       Written by: someone

                                             │                         │
                                             └──────────┬──────────────┘
                                                        │
                                                        ▼
                                                    CONFLICT!
                                          Git can't auto-pick a winner
```

---

## 4. How to Trigger and See a Conflict

```bash
# You try to merge Bob's branch into yours
git merge bob-branch
```

**Expected output when conflict happens:**
```
Auto-merging greeting.txt
CONFLICT (content): Merge conflict in greeting.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**Check which files are conflicted:**
```bash
git status
```

```
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   greeting.txt
```

---

## 5. What Git Writes Inside Your File

Git rewrites the conflicted file with **conflict markers** showing both versions:

```
Hello, world!
<<<<<<< HEAD
Today is a wonderful day.
=======
Today is an amazing day.
>>>>>>> bob-branch
Written by: someone
```

### Reading the conflict markers:

```
<<<<<<< HEAD
Today is a wonderful day.       ← YOUR version (current branch)
=======                         ← Divider between the two versions
Today is an amazing day.        ← THEIR version (bob-branch)
>>>>>>> bob-branch

⚠️  The <<<, ===, >>> lines are NOT real code!
    You MUST delete ALL of them when done resolving.
```

---

## 6. Your Four Choices to Resolve

```
  OPTION 1: Keep YOUR version (Alice)
  ─────────────────────────────────────
  Hello, world!
  Today is a wonderful day.      ← keep this
  Written by: someone
  (delete the markers + Bob's line)


  OPTION 2: Keep THEIR version (Bob)
  ────────────────────────────────────
  Hello, world!
  Today is an amazing day.       ← keep this
  Written by: someone
  (delete the markers + Alice's line)


  OPTION 3: Keep BOTH lines
  ───────────────────────────
  Hello, world!
  Today is a wonderful day.     ← keep both
  Today is an amazing day.      ← keep both
  Written by: someone
  (delete only the markers)


  OPTION 4: Write something completely new
  ─────────────────────────────────────────
  Hello, world!
  Today is a fantastic day!     ← brand new line
  Written by: someone
  (delete everything — markers AND both old lines)
```

---

## 7. Step-by-Step Resolution

```bash
# Step 1: Git tells you there's a conflict
git merge bob-branch
# CONFLICT (content): Merge conflict in greeting.txt

# Step 2: Find conflicted files
git status
# both modified: greeting.txt

# Step 3: Open the file and fix it
# (edit manually OR use VS Code's built-in conflict resolver)

# Step 4: Stage the fixed file
git add greeting.txt

# Step 5: Complete the merge with a commit
git commit -m "Resolve merge conflict in greeting.txt"
```

**Expected output after commit:**
```
[main a7f3c1b] Resolve merge conflict in greeting.txt
```

---

## 8. Visual: The Full Conflict Resolution Flow

```
  1. Normal work           2. Both edit same line   3. You merge
  ─────────────            ─────────────────────    ──────────────
  main: ──●──●──●          alice: ──●──●──[A]       git merge bob
               \                                    CONFLICT!
                bob: ──●──●──[B]


  4. Git marks your file   5. You fix it            6. You commit the fix
  ────────────────────     ────────────             ────────────────────
  <<<<<<< HEAD             Today is a              git add greeting.txt
  wonderful                wonderful,              git commit -m "Resolved"
  =======                  amazing day!
  amazing                                           main: ──●──●──[A]──[R]
  >>>>>>> bob                                                           ▲
                                                                    [R] = resolve commit
```

---

## 9. Using VS Code to Resolve (Easier!)

VS Code shows a visual interface for conflicts:

```
  ┌─────────────────────────────────────────────────────┐
  │  index.html  ⚠ 1 conflict                           │
  ├─────────────────────────────────────────────────────┤
  │  [Accept Current Change] [Accept Incoming] [Both]   │
  │  ──────────────────────────────────────────────     │
  │  <<<<<<< HEAD (Current Change)                      │
  │  Today is a wonderful day.    ← green highlight     │
  │  =======                                            │
  │  Today is an amazing day.     ← blue highlight      │
  │  >>>>>>> bob-branch (Incoming Change)               │
  └─────────────────────────────────────────────────────┘

  Just click one of the buttons at the top — no manual editing!
```

---

## 10. Abort a Merge (Start Over)

If the conflict is too complex and you want to start fresh:

```bash
git merge --abort
```

**This cancels the merge completely** and restores everything to before you ran `git merge`. Like it never happened.

---

## 11. How to Prevent Conflicts

```
  PREVENTION STRATEGY          WHY IT HELPS
  ───────────────────          ────────────
  🔄 Pull often                Stay in sync → smaller gaps → fewer conflicts
     git pull every morning

  🌿 Keep branches short       Less time to drift apart from main
     Merge within days, not weeks

  🗣️  Communicate with team    "I'm editing the header today" prevents overlap
     Tell people what file you're touching

  📦 Make small commits        Small changes = smaller conflicts when they happen

  👤 Use code ownership        Different people own different parts of codebase
     Alice owns /auth, Bob owns /payment
```

---

## 12. Interview Questions

---

### Q1: What is a merge conflict and how do you resolve it?
**A:** A merge conflict occurs when two branches modify the same line of the same file. Git marks the conflicting section with `<<<`, `===`, `>>>` markers. To resolve: open the file, manually choose which version to keep (or combine them), delete all the markers, then `git add` the file and `git commit`.

---

### Q2: Can you abort a merge? How?
**A:** Yes. Run `git merge --abort`. This cancels the entire merge operation and restores the branch to the state it was in before you started the merge. It's the safe escape hatch when a conflict is too complex to resolve right now.

---

### Q3: How do you find which files have conflicts after a failed merge?
**A:** Run `git status`. Files with conflicts are listed under `both modified:`. You can also search inside files for the string `<<<<<<<` to find exactly which lines are conflicted.

---

### Q4: Does a merge conflict mean Git is broken?
**A:** No. A conflict means Git found two competing changes it cannot automatically reconcile. Git is actually protecting you from silently overwriting someone's work. Conflicts are a normal part of collaborative development — every developer deals with them.

---

### Q5: What are some strategies to avoid merge conflicts?
**A:** Pull frequently to stay in sync with the remote. Keep feature branches small and short-lived (days, not weeks). Communicate with teammates about which files you're editing. Consider code ownership patterns where different people are responsible for different parts of the codebase.

---

### Q6: If you accidentally committed during a conflict resolution without resolving all markers, what happens?
**A:** The conflict markers (`<<<`, `===`, `>>>`) will be left in your file as literal text, which will likely break your application. The commit itself goes through — Git doesn't validate that you removed the markers. You'd need to fix the file manually and make a new commit.

---

### Q7: What is the difference between resolving a conflict in `git merge` vs `git rebase`?
**A:** The resolution process is identical — edit the file, remove markers, `git add`. The difference is what command you run to continue: after a merge conflict you run `git commit`. After a rebase conflict you run `git rebase --continue`. And rebase may give you multiple conflicts to resolve one by one (one per commit being replayed).

---

*📌 Previous: [02 - Branches & Merging](./02-branches-merging.md) | Next: [04 - Git Rebase](./04-git-rebase.md)*
