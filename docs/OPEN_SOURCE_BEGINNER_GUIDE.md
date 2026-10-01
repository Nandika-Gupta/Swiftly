# Open Source Beginner Guide: Git & GitHub Commands (Codess Cohort 8)

A start-to-finish guide to every Git/GitHub command you need to contribute to open source,
what each one means, and a walkthrough of your first real PR to
[firstcontributions/first-contributions](https://github.com/firstcontributions/first-contributions)
titled **"Codess cohort 8"**.

---

## 1. The mental model (read this first)

| Term | What it means |
|---|---|
| **Git** | A tool on *your computer* that records every change to your files (what, when, who). |
| **GitHub** | A website that hosts Git repositories online so people can collaborate. |
| **Repository (repo)** | A project folder tracked by Git, including its full history. |
| **Working directory** | The files you see and edit on your machine. |
| **Staging area** | A "shopping cart" of changes you've chosen to include in the next commit. |
| **Commit** | A saved snapshot of staged changes, with a message explaining it. |
| **Branch** | A separate line of work. You make changes on a branch so `main` stays clean. |
| **Remote** | A copy of the repo hosted somewhere else (e.g. GitHub). |
| **`origin`** | The default name for *your* remote (usually your fork). |
| **`upstream`** | The conventional name for the *original* project you forked from. |
| **Fork** | Your own copy of someone else's repo, under your GitHub account. You can push to it. |
| **Clone** | Downloading a repo from GitHub to your computer. |
| **Pull Request (PR)** | A request asking the maintainers to merge changes from your branch into their project. |
| **Issue** | A note on a project describing a bug, feature request, or question. Not code. |
| **Merge conflict** | When two changes edit the same lines and Git needs you to choose which to keep. |

The flow of a change:

```
 edit files          git add           git commit          git push           open PR
 ─────────────▶ [working dir] ─────▶ [staging] ─────▶ [local repo] ─────▶ [your fork] ─────▶ [original repo]
```

Open source workflow in one picture:

```
 original repo (upstream) ──Fork (on GitHub)──▶ your fork (origin) ──git clone──▶ your laptop
        ▲                                                 ▲                          │
        └──────────── Pull Request ◀──────────────────────┴──────── git push ◀───────┘
```

---

## 2. One-time setup

```bash
git --version
```
Checks Git is installed. If not, download it from https://git-scm.com/downloads.

```bash
git config --global user.name "Nandika Gupta"
git config --global user.email "you@example.com"
```
Tells Git who you are. This name/email is stamped on every commit. Use the email linked to your
GitHub account so commits show up on your profile.

```bash
git config --global init.defaultBranch main
```
Makes new repos start on a branch called `main` (the modern default).

```bash
git config --list
```
Shows all your current Git settings.

**Authentication:** GitHub no longer accepts your password on the command line. Either:
- Install the [GitHub CLI](https://cli.github.com/) and run `gh auth login` (easiest), or
- Create a Personal Access Token (GitHub → Settings → Developer settings → Tokens) and paste it when asked for a password, or
- Set up an SSH key (`ssh-keygen -t ed25519 -C "you@example.com"`, then add the `.pub` key under GitHub → Settings → SSH keys).

---

## 3. Terminal basics you'll use alongside Git

| Command | Meaning |
|---|---|
| `pwd` | Print where you are (current folder). |
| `ls` (`dir` on Windows CMD) | List files in the folder. |
| `cd folder-name` | Move into a folder. `cd ..` goes up one level. |
| `mkdir name` | Make a new folder. |
| `touch file.txt` | Create an empty file (Mac/Linux/Git Bash). |
| `code .` | Open the current folder in VS Code (if installed). |

---

## 4. Getting a project onto your machine

```bash
git clone https://github.com/<your-username>/<repo>.git
```
Downloads a full copy of the repo (all files + history) into a new folder. Clone **your fork**, not
the original, so you can push to it.

```bash
cd <repo>
```
Enter the project folder; Git commands only work inside a repo.

```bash
git init
```
Turns the *current* folder into a brand-new Git repo (creates a hidden `.git` folder). You only need
this when starting your own project from scratch, not when cloning.

```bash
git remote -v
```
Lists remotes and their URLs. After cloning your fork you'll see `origin`.

```bash
git remote add upstream https://github.com/<original-owner>/<repo>.git
```
Adds the original project as a second remote named `upstream`, so you can pull in its latest changes.

---

## 5. Checking what's going on

```bash
git status
```
**Your most-used command.** Shows your branch, which files are modified, staged, or untracked.
Run it before and after everything.

```bash
git diff
```
Shows exactly which lines changed that are *not yet staged*. `git diff --staged` shows what *is* staged.

```bash
git log
git log --oneline
git log --oneline --graph --all
```
Shows commit history. `--oneline` = one line per commit; `--graph --all` draws branches.

---

## 6. Branches

```bash
git branch
```
Lists local branches; `*` marks the one you're on. `git branch -a` includes remote branches.

```bash
git switch -c my-branch-name
# older equivalent: git checkout -b my-branch-name
```
**Creates** a new branch and moves onto it. Always make a new branch for each contribution.

```bash
git switch main
# older equivalent: git checkout main
```
Moves to an existing branch.

```bash
git branch -d my-branch-name
```
Deletes a local branch (after it's merged). `-D` forces deletion.

---

## 7. Saving your work

```bash
git add Contributors.md     # stage one file
git add .                   # stage everything in this folder and below
git add -A                  # stage every change in the whole repo, including deletions
```
Moves changes into the staging area. In open source, prefer adding **specific files** so you don't
accidentally commit junk.

```bash
git restore --staged file.txt
# older equivalent: git reset file.txt
```
Un-stages a file (keeps your edits).

```bash
git restore file.txt
```
Throws away your un-staged edits to a file (careful: not recoverable).

```bash
git commit -m "Add Nandika Gupta to Contributors list"
```
Saves the staged changes as a snapshot. Write messages in the imperative: "Add…", "Fix…", "Update…".

```bash
git commit --amend
```
Edits the most recent commit (message or contents). Only do this **before** pushing, or on your own branch.

```bash
git reset HEAD~1
```
Undoes the last commit but keeps the changes in your files.

```bash
git rm file.txt            # delete file and stage the deletion
git rm --cached file.txt   # stop tracking but keep the file on disk
git mv old.txt new.txt     # rename/move and stage it
```

---

## 8. Syncing with GitHub

```bash
git push -u origin my-branch-name
```
Uploads your branch's commits to your fork. `-u` remembers the link, so next time just `git push`.

```bash
git pull
```
Downloads new commits from the remote branch and merges them into your current branch
(= `git fetch` + `git merge`).

```bash
git fetch upstream
```
Downloads the latest from the original project **without** changing your files.

```bash
git switch main
git pull upstream main
git push origin main
```
The standard "keep my fork up to date" routine. Do this before starting new work.

---

## 9. Combining work & fixing conflicts

```bash
git merge upstream/main
```
Brings changes from another branch into your current branch.

If you see `CONFLICT`, open the file and look for:
```
<<<<<<< HEAD
your version
=======
their version
>>>>>>> upstream/main
```
Edit it to the final text you want, delete the markers, then:
```bash
git add <file>
git commit
```

```bash
git rebase upstream/main
```
Replays your commits on top of the latest upstream (cleaner history). Only rebase branches that are
yours. If you rebased an already-pushed branch, you'll need `git push --force-with-lease`.

```bash
git merge --abort    # or: git rebase --abort
```
Bail out of a merge/rebase that went wrong and go back to where you were.

---

## 10. Handy extras

```bash
git stash          # temporarily shelve uncommitted changes
git stash pop      # bring them back
git revert <commit-sha>   # create a new commit that undoes an old one (safe on shared branches)
git show <commit-sha>     # see what a specific commit changed
git blame file.txt        # see who last changed each line
```

**`.gitignore`**: a file listing things Git should never track (e.g. `node_modules/`, `.env`).

---

## 11. Your first contribution: first-contributions → PR "Codess cohort 8"

The [first-contributions](https://github.com/firstcontributions/first-contributions) repo exists so
beginners can practise the whole flow safely. The task is to **add your name to `Contributors.md`** and open a PR.

### Step 1: Fork
Go to https://github.com/firstcontributions/first-contributions and click **Fork** (top right) →
**Create fork**. You now have `https://github.com/Nandika-Gupta/first-contributions`.

### Step 2: Clone *your fork*
```bash
git clone https://github.com/Nandika-Gupta/first-contributions.git
cd first-contributions
```

### Step 3: Create a branch
```bash
git switch -c codess-cohort-8
```

### Step 4: Make the change
Open `Contributors.md` in any editor. Add one line **somewhere in the middle** (not the very top or
bottom, which avoids conflicts with others), matching the existing format:

```markdown
- [Nandika Gupta](https://github.com/Nandika-Gupta) - My first open-source contribution with Codess Cohort 8!
```

Check your change:
```bash
git status
git diff
```

### Step 5: Stage and commit
```bash
git add Contributors.md
git commit -m "Add Nandika Gupta to Contributors list"
```

### Step 6: Push to your fork
```bash
git push -u origin codess-cohort-8
```

### Step 7: Open the Pull Request
1. Go to your fork on GitHub. A yellow banner shows **Compare & pull request**; click it.
2. Check that the base repository is `firstcontributions/first-contributions` with base `main`, and the head repository is `Nandika-Gupta/first-contributions` with compare `codess-cohort-8`.
3. **Title:** `Codess cohort 8`
4. **Description** (example):
   ```
   Adding my name to Contributors.md as my first open-source contribution, as part of Codess Cohort 8.
   ```
5. Click **Create pull request**. 🎉

### Step 8: After you open it
- A bot/maintainer usually merges it automatically within a short time; you'll get an email.
- If asked to change something: edit, `git add`, `git commit`, `git push`. The PR updates by itself.
- If GitHub says there's a conflict, sync with upstream (section 8) and merge (section 9).

### Step 9: Clean up (optional)
```bash
git switch main
git pull upstream main     # after: git remote add upstream https://github.com/firstcontributions/first-contributions.git
git branch -d codess-cohort-8
```

---

## 12. Etiquette & next steps

- **Read `README`, `CONTRIBUTING.md`, and the Code of Conduct** of every project first.
- **Look for labels** like `good first issue`, `help wanted`, `documentation`. Also try
  [goodfirstissue.dev](https://goodfirstissue.dev) and [up-for-grabs.net](https://up-for-grabs.net).
- **Comment on an issue before working on it** ("I'd like to work on this, my plan is…"). Wait to be
  assigned if the project assigns issues. Don't claim many issues at once.
- **Check a project is active**: recent commits, maintainers replying to issues/PRs.
- **Keep PRs small and focused**: one fix per PR. Link the issue with `Fixes #123` in the description.
- **Use a draft PR** if you want early feedback on unfinished work.
- **Be patient**: maintainers are often volunteers. A polite follow-up after ~1 week is fine.
- **If you use AI**, understand and test every line you submit; you're responsible for it.
- Contribution isn't only code: docs, tests, bug reports, reviewing, and helping others all count.

---

## 13. Cheat sheet

| Goal | Command |
|---|---|
| Copy repo to laptop | `git clone <url>` |
| See what changed | `git status`, `git diff` |
| New branch | `git switch -c <name>` |
| Switch branch | `git switch <name>` |
| Stage | `git add <file>` |
| Commit | `git commit -m "message"` |
| Upload | `git push -u origin <branch>` |
| Get latest | `git pull` / `git fetch upstream` |
| Add original repo | `git remote add upstream <url>` |
| Combine | `git merge <branch>` |
| Shelve work | `git stash` / `git stash pop` |
| Undo last commit (keep edits) | `git reset HEAD~1` |
| Undo a pushed commit safely | `git revert <sha>` |
| History | `git log --oneline` |
