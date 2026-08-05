## Merge

- https://www.youtube.com/watch?v=0chZFIZLR_0
- https://www.youtube.com/shorts/KiMsYF8fff8



## Git: Merge vs Rebase

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
## Key Rules
- ✅ Solo on branch → rebase + force push is fine
- ✅ Use `--force-with-lease` instead of `--force` (safer)
- ❌ Never rebase a branch others are committing on top of
- ⚠️  If others are building on your branch → use merge instead

