
# Github debugging

## Keep asking for ssh passphrase
SSH Agent May Not Caching the Key:
Your SSH agent might not be caching your key, requiring you to enter the passphrase repeatedly. You can add your key to the SSH agent to avoid having to enter the passphrase every time you use the key. Here’s how you can do it:
```bash
eval "$(ssh-agent -s)"  # Start the ssh-agent in the background
ssh-add ~/.ssh/id_ed25519  # Add your SSH private key to the ssh-agent
```
After running these commands, your SSH key should be added to the SSH agent, and you should no longer be prompted for the passphrase when using the key.

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

## Can't push to remote repository
### local repo is out-of-date
**We need to `pull` first**

1. Merging
This is the default behavior for git pull without specifying any configuration. It fetches the remote branch and then merges it into your local branch. Merging creates a merge commit in your history which shows where two divergent branches were brought together.

To pull with a merge:

```bash
git pull --no-rebase origin main
```

2. Rebasing
Rebasing rewrites your local changes to be on top of what is on the remote. This often makes the history cleaner as it appears as if all the changes were made in a linear sequence.

To pull with a rebase:

```bash
git pull --rebase origin main
```

3. Fast-Forward Only
If you prefer to avoid creating additional merge commits and want to ensure that your local changes create a linear history with the remote branch (assuming your changes could be applied directly on top of the base branch without divergence), you can opt for a fast-forward merge. This option will fail if there's no clear path to fast-forward, meaning it can't apply your local changes directly on top of the remote changes without needing a merge.

To pull with fast-forward only:

```bash
git pull --ff-only origin main
```

Setting a Default Pull Behavior

If you find yourself preferring one method over the others, you can set a default configuration for your repository or globally for all your Git repositories. Here's how to set each as a default:

Set merge as the default:

```bash
git config pull.rebase false
# Or globally
git config --global pull.rebase false
```

Set rebase as the default:

```bash
git config pull.rebase true
# Or globally
git config --global pull.rebase true
```

Set fast-forward only as the default:

```bash
git config pull.ff only
# Or globally
git config --global pull.ff only
```

By setting one of these configurations, future git pull commands will default to the behavior you've specified, eliminating the need to specify how to handle divergent branches each time. After pulling and resolving any conflicts (if necessary), you should be able to push your changes to the remote repository without any further issues.

