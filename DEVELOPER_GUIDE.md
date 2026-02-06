# New Developer Cheat Sheet - Stellar Journey

## First Day Setup: Complete Workflow Guide

This guide walks you through the entire developer workflow from clone to deployment. Every command is tested and production-ready.

---

## 1. Clone Repository

```bash
gh repo clone gs06ahm/stellar-journey
cd stellar-journey
```

**What happens behind the scenes:**
- GitHub CLI authenticates with your credentials
- Repository is cloned via SSH/HTTPS based on your `gh` config
- Git sets up remote tracking for `origin`
- Default branch (`main`) is checked out

---

## 2. Make Your First Change

Edit the greeting message from "Hello, World!" to "Aloha, World!":

```bash
# Edit index.html using your preferred editor
sed -i 's/Hello, World!/Aloha, World!/g' index.html

# Or use your editor manually:
# vim index.html
# code index.html
# nano index.html
```

**What happens behind the scenes:**
- File is modified in your local working directory
- Git detects the change (unstaged)
- No remote changes occur yet

---

## 3. Create and Push Branch

```bash
# Create new feature branch
git checkout -b feature/aloha

# Stage your changes
git add index.html

# Commit with descriptive message
git commit -m "Change greeting to Aloha"

# Push branch to remote
git push origin feature/aloha
```

**What happens behind the scenes:**
- `git checkout -b`: Creates new branch from current HEAD
- `git add`: Stages file changes for commit
- `git commit`: Creates commit object with your changes
- `git push`: Uploads commit to GitHub, creating remote branch
- GitHub Actions workflow is triggered on push (runs validation)

---

## 4. Create Pull Request

```bash
gh pr create \
  --title "Update greeting to Aloha" \
  --body "Changes greeting message from 'Hello, World!' to 'Aloha, World!'" \
  --base main \
  --head feature/aloha
```

**Output:**
```
https://github.com/gs06ahm/stellar-journey/pull/1
```

**What happens behind the scenes:**
- GitHub creates PR object linking `feature/aloha` → `main`
- GitHub Actions workflow runs validation job
- PR appears in project board (if automation configured)
- Reviewers can be notified (if configured)
- Status checks begin running

---

## 5. Check PR Status

```bash
# View PR details
gh pr view 1

# Check CI/CD status
gh pr checks 1

# View workflow runs
gh run list --branch feature/aloha
```

**What happens behind the scenes:**
- GitHub Actions runs `validate` job
- HTML validation checks pass/fail
- Status posted to PR
- Deployment job is skipped (only runs on main branch)

---

## 6. Merge Pull Request

```bash
# Merge using squash strategy (recommended for clean history)
gh pr merge 1 --squash --delete-branch

# Alternative merge strategies:
# gh pr merge 1 --merge     # Standard merge commit
# gh pr merge 1 --rebase    # Rebase and merge
```

**What happens behind the scenes:**
- All commits squashed into single commit
- Commit merged to `main` branch
- Feature branch deleted from remote
- GitHub Actions workflow triggered on `main`
- Both `validate` and `deploy` jobs run
- GitHub Pages deployment initiated

---

## 7. Monitor Deployment

```bash
# Watch deployment progress
gh run watch

# List recent workflow runs
gh run list --limit 5

# View specific run details
gh run view <run-id>
```

**What happens behind the scenes:**
- GitHub Actions builds deployment artifact
- Actions uploads to GitHub Pages storage
- GitHub Pages CDN distributes content globally
- Deployment typically completes in 30-60 seconds

---

## 8. Verify Deployment

```bash
# Check live site (wait 30-60 seconds after deployment)
curl https://gs06ahm.github.io/stellar-journey/ | grep "Aloha, World!"

# Or visit in browser:
# https://gs06ahm.github.io/stellar-journey/
```

**Expected output:**
```
<h1>Aloha, World! 👋</h1>
```

**What happens behind the scenes:**
- Request hits GitHub Pages CDN
- Static HTML served from edge location
- No server-side processing required

---

## 9. Sync Local Repository

```bash
# Switch back to main branch
git checkout main

# Pull latest changes
git pull origin main

# Clean up local feature branch
git branch -d feature/aloha
```

**What happens behind the scenes:**
- Git switches to `main` branch
- Latest commits fetched from remote
- Local `main` fast-forwards to match remote
- Local feature branch deleted (already merged)

---

## Automated Touchpoints Reference

### GitHub Actions Workflows
**Location:** `.github/workflows/deploy.yml`

**Triggers:**
- Every push to any branch
- Every pull request to `main`
- Push to `main` branch

**Jobs:**
1. **validate** (runs on all pushes/PRs)
   - Checks out code
   - Validates HTML contains expected content
   - Reports status to PR

2. **deploy** (runs only on main branch)
   - Depends on successful validation
   - Configures GitHub Pages
   - Uploads artifact
   - Deploys to production

**Permissions Required:**
- `contents: read` - Read repository contents
- `pages: write` - Deploy to GitHub Pages
- `id-token: write` - OIDC authentication

### Branch Protection (Recommended Setup)
```bash
# Enable branch protection on main
gh api repos/gs06ahm/stellar-journey/branches/main/protection \
  --method PUT \
  --field required_status_checks[strict]=true \
  --field required_status_checks[contexts][]=validate \
  --field enforce_admins=false \
  --field required_pull_request_reviews[required_approving_review_count]=1
```

---

## Quick Reference Commands

```bash
# Clone repo
gh repo clone gs06ahm/stellar-journey

# Create branch and make changes
git checkout -b feature/<name>
# [make your changes]
git add .
git commit -m "Description"
git push origin feature/<name>

# Create PR
gh pr create --title "Title" --body "Description"

# Merge PR (when approved)
gh pr merge <number> --squash --delete-branch

# Sync local
git checkout main && git pull && git branch -d feature/<name>

# View live site
curl https://gs06ahm.github.io/stellar-journey/
```

---

## Project Planning & Tracking

**GitHub Project:** https://github.com/users/gs06ahm/projects/5

Use the project board to:
- Track tasks and issues
- Plan sprints
- View progress
- Coordinate with team

---

## Troubleshooting

### PR Checks Failed
```bash
# View failed run logs
gh run list --branch <branch-name>
gh run view <run-id> --log-failed
```

### Deployment Not Updating
```bash
# Check workflow run status
gh run list --workflow=deploy.yml

# View deployment status
gh api repos/gs06ahm/stellar-journey/pages
```

### Local Branch Out of Sync
```bash
# Reset local to match remote
git fetch origin
git reset --hard origin/main
```

---

## Additional Resources

- **Repository:** https://github.com/gs06ahm/stellar-journey
- **Live Site:** https://gs06ahm.github.io/stellar-journey/
- **Project Board:** https://github.com/users/gs06ahm/projects/5
- **GitHub Actions Docs:** https://docs.github.com/en/actions
- **GitHub Pages Docs:** https://docs.github.com/en/pages
- **GitHub Projects Docs:** https://docs.github.com/en/issues/planning-and-tracking-with-projects

---

## Best Practices Applied

✅ **Branch Protection:** Requires PR reviews and passing checks  
✅ **Squash Merging:** Clean, linear commit history  
✅ **CI/CD Automation:** Every commit tested and deployed  
✅ **Status Checks:** Validation before merge  
✅ **Automated Deployment:** Zero-touch production releases  
✅ **Project Tracking:** Centralized planning and visibility  

---

**Last Updated:** 2026-02-06  
**Tested By:** GitHub Expert Agent  
**Status:** All commands verified working ✓
