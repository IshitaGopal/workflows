# Pathlib

## Basics

```python
from pathlib import Path

# Get the parent directory of the current file's directory
Path(__file__).resolve().parent.parent

my_dir = Path("scripts")
my_file = Path("notebooks/irr.ipynb")

my_file.name     # 'irr.ipynb'
my_file.stem     # 'irr'
my_file.suffix   # '.ipynb'
my_file.parent   # get parent dir -> Path('notebooks')
```

## Creating and checking paths

```python
# create a path (doesn't create the file yet)
new_file = my_dir / "new_file.txt"

new_file.exists()    # does it exist?
new_file.absolute()  # get absolute path
new_file.resolve()   # preferred — usually resolve paths instead of just joining them
```

## Home directory

```python
# Don't do this — won't expand "~" correctly:
Path("~/.ssh").resolve()

# Instead, use expanduser() for existing paths with "~":
Path("~/.ssh").expanduser()

# Better — when building a path from scratch, start from Path.home():
dotfiles = Path.home() / "dotfiles.txt"
```

> Use `expanduser()` when working with a path that already contains `~`.
> Use `Path.home()` when constructing a new path from scratch.

## Searching with glob

```python
# glob — search current directory
for p in dotfiles.glob("*vscode*"):
    print(p)

# rglob — recursive glob, searches subdirectories too
for p in dotfiles.rglob("*vscode*", case_sensitive=False):
    print(p)

for p in dotfiles.rglob("*.json", case_sensitive=False):
    print(p)
```

## Opening files

```python
# Pathlib's built-in open
with dotfiles.open() as f:
    print(f.read())

# Equivalent using the standard open()
with open(dotfiles) as f:
    print(f.read())
```

## Creating, moving, and deleting

Pathlib has *limited* ability to create, move, and delete files/dirs.

```python
# Create a directory
p = Path("TempDir")
p.mkdir(exist_ok=True)

p.rmdir()  # delete dir — only works on EMPTY directories

# Create nested subdirectories
p = Path("TempDir/SubDir")
p.mkdir(parents=True)

# Create a file (be careful — this can overwrite/reset timestamps)
p = Path("TempFile.txt")
p.touch()

# Rename
p.rename("tempfile.txt")

# Replace — safer alternative to rename (atomic, cross-platform overwrite)
p.replace("temp_file.txt")

# Remove a file
p = Path("temp_file.txt")
p.unlink()
```

## When NOT to use pathlib

Pathlib is great for representing and manipulating **paths**, but for actual
filesystem operations beyond the basics, reach for `os` or `shutil` instead:

- Getting/setting environment variables → `os`
- Copying files → `shutil`
- Deleting non-empty folders (recursively) → `shutil.rmtree`



<!-- # # Pathlib
# Path(__file__).resolve().parent().parent()

# my_dir = Path("scripts")
# my_file = Path("notebooks/irr.ipynb")
# my_dir.name
# my_dir.suffix  # '.ipynb'
# my_dir.name  # 'irr'
# new_file.parent  # get parent dir
# # create a file
# new_file = my_dir / "new_file.txt"
# # does it exist?
# new_file.exists()
# new_file.absolute()  # get absolute or resolve
# new_file.resolve()  # use this most of the times -- always usually want to resolve the paths instead of getting the PAth

# # When you want to access the home folder
# Path("~/.ssh").resolve()  # will not work well. Instaed use
# Path(
#     "~/.ssh"
# ).expanduser()  # Dont use when creating from scratch only if the file exists
# # instead use:
# dotfiles = Path.home() / "dotfiles.txt"

# # search using glob
# for p in dotfiles.glob("*vscode*"):
#     print(p)

# # recursive glob = rglob
# for p in dotfiles.rglob("*vscode*", case_sensitive=False):
#     print(p)

# for p in dotfiles.rglob("*.json", case_sensitive=False):
#     print(p)

# # open file
# with dotfiles.open() as f:
#     print(f.read())


# with open(dotfiles) as f:
#     print(f.read())
 -->

<!-- # # Limited ability to create move and delete
# p = Path("TempDir")
# p.mkdir(exist_ok=True)
# p.rmdir()  # delete dir - only removes empty dirs

# # make sub dir
# p = Path("TempDir/SubDir")
# p.mkdir(parents=True)

# # Create file
# # Over writes be carefule
# p = Path("TempFile.txt")
# p.touch()
# # Rename
# p.rename("tempfile.txt")
# p.replace("temp_file.txt")  # better

# # remove a file
# p = Path("temp_file.txt")
# p.unlink()

# # When to not use Pathlib - shutil, os
# # use Path only for paths
# # getting env variables
# # copying files
# # deleting folders -->
