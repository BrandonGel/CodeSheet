# Git cheat sheet

## Branches
Create and switch to a new branch `BRANCH_NAME`:
```bash
git checkout -b BRANCH_NAME
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
