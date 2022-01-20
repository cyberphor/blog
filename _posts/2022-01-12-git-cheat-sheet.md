---
layout: post
title: 'Git Cheat-Sheet'
permalink: 'git-cheat-sheet'
---

**A Typical Git Workflow**
```bash
# set credentials (once)
git config --global user.name "elliotalderson"
git config --global user.email "elliotalderson@evil.corp"

# download a repo (as needed)
git clone https://github.com/cyberphor/scripts/
# your_username
# your_token
git config --global credential.helper cache

# add changes to the repo (after you update something in it)
git add -u # stage modified and deleted files
git commit -m "Updated something"
git push

# pull changes from the repo (after something has been updated by somebody else)
git pull
```

**How to Remove a Cached Token**
```bash
git config --global --unset credential.helper
```

**References**  
* [https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token](https://stackoverflow.com/questions/7124726/git-add-only-modified-changes-and-ignore-untracked-files)
* [https://www.edgoad.com/2021/02/using-personal-access-tokens-with-git-and-github.html](https://stackoverflow.com/questions/7124726/git-add-only-modified-changes-and-ignore-untracked-files)
* [https://stackoverflow.com/questions/7124726/git-add-only-modified-changes-and-ignore-untracked-files](https://stackoverflow.com/questions/7124726/git-add-only-modified-changes-and-ignore-untracked-files)
