## clone a repo

```bash
git clone $@git

git remote -v

# add/delete upstream
git remote add upstream $git:.git
git remote rm upstream

# rename
git remote set-url origin $.git
git remote set-url upstream $.git
```

## create a branch

```bash
git checkout -b $branch
git branch -d
```

## basic commands

```bash
git branch
git remote -v

git status
git diff $file

git clean -n # check which files will be deleted before actually deleting
git clean -f # delete untracked files
```

## create pull request

```bash
git add $file
git add .

git commit -m "message"

git push origin $branch
git push origin $current_branch:remote_branch
```

## resolve a conflict

to be validated

```bash
git checkout master
git remote -v

git pull upstream master
git push origin master

git checkout $branch # get back to the working branch

git rebase master

git am --show-current-patch

vi $file
git add $file

git rebase --continue

git push origin $branch -f
```

## origin vs upstream

in general

- upstream = original repo

- origin = fork

`git fetch` alone would fetch from `origin` by default

[What is the difference between origin and upstream on GitHub?](https://stackoverflow.com/questions/9257533/what-is-the-difference-between-origin-and-upstream-on-github)