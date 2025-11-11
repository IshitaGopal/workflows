# Git

## Configuring your Git Profile

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

- ```--global``` -> Applies the configuration **globally** on this computer. Meaning, whenever you make a commit in *any* Git Repo on your computer, Git will record that commit as being authored by ishitagopal@gmail.com

- So, if you want to set it only for one repository (not globally), you’d run the following in the repo folder without ```--global```

```
git config --global user.email "ishitagopal@gmail.com"
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

**Notes:**
- *The test should return: Hi IshitaGopal! You've successfully authenticated, but GitHub does not provide shell access.*



## Initializing a git repository

**Option 1:** Make a new dir and then initalize it as a git repo
```
# 
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

## Attach your local repo to GitHub 
1. Create a new repository called workflows. Go to GitHub → click New Repository → name it workflows → don’t initialize with README (since you already have a local repo).

2. Add the remote repository in your local repository. This uploads your local commits to the remote repository. The remote is named "origin" by convention.
```
git remote add origin https://github.com/username/workflows.git
```
3. Check the remote repository
```
git remote -v
```
4. Push the local changes on our repository to the GitHub repository. Takes a copy of my repository and pushes it to GitHub.
```
git push -u origin main

```
- -u sets origin/main as the default upstream branch, so future pushes are simpler.

5. Once the upstream is set, when you push changes to GitHub in the future you simply run:
```
git push

```


## Typical Workflow 

1. Make sure you have the latest copy of the repo.

```
git pull 
```

2. Tell git which files have changed and should be added in the next snapshot. It is good practice to add a single file at a time.

```
git add {filename}
 ```

3. Take a snapshot. Good commit messages lead to a usable git log. *If applied this commit will,* . Eg: "Add a sum function"

```
git commit -m "a meaningful but short message describing the changes you made"
```

4. Make the snapshot is in the remote repo, so others can see it
```
git push
```

## Additional Commands 

- Delete a file or director from the repo. Removing files via git allows them to be recovered.

```
git rm {filename}
git rm -r {directory}
```

- To just get logs or just titles of commit messages 
```
git log
git log --oneline
git log --oneline --graph --all --decorate
```

- Renaming files 

```
git mv OLD NEW

```




## .gitignore 
This file will allow us to tell git which files/directories we don't to be part of the repo ever. This is useful for keeping credentials, data etc. out of your github repo.


