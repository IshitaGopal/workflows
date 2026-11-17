- https://www.youtube.com/watch?v=FdZecVxzJbk&list=PL-osiE80TeTuRUfjRe54Eea17-YfnOOAx&index=2

* "Working directory is clean" msg = no untracked/modified files

## Git log

```bash
git log
```
- gives a hash number of each commit
- all hashes are unique
- Provides the author who made the commit + the msg
-

## Git clone
```bash
git clone <url> <where you want to clone>

# Clone all the files from the <url> repo into the current directory
git clone <url> .

# List all branches locally and remote
git branch -a
```

## Git Push
- commit changes locally -> push the changes to the remote repository

```bash
# Show changes to the code
git diff

git status
git add -A
git commit -m "msg"

git pull
git push

```
## Git branch

```bash
# create a branch
git branch calc-divide

# list local braches
git branch
git checkout calc-divide


git add -A
git commit -m "Divide Function"

# origin = name of the remote repo and the branch we want to push to
# u = tells git we want to associate the local calc-divide with the remote calc-divide branch. Then in the future we just do git push and git pull.
git push -u origin calc-divide
```

## Merge??

```bash
git checkout master main?
git pull
git branch --merged
git merge calc-divide
git push origin master

# delete branch
git branch --merged
git branch -d calc-divide
git branch -a
git push origin --delete calc-divide
git branch -a
```


### Made changes to a file but we dont want to keep any of the changes
```bash
# Go back to the way things were
# hack hack
git status # shows modified
git diff # shows differences
# Clean working directory
git checkout
```
###  Bad/wrong commit message
- Change the message without creating another commit
- `--ammend` unique hash is different/changed
- When the hash changes -> means it chaged the git history. We only want to do this if we are the only ones who have had access to the changes. If we pushed the changes for other users to see, we do not want to change the hsistory because it can cause problems. There are other ways to make corrections without changing the git history. But this is ok to do if we are the only ones who has seen it. Also leads to cleaner commits.
- mostly do this only when you have not pushed your changes to other people

### Accidently left of a file we meant to commit

```bash
# Make it part of the last commit
git add <file>
git commit --ammend
git commit --ammend --no-edits
# shows files changed in the commit
git log --stat
# interactive editor

```

```
# Modify commit msg
git commit --ammend -m "<New Message>"
```

## Remove files from the staging area
```bash
# This will remove calc.py from the staging area
# Put it in untracked files
git reset calc.py

# Remove everything from the staging area
git reset
```

```bash
# Undo unstaged changes (restore file to last commit)
# This resets the file back to the last committed version.
git restore app.py
# Undo staged changes (remove from staging area)
# This keeps your changes but unstages them.

git restore --staged app.py

# Undo both staged + unstaged changes (hard reset for a file)

```


## Cherrypick
- Made commit to the wrong branch
- Move changes to a branch, revert master to initial state
- `cherry-pick` does not delete commmits

```bash
1. git log
2. # grab the hash of the commit we want to pick
   # (6-7 characters from the hash is fine..dont have to copy the whole thing)
3. git checkout subtract-feature
4. git cherry-pick <hash>
5. git log
# This does not delete commits on the main
6. git checkout main
7. git log
8. git reset --hard

```

## Git Resets
soft:
- reset back to the commit we specify + keeps changes in the staging directory
```bash
git log

# grab the hash of the commit you want to go to try to return to the initial commit
git reset --soft d63a377

# Will no longer show the second commit
git log

# But it keeps the changes in the staging directory
git status
```

mixed:
- default
keeps the changes but the changes are in the working directory
```bash
# default
git log
git reset d63a377c
git log
# Keeps the changes are in the working dir not the staging area
git status
```

hard:
- CAREFUL WITH THIS!
- Will make all the tracked files match the state they were in at the hash we specify
- It leaves untracked files alone
- Gets rid of changes

```bash
git reset --hard d63a377c
```
### Get rid of untracked files


`-df`: gets rid of untracked files and directory

`git clean` → removes untracked files

`-d` → includes directories (not just files)

`-f` → force (required, otherwise Git won’t run it

`-n` = no action

```bash
git clean -n
git clean -df
```
### Get rid of files within a particular folder
```bash
# Preview what will be deleted
git clean -n <folder>

# Delete untracked files in a specific folder
git clean -f <folder>

# Include untracked directories too
git clean -fd <folder>
git clean -fd data/temp/

```

### Run git reset hard on files you needed
- May not be totally out of luck if a lot of time has not passed
-

```bash
git reflog
# Grab the hash before the reset
git checkout <hash>
```

### `git revert` : Undo commit but other people have pulled the changes
- You should not change the git history if other peopl have pulled those changes already
- `revert` creates new commits to reverse the effect of some earlier commits
- You dont re write any history. This does not modify or delete the existing commits we have made.
- It creates **new commits** on top of the existing commits that comepletely undo all of the changes, so our history remains intact.

```bash
# revert a commit
git log
# Get hash of the commit you want to undo
git revert <hash>
# Save exit

# Latest commit shows 'Revert "Commit Message"'
git log

```

### `git diff`

- Shows the difference between

```bash
git diff d63a377c d63a37890
```

## Example: pushed commit to the wrong branch

```bash
# 1. See what's on the current (wrong) branch and confirm the commit that needs to move
git status
git log --oneline -3

# 2. Fetch to make sure the target branch ref is up to date locally
git fetch origin

# 3. Create a local branch tracking the *correct* remote branch
git checkout -b fix/missing-abstract-csv-322 origin/fix/missing-abstract-csv-322

# 4. Cherry-pick the misplaced commit onto the correct branch
git cherry-pick <commit-sha>

# 4a. If conflicts occur, resolve them in the flagged files, then:
git add <resolved-file>
git cherry-pick --continue
# (git cherry-pick --abort would bail out instead, if needed)

# 5. Push the corrected branch
git push origin fix/missing-abstract-csv-322

# 6. Switch back to the branch that had the mistaken commit
git checkout feat/csv-converter-cli-config

# 7. Revert the commit there (adds a new "revert" commit — does NOT rewrite history)
git revert --no-edit <commit-sha>

# 8. Sanity-check the branch is back to its pre-mistake state
git diff <last-good-commit> HEAD -- <affected-files>   # should be empty

# 9. Push the revert
git push origin feat/csv-converter-cli-config
```

## Example: switch to remote branch locally

```bash
git fetch origin                    # make sure you have the latest remote refs
git checkout <branch-name>          # e.g. git checkout fix/218-verbatim-text-output

```
If the branch doesn't exist locally yet, modern git (2.23+) will auto-create a local tracking branch from origin/<branch-name> as long as the name is unambiguous — so git checkout <branch-name> alone usually just works. That's shorthand for:

```git checkout -b <branch-name> origin/<branch-name>```

Use the explicit -b ... origin/... form when you want to be unambiguous, or when the short form fails (e.g. name collision with a local branch, or git version too old for the auto-tracking shortcut).

If you're using the newer worktree-style syntax, ```git switch <branch-name>``` is the more modern equivalent of git checkout <branch-name> for this purpose.



| checkout | switch |
|----------|----------|
| git checkout < branch >    | git switch < branch >   |
| git checkout -b < new >    | git switch -c < new >   |

# Example untrack a file

```
git rm --cached notebooks/config.env
```