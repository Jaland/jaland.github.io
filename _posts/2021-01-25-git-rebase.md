---
layout: post
title:  "Rebasing Git Commits"
tags: github git
category: git
---

# Squashing Git Commits

A quick and dirty on combining multiple commits. Can be used to clean up commit history before merging a personal branch.


### Steps

* Run Rebase Command (interactively `-i`)
```bash
git rebase -i HEAD~<# Of commits>
```

* Any commits you want to squash by changing `pick` into `s` inside vi
  * Note everything with `#` in front of it is just a comment and will be ignored
  * Save and quit vi `:wq`
````
pick b2149dc Commit we want to keep
s c5489da Commit to lose

# Rebase 263e7ff..b2149dc onto 263e7ff (1 command)
````

* Update commit message
 * By default it will just combine all of the commit messages into one. But you can override with your own commit message
````
# This is a combination of 2 commits.
# This is the 1st commit message:

Commit we want to keep

# This is the commit message #2:

#Just comment out the line below to remove it from your commit message
Commit we want to lose
````

* Push to git
  * Requires a forced push since you are rewriting history
    * Makes you feel like the historian of the winning side doesn't it. 
```bash
git push -f
```

---
**IMPORTANT**

Rebasing commits in git will DESTROY ALL THE COMMITS YOU REBASE. Make sure you are careful when you check in.

You should not rebase if:
* One of your commits include a merge
* You are spanning multiple branches
* On main/master branches

The main intent of rebasing is to squash multiple commits on a local branch into one before pushing back to your main branch in order to keep the git history clearer. And make you seem smarter, you know for all those people who go back and look at git commit history... I pity those people

---


## Tips and Tricks

Replace `picks` with squash `s`:
```bash
:%s/pick/s
 ```

Something went wrong? Abort the rebase with:
```bash
git rebase --abort
```