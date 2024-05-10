
# Github debugging

## .gitignore not working
### rm cache
```bash
git rm -r --cached [files/folders]
```

### Additional cleaning
If the large files were previously committed to history and you need to reduce your repository size or ensure they are removed from all history (for example, if they contain sensitive information), you might need to rewrite history using git filter-branch, or use a tool like BFG Repo-Cleaner. This is especially important if your repository is close to or exceeding size limits on platforms like GitHub.

Here's a simple way to use git filter-branch to remove a folder from all commits:

```bash
git filter-branch --force --index-filter \
"git rm --cached -r --ignore-unmatch 3_result/figures" \
--prune-empty --tag-name-filter cat -- --all
```
After using git filter-branch, force push to your repository:

```bash
git push origin --force --all
```

Note on Using git filter-branch

Rewriting history with git filter-branch can affect all collaborators. They will need to rebase their work on top of the updated history. Communicate with your team before performing such actions.

By following these steps, you should be able to remove the large files from your repository, clean up your commit history if necessary, and prevent such files from being tracked in the future.