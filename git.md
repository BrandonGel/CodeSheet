# Github cheat sheet

Creating/Switiching to a new branch ```BRANCH_NAME```
```
git checkout -b BRANCH_NAME
```

To bypass the github requirement for password and username, need to update remote url.
For regular github:
```
git remote set-url origin git@github.com:username/repo.git
```
For gatech github
```
git remote set-url origin git@github.gatech.edu:username/repo.git
```
Next, use this [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) for setting up the ssh keys.
 
For creating a new git repo on terminal and pushing to the existing new git repo
```
git init
git add --all
git commit -m "first commit"
```
Paste this command line for adding the github repo online (remember to change the name)
```
git remote add origin git@github.com:brandongel/<reponame>.git
```
Then push
```
git push -u origin master
```

For gitignore, to ignore files with greater than 100mb
```
find * -size +100M | cat >> .gitignore
```
