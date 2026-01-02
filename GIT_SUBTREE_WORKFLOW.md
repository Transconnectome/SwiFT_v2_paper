# Git Subtree Workflow for SwiFT_v2 Paper Repository

## Overview
This document describes the git subtree workflow used to synchronize the `manuscript/` directory with the separate paper repository for Overleaf integration.

## Repository Configuration

**Main Repository**: https://github.com/Transconnectome/SwiFT_v2.git
- Contains the full SwiFT_v2 codebase
- Includes `manuscript/` directory for paper-related files

**Paper Repository**: https://github.com/Transconnectome/SwiFT_v2_paper.git
- Contains only the contents of `manuscript/` directory
- Connected to Overleaf for collaborative paper writing
- Synced using git subtree

## Initial Setup (Already Completed)

The paper repository remote has been configured:

```bash
cd /Users/apple/Desktop/swift-agent-system/SwiFT_v2
git remote add paper https://github.com/Transconnectome/SwiFT_v2_paper.git
git fetch paper
```

Verify remotes:
```bash
git remote -v
# Should show:
# origin  https://github.com/Transconnectome/SwiFT_v2.git
# paper   https://github.com/Transconnectome/SwiFT_v2_paper.git
```

## Pushing Updates to Paper Repository

When you've made changes to files in the `manuscript/` directory:

1. Commit changes in the main repository:
   ```bash
   git add manuscript/
   git commit -m "Your descriptive commit message"
   ```

2. Push the manuscript subtree to the paper repository:
   ```bash
   git subtree push --prefix manuscript paper main
   ```

This command:
- Extracts the history of the `manuscript/` directory
- Creates commits in the paper repository with only manuscript files
- Pushes to the `main` branch of the paper repository

## Pulling Updates from Paper Repository

If changes are made in Overleaf (via the paper repository), pull them back:

```bash
git subtree pull --prefix manuscript paper main --squash
```

The `--squash` flag:
- Combines all paper repository changes into a single commit
- Keeps the main repository history cleaner
- Recommended for most workflows

## Common Workflows

### Workflow 1: Adding New Figures
```bash
# 1. Add figure to manuscript/figures/
cp Figure1.png manuscript/figures/

# 2. Commit in main repository
git add manuscript/figures/Figure1.png
git commit -m "Add Figure1: SwiFT_v2 architecture overview"

# 3. Push to paper repository
git subtree push --prefix manuscript paper main
```

### Workflow 2: Updating LaTeX Files in Overleaf
```bash
# After editing in Overleaf, pull changes:
git subtree pull --prefix manuscript paper main --squash

# Review changes
git log -1

# If satisfied, push to main repository
git push origin final_heehwan  # or your current branch
```

### Workflow 3: Syncing Both Directions
```bash
# 1. Pull latest from paper repository
git subtree pull --prefix manuscript paper main --squash

# 2. Make local changes
# ... edit files ...

# 3. Commit locally
git add manuscript/
git commit -m "Update paper content"

# 4. Push back to paper repository
git subtree push --prefix manuscript paper main

# 5. Push to main repository
git push origin final_heehwan
```

## Current Status (as of 2026-01-02)

- Paper remote configured: ✓
- Initial subtree push completed: ✓
- Figure1.png successfully pushed: ✓
- Paper repository contains all manuscript files including:
  - drafts/ (LaTeX templates, bibliography)
  - figures/ (Figure1.png and assets)
  - papers/ (reference papers)
  - README.md, OVERVIEW.md, QUICK_START.md

Latest commit in paper repository:
```
794a987 Add manuscript figures and drafts including Figure1.png
```

## Troubleshooting

### Issue: "fatal: ambiguous argument 'paper/main'"
**Solution**: Fetch the paper repository first
```bash
git fetch paper
```

### Issue: "Updates were rejected because the tip of your current branch is behind"
**Solution**: Pull before pushing
```bash
git subtree pull --prefix manuscript paper main --squash
git subtree push --prefix manuscript paper main
```

### Issue: Merge conflicts during pull
**Solution**: Resolve conflicts manually
```bash
git subtree pull --prefix manuscript paper main --squash
# Resolve conflicts in manuscript/ files
git add manuscript/
git commit
```

## Best Practices

1. **Always commit manuscript changes before subtree push**
   - Ensures all changes are tracked in main repository

2. **Use descriptive commit messages**
   - Helps track what changed in both repositories

3. **Pull before major edits**
   - Prevents merge conflicts from Overleaf changes

4. **Test in Overleaf after pushing**
   - Verify figures and LaTeX compile correctly

5. **Keep .gitignore updated**
   - Exclude auxiliary LaTeX files (.aux, .log, .bbl, etc.)
   - Current .gitignore handles most cases

## Integration with Overleaf

The paper repository is connected to Overleaf:

1. Overleaf syncs with paper repository automatically
2. Changes in Overleaf push to paper repository
3. Pull from paper repository to get Overleaf changes
4. Push to paper repository to update Overleaf

**Sync Flow**:
```
Local (manuscript/)
  ↓ git subtree push
Paper Repository (GitHub)
  ↔ automatic sync
Overleaf Project
```

## Future Maintenance

### Adding New Collaborators
Share the paper repository URL with collaborators:
- GitHub: https://github.com/Transconnectome/SwiFT_v2_paper.git
- They can clone and work directly in the paper repository
- Or connect it to their own Overleaf

### Archiving Paper Versions
Tag important versions in paper repository:
```bash
git tag -a v1.0-submission -m "Initial submission version"
git push paper v1.0-submission
```

### Checking Sync Status
```bash
# See what's in paper repository
git ls-tree -r --name-only paper/main

# Compare with local manuscript
diff <(git ls-tree -r --name-only paper/main) \
     <(git ls-files manuscript/ | sed 's|^manuscript/||')
```

## References

- Git Subtree Documentation: https://git-scm.com/docs/git-subtree
- Overleaf Git Integration: https://www.overleaf.com/learn/how-to/Git_integration
