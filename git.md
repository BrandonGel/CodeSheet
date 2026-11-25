# Git cheat sheet

## Cloning a repo
Download a repo into a new folder named after it:
```bash
git clone git@github.com:USERNAME/REPO.git
cd REPO
```
Clone a single branch `BRANCH_NAME` only:
```bash
git clone -b BRANCH_NAME --single-branch git@github.com:USERNAME/REPO.git
```

## Checking status and changes
See which files are changed, staged, or untracked:
```bash
git status
```
See unstaged changes, then changes already staged for the next commit:
```bash
git diff
git diff --staged
```
Compare your branch against `main`:
```bash
git diff main...HEAD
```
Compact history of the current branch:
```bash
git log --oneline --graph -n 20
```

## Committing
Stage everything and commit:
```bash
git add --all
git commit -m "MESSAGE"
```
Pull the latest changes, then push:
```bash
git pull
git push
```

## Branches
Create and switch to a new branch `BRANCH_NAME`:
```bash
git checkout -b BRANCH_NAME
```
Switch to an existing branch, and list all branches:
```bash
git checkout BRANCH_NAME
git branch -a
```
Delete a branch locally (`-d` refuses if it is not merged yet), then on GitHub:
```bash
git branch -d BRANCH_NAME
git push origin --delete BRANCH_NAME
```

## Merging
Bring `BRANCH_NAME` into `main`:
```bash
git checkout main
git pull
git merge BRANCH_NAME
```
On a conflict, fix the marked files, then:
```bash
git add --all
git commit
```
Give up on the merge and go back to before it started:
```bash
git merge --abort
```

## Rebasing
Replay your branch on top of the latest `main` for a straight history:
```bash
git checkout BRANCH_NAME
git fetch origin
git rebase origin/main
```
On a conflict, fix the marked files, then continue (or abort):
```bash
git add --all
git rebase --continue
git rebase --abort
```
A rebased branch that was already pushed needs a force push. `--force-with-lease` refuses if someone else pushed in the meantime:
```bash
git push --force-with-lease
```

## Stashing
Set uncommitted changes aside, for example to switch branches:
```bash
git stash push -m "MESSAGE"
```
List stashes, then bring the latest one back:
```bash
git stash list
git stash pop
```
Include untracked (new) files too:
```bash
git stash push -u -m "MESSAGE"
```

## Undoing changes
Discard unstaged changes to a file:
```bash
git restore FILE
```
Unstage a file but keep its changes:
```bash
git restore --staged FILE
```
Fix the last commit's message, or add forgotten staged files to it (only before pushing):
```bash
git commit --amend
```
Undo the last commit but keep its changes staged:
```bash
git reset --soft HEAD~1
```
Undo the last commit and throw its changes away (cannot be undone easily):
```bash
git reset --hard HEAD~1
```
Undo a commit that was already pushed by adding a new commit that reverses it:
```bash
git revert COMMIT_HASH
```
Find a lost commit after a bad reset, then go back to it:
```bash
git reflog
git reset --hard COMMIT_HASH
```


## Using SSH instead of username/password
Point the remote at the SSH URL.

GitHub:
```bash
git remote set-url origin git@github.com:USERNAME/REPO.git
```
Georgia Tech GitHub:
```bash
git remote set-url origin git@github.gatech.edu:USERNAME/REPO.git
```
Then set up an SSH key: [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

## Pushing a local project to a new GitHub repo
Create the empty repo on GitHub first, then in the project folder:
```bash
git init
git add --all
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:USERNAME/REPO.git
git push -u origin main
```

## Ignoring files over 100 MB
GitHub rejects files larger than 100 MB. Append them to `.gitignore`:
```bash
find * -type f -size +100M >> .gitignore
```
