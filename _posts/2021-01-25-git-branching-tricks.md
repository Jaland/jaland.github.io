---
layout: post
title:  "Git Tricks for Branching"
tags: github
categories: git
---

# Some tips and tricks I have found for GitHub branching over the years

Deleting a local branch
```bash
git branch -D <BRANCH_NAME>
```

Deleting a remote branch
```bash
git push origin --delete <BRANCH_NAME>
```

# Setting up SSH Keys

This allows you to push from your local computer without having to supply username/password

First lets create the SSH Key:

1. Paste the following:
    * `ssh-keygen -o`
2. Fill in required information, I used default key location
3. Retrieve and copy the public key information `cat ~/.ssh/id_rsa.pub`


Now let's add our public key to our Github account
* Navigate to `settings -> SSH and GPG keys`
* Click `New SSH key`
* Paste the information from your public key inside the textbox and save

Should not be able to pull using SSH.

# Adding Existing PEM to SSH Keys

If a `pem` already exist just add to your private keys with `ssh-add <PATH TO KEY>` (note you may need to `chmod 400`).
