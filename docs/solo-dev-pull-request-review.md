# Solo Developer Pull Request Review Workflow

## Overview

This guide explains the **AI Code Review GitHub Action** that automatically approves pull requests created by the repository owner (solo developer). This workflow streamlines the review process while maintaining quality standards.

---

## Workflow Trigger Events

The AI Code Review workflow activates when:
- A new PR is **opened**
- New **commits are pushed** to an existing PR (synchronize event)
- A PR is **reopened** (after being closed)

---

## Auto-Approval Conditions

A PR is **automatically approved** if ALL of the following conditions are met:

| Condition | Status |
|-----------|--------|
| PR created by `pluto-atom-4` | ✅ Required |
| PR is NOT in draft status | ✅ Required |
| PR title does NOT contain `[skip-review]` | ✅ Required |

**If any condition fails**, auto-approval is skipped (external PR, draft, or explicit skip).

---

## Step-by-Step Workflow

### Step 1: Validate Author

**Condition Check:**
```yaml
if: github.actor == 'pluto-atom-4' && github.event.pull_request.draft == false
```

- Verifies that the PR creator is the repository owner
- Confirms the PR is not in draft status
- Skips all remaining steps if either condition fails

**Output:**
```
🔐 Author Validation:
  Expected Owner: pluto-atom-4
  PR Author: pluto-atom-4
  ✅ Author is repository owner - eligible for auto-approval
```

### Step 2: Log PR Metadata

**Logged Information:**
- PR number
- PR author/creator
- Draft status (true/false)
- Event type (opened, synchronize, reopened)
- Timestamp (ISO 8601)
- PR title
- Branch information (source → target)
- File change count

**Output Example:**
```
📋 Pull Request Metadata:
  PR Number: #11
  Author: pluto-atom-4
  Draft Status: false
  Event Type: opened
  Timestamp: 2026-05-19T17:25:00Z
  Title: feat: Implement minimal docker-compose setup (Issue #10)
  Branch: feat/docker-compose-setup → main
```

### Step 3: Check for [skip-review] Tag

**Purpose:** Allow explicit opt-out of auto-approval for complex PRs requiring manual review.

**How to Skip Auto-Approval:**
1. Add `[skip-review]` tag to PR title
2. Create/update PR
3. Workflow detects tag and skips approval

**Examples:**
```
✅ Auto-approve: feat: Add new feature
❌ Skip approval: feat: Add new feature [skip-review]
```

### Step 4: Check Changed Files

**Information Gathered:**
- Count of changed files
- List of file paths (visible in workflow logs)
- Diff statistics

**Purpose:** Provide context for review and logging.

### Step 5: Validate Code Quality

**Checks Performed:**
- ✅ Author verified (solo developer)
- ✅ Not a draft PR
- ✅ File changes detected
- ℹ️ CI/CD checks run separately (linting, tests, builds)

**Note:** This step is informational; full CI/CD validation runs in separate GitHub Actions workflows.

### Step 6: Create Approval Review

**GitHub API Call:**
```javascript
github.rest.pulls.createReview({
  owner: repo.owner,
  repo: repo.repo,
  pull_number: pr.number,
  event: 'APPROVE',
  body: reviewBody
})
```

**Approval Message Includes:**
```
✅ AI Code Review Approved

**PR Details:**
- Number: #11
- Author: pluto-atom-4 (repository owner)
- Title: feat: Implement minimal docker-compose setup
- Branch: feat/docker-compose-setup → main
- Files Changed: 7

**Quality Checks:**
✅ Solo developer verification passed
✅ Not a draft PR
✅ Branch strategy validated

**Important:**
- ⚠️ All CI checks must pass before merge
- 📝 Manual code review may still be needed
- 🔄 Ready for manual merge when CI passes
```

### Step 7: Log Approval Summary

**Final Status Report:**
- ✅ Auto-approval completed (if approved)
- ⏭️ Auto-approval skipped (if skipped)
- Reason for skip (if applicable)

**Example Output:**
```
📊 Approval Summary:
  PR Author: pluto-atom-4
  Expected Owner: pluto-atom-4
  Author Matches: true
  Is Draft: false
  Skip Review Flag: false

✅ RESULT: Auto-approval completed successfully
   This PR was auto-approved by the solo developer workflow
   CI checks and manual review may still be required
```

---

## What Happens After Auto-Approval?

### 1. PR Receives Approval Review ✅
- The PR shows a green checkmark in the review section
- Approval is visible in the PR timeline

