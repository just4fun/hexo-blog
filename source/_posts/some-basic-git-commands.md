title: Some basic Git commands
date: 2015-02-13 21:11:12
tags:
- git

---

Record some basic Git commands here for later reference.

<!-- more -->

### Stashing

```bash
$ git stash                         # stash half-done code into stack except untracked files
$ git stash -u                      # stash any changed files
$ git stash -p                      # prompt which of changes you would like to stash
$ git stash branch new_branch_name  # create new branch with stash and drop it from stack
$
$ git stash list                    # list stored stashes
$
$ git stash apply                   # apply stored stash but the file you staged before wasn’t restaged
$ git stash apply --index           # apply stored stash and reapply the staged changes
$ git stash pop                     # apply stored stash and drop it from stack immediately
$
$ git stash drop                    # drop stash from stack
```