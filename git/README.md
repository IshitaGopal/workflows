# Git


## Configuring your Git profile

You need to configure your git client so your commits are attributed to you

```
# How the current git configuration look like
git config --list

# How the current git configuration look like for a particular repo

git config --list --local

# To view the actual file
cat .git/config

```

```
git config --global user.name "Ishita Gopal"
git config --global user.email "ishitagopal@gmail.com"
```

- `--global`: Applies the configuration **globally** on this computer. Meaning, whenever you make a commit in *any* Git Repo on your computer, Git will record that commit as being authored by ishitagopal@gmail.com

- **without** `--global`: If you want to set it only for one repository (not globally),

```
git config user.email "ishitagopal@gmail.com"
```

## Set up SSH key-based authentication for GitHub
- This controls how your computer securely connects to GitHub’s servers when you push, pull, or clone code.

- SSH authentication setup is completely separate from the ```git config``` setup which controls how your name and email appear in your commits.

- You need to create a pair of keys - public and private - and copy the public key into Github. Got to Settings >> SSH and GPG keys >> New SSH Key

```
# Generate the SSH key pair
ssh-keygen

# Navigate to the SSH directory
cd ~/.ssh

# View (copy) your public key
cat id_rsa.pub

# Add the key to Github and test the connection
ssh -T git@github.com
```

**Note:** *The test should return: Hi IshitaGopal! You've successfully authenticated, but GitHub does not provide shell access.*



## Initializing a Git repository

**Option 1:** Make a new dir and then initalize it as a git repo
```
mkdir xyz
cd xyz
git init
```
**Option 2:** If using uv, it will initalize automatically when you create a project

```
uv init xyz
```

**Option 3:** When contributing to an already existing repo:

```
git clone path/to/repo

```


## Typical workflow

1. Make sure you have the latest copy of the repo.

```
git pull
```

2. Tell git which files have changed and should be added in the next snapshot. It is good practice to add a single file at a time.

```
git add {filename}
 ```

3. Take a snapshot. Good commit messages lead to a usable git log. **If applied this commit will... Eg: "Add a sum function"**

```
git commit -m "a meaningful but short message describing the changes you made"
```

4. Make the snapshot available in the remote repo, so others can see it
```
git push
```

## But before you can push, attach your local repo to GitHub
1. **Eg: Create a new repository called workflows:** Go to GitHub → click New Repository → name it workflows → don’t initialize with README (if you already have a local repo).

2. **Add the remote repository in your local repository:** This uploads your local commits to the remote repository. The remote is named "origin" by convention.
    ```
    git remote add origin https://github.com/username/workflows.git
    ```
3. **Check the remote repository:**
    ```
    git remote -v
    ```
4. **Push the local change:** Takes a copy of my repository and pushes it to GitHub.
    ```
    git push -u origin main
    ```
    - `-u`: link local branch → remote branch (upstream).
    - `origin`: The default nickname for the remote repository. Without it, you’d have to type the full repo URL each time
    - `main`: The branch you’re pushing (In this case, main)

    This creates a tracking relationship:
    - main ↔ origin/main
    - After this, you can just run: `git push` (no need to specify `main`branch again)
    - Every new branch needs the `-u` step once:
    ```
    # 1st time pushing branch
    git push -u origin new-feature

    # After that
    git push
    ```
## Git fetch and pull

`git fetch origin`:
- **Downloads** the latest changes from the remote (e.g. GitHub) into your local repo
- **Does NOT change** your local files
- After fetching, you can inspect changes:
    ```
    git log origin/main
    git diff origin/main
    ```

`git pull`: Fetches and immediately merges/rebases
- **Fetches changes AND merges** them into your current branch. Updates my code immediately.

`git pull --rebase`
1. Fetches changes from the remote
2. Temporarily removes your local commits
3. Applies remote changes first
4. Re-applies your commits on top

## Remove files and directories

Delete a file or director from the repo. Removing files via git allows them to be recovered.

```
git rm {filename}
git rm -r {directory}
```
## Get logs
To just get logs or just titles of commit messages
```
git log
git log --oneline
git log --oneline --graph --all --decorate
```

## Renaming files

```
git mv OLD NEW
```

## Remove remote
```
git remove origin
```

## .gitignore
This file will allow us to tell git which files/directories we don't want to be part of the repo ever. This is useful for keeping credentials, data etc. out of your github repo.


## GitHub flow

- https://www.youtube.com/watch?v=hG_P6IRAjNQ


## Merge

- https://www.youtube.com/watch?v=0chZFIZLR_0
- https://www.youtube.com/shorts/KiMsYF8fff8



### Merge vs rebase

- **Rebase** = clean linear history, use on your own local/feature branches
- **Merge** = preserves full history, use on shared branches

- **Note**: When you rebase, your local history diverges from the remote, so a normal git push gets rejected. You need to force push to overwrite the remote.

    `--force` → overwrites the remote no matter what

    `--force-with-lease` → overwrites the remote only if no one else has pushed to the branch since your last fetch

```
# Common Workflow (feature branch off main)
# Before first push — main moves forward:
git fetch origin
git rebase origin/main
git push origin your-feature-branch

# After first push — main moves forward again:
git fetch origin
git rebase origin/main
git push --force-with-lease origin your-feature-branch

# If others are working on your branch — main moves forward:
git fetch origin
git merge origin/main
git push origin your-feature-branch

```
### Key rules
- ✅ Solo on branch → rebase + force push is fine
- ✅ Use `--force-with-lease` instead of `--force` (safer)
- ❌ Never rebase a branch others are committing on top of
- ⚠️  If others are building on your branch → use merge instead


## Stash
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