### 2. CI/CD Checks Still Run 🔄
**All automated checks must pass:**
- Type checking (TypeScript/C#)
- Linting & code formatting
- Unit tests (backend + frontend)
- Integration tests
- Build verification
- GraphQL schema validation

### 3. Manual Merge Ready 🚀
Once CI checks pass, the PR is ready for manual merge via GitHub UI:
1. Navigate to PR #11
2. Click "Merge pull request"
3. Choose merge strategy (Squash, Merge, Rebase)
4. Confirm merge
5. Delete branch (optional but recommended)

---

## Skip Auto-Approval

### When to Use [skip-review]

Add `[skip-review]` to the PR title if:
- Complex architectural changes requiring manual review
- Significant refactoring with subtle behavior changes
- Security-related changes
- Database schema modifications
- API contract changes

### How to Skip

**Create PR with [skip-review] tag:**
```bash
gh pr create --title "feat: Complex change [skip-review]"
```

**Or update existing PR title:**
1. Open PR on GitHub
2. Click edit on title
3. Add `[skip-review]` at the end
4. Save changes

**Workflow Response:**
```
⏭️  RESULT: Auto-approval skipped
   Reason: [skip-review] tag detected in PR title
```

---

## Error Handling & Fallback

### If Approval Fails

If the GitHub API call to create a review fails:

1. **Workflow logs the error** — Full error message captured
2. **Workflow does NOT fail** — Continues to completion
3. **PR remains open** — Can be manually approved/merged
4. **No blocking issues** — Developer can investigate logs

**Example Error Handling:**
```
❌ Error creating approval review: Resource not accessible by integration
   Details: Insufficient permissions to approve pull request
   ⚠️ Workflow continues despite review creation failure
```

### Debugging Tips

Check workflow logs if auto-approval doesn't occur:

1. Navigate to **GitHub → Actions → AI Code Review**
2. Click the failed/skipped workflow run
3. Expand the step logs
4. Look for error messages or skip reasons

---

## Security Considerations

### Access Control

- ✅ **Only approves PRs from `pluto-atom-4`** — Prevents external abuse
- ✅ **Skips draft PRs** — Incomplete work not accidentally approved
- ✅ **Uses GITHUB_TOKEN** — Automatic, no external secrets required
- ✅ **Logs all decisions** — Audit trail for every approval/skip

### CI Still Required

**Auto-approval does NOT bypass:**
- ❌ Linting failures
- ❌ Test failures
- ❌ Type errors
- ❌ Build failures

All CI checks must pass before merge.

---

## Example: Full Workflow Run

### Scenario: Creating PR #11

```bash
# Create and push feature branch
git checkout -b feat/docker-compose-setup
# ... make changes ...
git commit -m "feat: Implement minimal docker-compose"
git push origin feat/docker-compose-setup

# Create PR
gh pr create --base main --head feat/docker-compose-setup \
  --title "feat: Implement minimal docker-compose setup (Issue #10)"
```

### AI Code Review Workflow Runs:

```
✅ Step 1: Checkout Repository
✅ Step 2: Log PR Metadata
   PR #11 | Author: pluto-atom-4 | Draft: false | Title: feat: ...

✅ Step 3: Check Changed Files
   Files changed: 7

✅ Step 4: Validate Code Quality
   Author verified ✅ | Not draft ✅ | Files detected ✅

✅ Step 5: Create Approval Review
   Submitting approval review to GitHub API...
   Review ID: PR_kwDOL5bNs84ABCDE
   Status: APPROVED
   URL: https://github.com/.../pull/11#pullrequestreview-...

✅ Step 6: Log Approval Summary
   RESULT: Auto-approval completed successfully
   This PR was auto-approved by the solo developer workflow
```

### Then:

1. **CI/CD runs** — All checks must pass
2. **Developer reviews logs** — Ensures nothing unusual
3. **Manual merge** — Developer merges via GitHub UI
4. **Branch deleted** — Cleanup

---

## FAQ

### Q: Why auto-approve if CI must still pass?
**A:** Auto-approval confirms the PR structure is valid (owner-created, not draft). CI ensures code quality. Both are needed.

### Q: Can external contributors use this?
**A:** No. The workflow explicitly checks `github.actor == 'pluto-atom-4'`. Only the owner is auto-approved.

### Q: What if I want manual review of my own PR?
**A:** Add `[skip-review]` to the title. The workflow skips approval, allowing manual review.

### Q: Does auto-approval bypass tests?
**A:** No. Auto-approval and CI checks are independent. CI still runs and must pass before merge.

### Q: Where do I see the approval?
**A:** In the PR on GitHub:
1. Click the PR number (e.g., #11)
2. Scroll to "Reviews" section
3. See the approval with the AI Code Review message

### Q: Can I undo an auto-approval?
**A:** Yes. Dismiss the review via the GitHub UI:
1. In the Reviews section
2. Click "Dismiss review" on the approval
3. This removes the approval without deleting the review

---

## Workflow File Reference

**File Location:** `.github/workflows/ai-code-review.yml`

**Key Configuration:**
```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: read
  pull-requests: write

jobs:
  ai-review:
    if: github.actor == 'pluto-atom-4' && github.event.pull_request.draft == false
```

**Customization:**
- **Change owner:** Edit `github.actor == 'pluto-atom-4'`
- **Change branches:** Add `branches: [main, develop]` to `on.pull_request`
- **Change events:** Modify `types: [opened, synchronize, reopened]`

---

## Related Documentation

- **[CONTRIBUTING.md](../CONTRIBUTING.md)** — Full contribution guidelines
- **[README.md](../README.md)** — Project overview
- **[docs/SETUP.md](./SETUP.md)** — Local development setup

---

**Last Updated:** 2026-05-19  
**Status:** Active  
**Owner:** Pluto Atom (@pluto-atom-4)
