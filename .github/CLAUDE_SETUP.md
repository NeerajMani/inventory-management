# Claude AI PR Review Setup Guide

This guide explains how to set up automated PR reviews using Claude API.

## Prerequisites

- Anthropic API key
- GitHub repository with Actions enabled

## Step 1: Get Your Anthropic API Key

### Option 1: Via Anthropic Console (Recommended)
1. Go to: https://console.anthropic.com/
2. Sign in or create an account
3. Navigate to **API Keys** section
4. Click **"Create Key"**
5. Copy the key (starts with `sk-ant-api...`)

### Option 2: Via Anthropic Dashboard
1. Visit: https://www.anthropic.com/api
2. Sign up for API access
3. Generate an API key

**Important:** Save this key securely - you can't view it again!

## Step 2: Add API Key to GitHub Secrets

### Via GitHub Web UI
1. Go to your repository: `https://github.com/NeerajMani/inventory-management`
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **"New repository secret"**
4. Name: `ANTHROPIC_API_KEY`
5. Value: Paste your API key (e.g., `sk-ant-api03-...`)
6. Click **"Add secret"**

### Via GitHub CLI (Alternative)
```bash
gh secret set ANTHROPIC_API_KEY --body "your-api-key-here"
```

## Step 3: Test the Setup

### Create a Test PR
```bash
# Create a test branch
git checkout -b test-claude-review

# Make a small change
echo "# Test change" >> README.md
git add README.md
git commit -m "test: trigger Claude review"
git push origin test-claude-review

# Create PR
gh pr create --title "Test Claude Review" --body "Testing automated AI review"
```

### What Happens Next
1. GitHub Action triggers automatically
2. Claude analyzes the PR diff
3. Posts review comment within 30-60 seconds
4. Review includes:
   - Summary of changes
   - Strengths
   - Issues (if any)
   - Suggestions
   - Recommendation (Approve/Request Changes/Comment)

## Step 4: Verify the Workflow

Check the Actions tab:
```
https://github.com/NeerajMani/inventory-management/actions
```

You should see:
- ✅ "Claude PR Review" workflow running
- ✅ Review comment posted on PR

## Workflow Triggers

The review runs on:
- New PRs to `main` branch
- New commits pushed to existing PRs
- Reopened PRs

## Configuration

### Adjust Review Prompt
Edit `.github/workflows/claude-pr-review.yml`:
- Line ~45: Modify the prompt to Claude
- Customize review criteria
- Change tone (strict/lenient)

### Change Claude Model
Edit line 30:
```json
"model": "claude-sonnet-4-20250514"
```

Available models:
- `claude-opus-4-20250514` - Most capable (slower, expensive)
- `claude-sonnet-4-20250514` - Balanced (recommended)
- `claude-haiku-4-20250514` - Fast and affordable

### Adjust Token Limits
Edit line 31:
```json
"max_tokens": 4096  // Increase for longer reviews
```

## Costs

**Approximate costs per review:**
- Claude Sonnet 4: ~$0.01-0.05 per review
- Claude Opus 4: ~$0.05-0.20 per review
- Claude Haiku 4: ~$0.001-0.01 per review

*Costs vary based on PR size and complexity*

## Troubleshooting

### Issue: "ANTHROPIC_API_KEY not set"
**Fix:** Add the secret in GitHub Settings (Step 2)

### Issue: "API call failed"
**Fix:** Check:
1. API key is valid
2. You have API credits
3. API key has correct permissions

### Issue: "Review too short/generic"
**Fix:** Edit the prompt in the workflow file to be more specific

### Issue: "PR diff too large"
**Fix:** The workflow auto-truncates to 50KB. For larger PRs:
1. Split into smaller PRs
2. Increase truncation limit (line ~38)
3. Use Claude Opus for larger context

## Security Notes

✅ **API key is secure**
- Stored as encrypted GitHub secret
- Never exposed in logs
- Only accessible to workflow

✅ **Permissions**
- Workflow has minimal required permissions
- Read-only access to code
- Write access only for comments

## Disable/Enable

### Temporarily Disable
Add `[skip ci]` to commit message:
```bash
git commit -m "feat: update UI [skip ci]"
```

### Permanently Disable
Delete or rename the workflow file:
```bash
mv .github/workflows/claude-pr-review.yml .github/workflows/claude-pr-review.yml.disabled
```

## Support

- Anthropic API Docs: https://docs.anthropic.com/
- GitHub Actions Docs: https://docs.github.com/actions
- Issues: Open an issue in this repo

---

**Happy reviewing! 🤖✨**
