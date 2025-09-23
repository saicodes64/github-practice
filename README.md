### Git & GitHub — Hands-on Recovery + Practice Prompt (step-by-step)

## Goal

By the end of this exercise you will be comfortable with:
	•	Creating feature branches and PRs (no direct pushes to main).
	•	Creating, reproducing, and resolving merge conflicts.
	•	Using branch protection / PR review flow on GitHub.
	•	Useful Git commands: branch, checkout/switch, merge, rebase, stash, cherry-pick, revert, reset.
	•	Writing clear commit messages and maintaining a clean history.

⸻
Perfect! I can help you update your README guide to include git reset (soft, mixed, hard), git cherry-pick, and also emphasize recovery commands, so it’s a more complete hands-on learning resource. Here’s an updated version you can drop into your README:

⸻

Git & GitHub — Hands-on Recovery + Practice Prompt (step-by-step)

Problem you faced (short):
You repeatedly committed work directly to main instead of creating feature branches. main should be stable/production-ready; feature work belongs on branches and merged via PRs. That made history messy and made it hard to manage features, rollbacks, and collaboration.

Below is a copy-pasteable, step-by-step practice prompt you can follow to learn by doing, fix habits, and intentionally practice branching, PRs, merge conflicts, rebasing, stashing, and recovery.

⸻

Goal

By the end of this exercise you will be comfortable with:
	•	Creating feature branches and PRs (no direct pushes to main).
	•	Creating, reproducing, and resolving merge conflicts.
	•	Using branch protection / PR review flow on GitHub.
	•	Useful Git commands: branch, checkout/switch, merge, rebase, stash, cherry-pick, revert, reset.
	•	Writing clear commit messages and maintaining a clean history.

⸻
## **Setup (one-time)**

1. Create a new repo on GitHub called git-practice (or use a local repo).

1. Clone it locally:

```
git clone git@github.com:YOUR_USER/git-practice.git
cd git-practice
```

1. 
2. Create a small project file to work with:

```
echo "# git-practice" > README.md
mkdir src; echo "console.log('v0');" > src/app.js
git add .
git commit -m "chore: initial commit"
git push -u origin main
```

1. 
2. On GitHub enable **branch protection** for main (require PR before merging) — this forces PR workflow.

---

## **Practice Plan (do each step and check the ✅ items)**

### **Part A — Branching workflow (basic)**

1. Create a development branch and two feature branches:

```
git switch -c develop
git push -u origin develop

git switch -c feature/login
# edit src/app.js or add a file
echo "console.log('login feature');" >> src/app.js
git add .
git commit -m "feat(login): add login stub"
git push -u origin feature/login
```

1. 
2. Create a Pull Request (PR) on GitHub from feature/login → develop.
    
    ✅ PR created, reviewers can comment.
    
3. Merge PR on GitHub (use merge or squash — try both later).

```
git switch develop
git pull
```

1. ✅ develop now contains the feature.

### **Part B — Protect**

### **main**

### **and merge via PR**

1. Create feature/ui branch and push changes, open PR to main (or develop if you use that flow).
2. Ensure CI checks (or a simple test) run on PR (see optional Actions below).
3. Merge via PR only after review.
    
    ✅ No direct git push origin main used.
    

---

### **Part C — Create and resolve a merge conflict (intentional)**

1. Start from develop:

```
git switch develop
git pull
```

1. Create branch A:

```
git switch -c feature/A
# change a specific line in src/app.js (line X)
sed -n '1,200p' src/app.js
# Edit: replace a line or append a block; commit & push
git add src/app.js
git commit -m "feat(A): modify shared line"
git push -u origin feature/A
```

1. 
2. Create branch B from the same commit (simulate another collaborator):

```
git switch develop
git switch -c feature/B
# Make a DIFFERENT change to the SAME line in src/app.js
git add src/app.js
git commit -m "feat(B): conflicting change on same line"
git push -u origin feature/B
```

1. 
2. Merge feature A into develop on GitHub (or locally). Then try to merge feature B into develop:
    - If merging locally:

```
git switch develop
git merge feature/A    # ok
git merge feature/B    # <- conflict happens
```

1. 
    - 
    - Git will stop and show conflicted files. Resolve manually:

```
git status
# open src/app.js, find conflict markers <<<<<<< ======= >>>>>>>
# edit to correct content, then:
git add src/app.js
git commit -m "fix: resolve merge conflict between feature/A and feature/B"
git push origin develop
```

1. 
    
    ✅ You resolved a real merge conflict.
    

### **Part D — Rebase & interactive rebase (history clean-up)**

1. Practice interactive rebase to squash commits:

```
git switch feature/B
git rebase -i HEAD~3   # squash or reorder last 3 commits
```

1. 
    - Learn to pick, squash, reword.
        
        ✅ You can produce a cleaner commit history before merging.
        
2. Try rebase onto develop:

```
git switch feature/B
git fetch origin
git rebase origin/develop
# solve conflicts if any
git push --force-with-lease origin feature/B
```

1. ✅ Understand when force-pushing is appropriate (only on your feature branch).

⸻

Part E — Stash, cherry-pick, revert, reset
	1.	Stash (save WIP):

# make changes but don't commit
git stash
git switch develop
git stash pop

	2.	Cherry-pick (copy a commit from another branch):

git switch develop
git cherry-pick <commit-hash-from-feature>

	3.	Revert (undo a commit safely by creating a new commit):

git revert <commit-hash>
git push origin develop

	4.	Reset (undo commits locally):

Reset Type	Command	Effect
Soft	git reset --soft <commit>	Move HEAD, keep changes staged
Mixed	git reset <commit> (default)	Move HEAD, unstage changes, keep in working dir
Hard	git reset --hard <commit>	Move HEAD, discard changes in staging & working dir

Example Practice:

# create 3 commits A → B → C
git reset --soft HEAD~1   # C undone, changes staged
git reset HEAD~1          # C undone, changes unstaged
git reset --hard HEAD~1   # C undone, changes removed completely

✅ Understand when to use each reset type to recover or rewrite history.

⸻
## **Optional: Add a simple GitHub Actions CI (learn CI on PRs)**

Create .github/workflows/node.yml:

```
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node
        uses: actions/setup-node@v4
        with: node-version: '18'
      - run: npm ci || true
      - run: echo "run tests here"
```

✅ PR will show a check on GitHub; learn to require checks in branch protection.

-----------------

Quick command cheat-sheet (copy to your notes)

```
git status
git branch -a
git switch -c feature/name
git add .
git commit -m "type(scope): message"
git push -u origin feature/name
git merge feature/name
git rebase origin/develop
git stash
git stash pop
git cherry-pick <hash>
git revert <hash>
git reset --soft|--mixed|--hard <commit>
git log --oneline --graph --all
git show <hash>
```

⸻

# Challenges & Exercises (do them in order)
	1.	Create repo + develop + two feature branches; merge them via PRs.
	2.	Intentionally create a conflict and resolve it.
	3.	Use interactive rebase to squash multiple small commits into 1 neat commit.
	4.	Practice cherry-pick and revert.
	5.	Practice all three reset types (soft, mixed, hard) in a small commit chain.
	6.	Enable branch protection and require PR review before merging.
	7.	Add GitHub Actions CI checks on PRs.

⸻

## Success criteria
	•	Never push directly to main — only via PRs.
	•	Reproduce and resolve a merge conflict confidently.
	•	Clean up commits using rebase -i.
	•	Recover work with stash, revert, reset, and cherry-pick without losing data.
	•	Explain why main should be protected and how PRs help.

⸻
