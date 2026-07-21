# Pathlib Notes

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

## Creating and Checking Paths

```python
# create a path (doesn't create the file yet)
new_file = my_dir / "new_file.txt"

new_file.exists()    # does it exist?
new_file.absolute()  # get absolute path
new_file.resolve()   # preferred — usually resolve paths instead of just joining them
```

## Home Directory

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

## Opening Files

```python
# Pathlib's built-in open
with dotfiles.open() as f:
    print(f.read())

# Equivalent using the standard open()
with open(dotfiles) as f:
    print(f.read())
```

## Creating, Moving, and Deleting

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

## When NOT to Use Pathlib

Pathlib is great for representing and manipulating **paths**, but for actual
filesystem operations beyond the basics, reach for `os` or `shutil` instead:

- Getting/setting environment variables → `os`
- Copying files → `shutil`
- Deleting non-empty folders (recursively) → `shutil.rmtree`