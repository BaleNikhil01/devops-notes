# Day 3 — Git & GitHub

## 1. Git Workflow

```text
Working Directory
       ↓
Staging Area
       ↓
Local Repository
       ↓
Remote Repository (GitHub)
```

- **Working Directory** → files you are editing
- **Staging Area** → changes selected for the next commit
- **Local Repository** → committed changes on your machine
- **Remote Repository** → repository hosted on GitHub

---

## 2. Essential Commands

| Command | Purpose |
|---|---|
| `git init` | Initialize repository |
| `git clone <url>` | Clone repository |
| `git status` | Check changes |
| `git add .` | Stage changes |
| `git commit -m "msg"` | Create commit |
| `git push` | Upload commits to remote |
| `git pull` | Fetch + merge remote changes |
| `git fetch` | Download remote changes without merging |
| `git branch` | List branches |
| `git switch <branch>` | Switch branch |
| `git switch -c <branch>` | Create + switch branch |
| `git merge <branch>` | Merge branch |
| `git rebase <branch>` | Reapply commits on top of branch |
| `git log --oneline` | View commit history |
| `git diff` | View unstaged changes |
| `git diff --staged` | View staged changes |
| `git stash` | Temporarily save changes |
| `git stash pop` | Restore stashed changes |
| `git reset` | Move HEAD/branch backward |
| `git revert <commit>` | Create commit that undoes changes |

---

## 3. Branches

Branches allow developers to work independently.

```bash
git switch -c feature-login
# make changes
git add .
git commit -m "Add login feature"
git push -u origin feature-login
```

Typical workflow:

```text
main
 └── feature-login
       ↓
   development
       ↓
 Pull Request
       ↓
    main
```

---

## 4. Merge vs Rebase

### Merge

```bash
git switch main
git merge feature-login
```

- Combines branch histories
- Preserves existing commit history
- May create a merge commit

### Rebase

```bash
git switch feature-login
git rebase main
```

- Creates a cleaner, linear history
- Rewrites commit history
- Avoid rebasing commits already shared with others unless you understand the impact

---

## 5. Merge Conflicts

A conflict can occur when Git cannot automatically combine changes.

```bash
git status
```

Fix the conflicting file, then:

```bash
git add .
git commit
```

---

## 6. `.gitignore`

Prevents unwanted files from being tracked.

Example:

```gitignore
.env
*.log
node_modules/
.terraform/
*.tfstate
```

Never commit passwords, API keys, or other secrets.

---

## 7. Stash

Use when you have unfinished changes but need to switch branches.

```bash
git stash
git switch main
```

Later:

```bash
git stash pop
```

---

## 8. Reset vs Revert

| Reset | Revert |
|---|---|
| Moves branch/HEAD backward | Creates a new undo commit |
| Rewrites history | Preserves history |
| Useful for local changes | Safer for shared branches |

```bash
git reset --hard <commit>
git revert <commit>
```

For a bad commit already pushed to a shared branch, `git revert` is generally the safer approach.

---

## 9. Pull vs Fetch

```text
git fetch
    ↓
Download remote changes
    ↓
Does NOT merge

git pull
    ↓
Fetch remote changes
    ↓
Integrate them into current branch
```

---

# Interview Scenarios

### Q1. Git vs GitHub?

**Git** is a version-control system.  
**GitHub** is a platform for hosting Git repositories and collaboration.

### Q2. `git pull` vs `git fetch`?

`fetch` downloads changes without integrating them.  
`pull` fetches and integrates the changes.

### Q3. You have uncommitted changes and need to switch branches. What do you do?

```bash
git stash
git switch <branch>
```

### Q4. Two developers modify the same lines. What happens?

Git may create a **merge conflict**. Resolve the file manually, then:

```bash
git add .
git commit
```

### Q5. You accidentally committed a secret. Is `.gitignore` enough?

No. `.gitignore` only prevents future tracking. **Immediately revoke/rotate the exposed secret** and remove it from history if necessary.

### Q6. You pushed a bad commit to a shared branch. Reset or revert?

Usually **revert**, because it creates a new commit without rewriting shared history.

### Q7. Why use branches?

To isolate work such as features, bug fixes, or experiments without directly modifying the main branch.

### Q8. What is a Pull Request?

A request to review and merge changes from one branch into another.

---

# Day 3 Hands-on

Create:

```text
devops-practice/
├── linux/
├── bash/
├── docker/
├── terraform/
├── kubernetes/
└── README.md
```

Practice:

```bash
mkdir devops-practice
cd devops-practice
git init

mkdir linux bash docker terraform kubernetes
touch README.md

git status
git add .
git commit -m "Initial DevOps practice structure"

git switch -c feature-git
git log --oneline
git diff
git stash
git stash pop
```

Then create the GitHub repository, connect it, push your `main` branch, create a feature branch, and practice a Pull Request + merge conflict.