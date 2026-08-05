## Stash
###
- Stash command is used when you have changes that you are not quite ready to commit.
- You want to revert back (temporarily) to where you started
- You dont know what to do with your changes yet.
- Stash carries over from branch to branch

Stash will save them to a temprary place and you can get back to them later on

```bash
# Save stash with a message
git stash save "Worked on add function"

# All stashes have ids and messages
git stash list

# Takes changes from the stash but does not get rid of the stash
git stash apply stash@{0}

# Grabs the latest stash + applies it + drops the stash
git stash pop

# Get rid of a stash
git stash drop stash@{0}

# Get rid of all stash
git stash clear

```

Most likely case..
You were on the wrong branch (say, main) and made changes that were supposed to be in another branch.

```
git checkout main

## made some changs....

# Stash the changes
git stash save "Add function"

# Go to correct branch
git checkout add

# Apply changes
git stash pop
```