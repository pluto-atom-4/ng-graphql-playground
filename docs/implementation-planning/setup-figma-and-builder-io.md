# Setup Guide: Figma Workspace & Builder.io Account

**Purpose**: External account setup for design-to-code workflow (Issue #9, Phase 1)  
**Time**: ~15 minutes  
**Prerequisites**: GitHub account, valid email address

---

## Overview

The design-to-code pipeline requires two external accounts:

1. **Figma** - Visual design source for UI components
2. **Builder.io** - AI-powered code generation engine

This guide walks through setting up both and configuring GitHub Secrets for automation.

---

## Part 1: Figma Workspace Setup

### Step 1.1: Create Figma Account

1. Go to [figma.com](https://figma.com)
2. Click **"Sign up"** (top right)
3. Enter email and password (or use Google/SSO)
4. Verify email (check inbox)
5. Complete onboarding

**You now have a Figma account.**

---

### Step 1.2: Create Design File

1. Go to [figma.com/files](https://figma.com/files)
2. Click **"New file"** button
3. Name it: `ng-graphql-playground-components`
4. Click **"Create file"**

**A blank design file opens.**

---

### Step 1.3: Create Atomic Components

Create 5-10 simple atomic components for testing. Examples:

#### Button Component

1. Insert → Rectangle (120px × 40px)
2. Add text: "Click Me"
3. Add padding, background color
4. Select all layers → Right-click → **"Create component"**
5. Name: `Button`

#### Card Component

1. Insert → Rectangle (300px × 200px)
2. Add rounded corners
3. Add shadow
4. Right-click → **"Create component"**
5. Name: `Card`

#### Input Field Component

1. Insert → Rectangle (200px × 40px)
2. Add border
3. Add placeholder text
4. Right-click → **"Create component"**
5. Name: `Input`

#### Badge Component

1. Insert → Rectangle with text (60px × 24px)
2. Right-click → **"Create component"**
3. Name: `Badge`

#### Progress Bar Component

1. Insert → Rectangle (300px × 8px)
2. Add fill gradient
3. Right-click → **"Create component"**
4. Name: `ProgressBar`

**You now have 5 reusable components in Figma.**

---

### Step 1.4: Get Figma API Token

1. Click your **profile icon** (bottom left)
2. Select **"Settings"**
3. Go to **"Personal access tokens"** tab
4. Click **"Generate a new token"**
5. Name it: `ng-graphql-playground-figma-token`
6. Copy the token (you won't see it again)
7. Paste it somewhere safe temporarily

**You now have a Figma API token.**

---

### Step 1.5: Get Figma File ID

1. Open your design file: `ng-graphql-playground-components`
2. Look at the URL: `figma.com/file/{FILE_ID}/...`
3. Copy the `FILE_ID` portion
4. Paste it somewhere safe temporarily

**You now have a Figma Project ID (file ID).**

---

## Part 2: Builder.io Account Setup

### Step 2.1: Create Builder.io Account

1. Go to [builder.io](https://builder.io)
2. Click **"Sign up"** (top right)
3. Enter email and password (or use GitHub/Google SSO)
4. Verify email
5. Choose **"Free Plan"** for testing

**You now have a Builder.io account.**

---

### Step 2.2: Get Builder.io API Token

1. Click your **profile icon** (top right)
2. Select **"Account settings"**
3. Go to **"API tokens"** tab
4. Click **"Create token"**
5. Name: `ng-graphql-playground`
6. Permissions: Select `"Read"` and `"Write"`
7. Copy the token
8. Paste it somewhere safe temporarily

**You now have a Builder.io API token.**

---

### Step 2.3: Get Builder.io Space ID

1. Go to [builder.io/account](https://builder.io/account)
2. Click **"Spaces"** or **"Organization"** section
3. Find your workspace/space name (usually "Personal Space" or similar)
4. Copy the **Space ID** from the URL or settings
5. Paste it somewhere safe temporarily

Alternative: Go to your Builder.io dashboard and look for Space ID in settings.

**You now have a Builder.io Space ID.**

---

## Part 3: Configure GitHub Secrets

GitHub Secrets securely store credentials used by CI/CD workflows and local scripts.

### Step 3.1: Add Secrets to Repository

1. Go to [github.com/pluto-atom-4/ng-graphql-playground](https://github.com/pluto-atom-4/ng-graphql-playground)
2. Click **Settings** (top right)
3. Go to **"Secrets and variables"** → **"Actions"**
4. Click **"New repository secret"** (repeat for each)

### Step 3.2: Add Each Secret

Add these four secrets with the values you collected:

**Secret 1: FIGMA_API_TOKEN**

- Name: `FIGMA_API_TOKEN`
- Value: (paste your Figma API token)
- Click "Add secret"

**Secret 2: FIGMA_PROJECT_ID**

- Name: `FIGMA_PROJECT_ID`
- Value: (paste your Figma file ID)
- Click "Add secret"

**Secret 3: BUILDER_IO_API_TOKEN**

- Name: `BUILDER_IO_API_TOKEN`
- Value: (paste your Builder.io API token)
- Click "Add secret"

**Secret 4: BUILDER_IO_SPACE_ID**

- Name: `BUILDER_IO_SPACE_ID`
- Value: (paste your Builder.io Space ID)
- Click "Add secret"

**All secrets are now encrypted and available to workflows.**

---

## Part 4: Local Environment Variables (Optional)

For local testing without GitHub Actions, create a `.env.local` file:

```bash
# .env.local (DO NOT COMMIT - add to .gitignore)
FIGMA_API_TOKEN=your_figma_api_token_here
FIGMA_PROJECT_ID=your_figma_project_id_here
BUILDER_IO_API_TOKEN=your_builder_io_api_token_here
BUILDER_IO_SPACE_ID=your_builder_io_space_id_here
```

Then load it before running npm scripts:

```bash
# Load env and test design sync
source .env.local
pnpm design:sync
```

---

## Part 5: Verify Setup

### Test Builder.io CLI Locally

```bash
# Install dependencies (if not already done)
pnpm install

# Test Figma → Builder.io sync
pnpm design:sync

# Expected output:
# - Components synced from Figma
# - Generated code in frontend/src/app/components/generated/
# - Success message
```

### Test Component Generation

```bash
# Validate generated components
pnpm validate:generated-components

# Expected output:
# - List of generated components
# - Type checking results
# - Validation report
```

---

## Troubleshooting

### "Invalid API Token"

- Verify the token hasn't expired
- Create a new token from Figma/Builder.io settings
- Update GitHub Secrets

### "Project/File not found"

- Verify Figma file ID is correct
- Ensure file is shared publicly or token has access
- Check file exists in Figma workspace

### "No components generated"

- Ensure Figma components are properly marked as components (right-click → "Create component")
- Check Builder.io space is accessible
- Verify component names follow naming conventions (PascalCase)

### "GitHub Actions workflow fails"

- Check GitHub Secrets are set (Settings → Secrets → Actions)
- Verify secret names match exactly: `FIGMA_API_TOKEN`, `FIGMA_PROJECT_ID`, etc.
- Check workflow logs: Actions tab → failed workflow → view logs

---

## Next Steps

Once setup is complete:

1. **Phase 1 Testing**:

   ```bash
   pnpm design:sync          # Generate from Figma
   pnpm design:sync-back     # Bidirectional sync
   ```

2. **Phase 2** (GitHub Actions automation):
   - Automated component generation on Figma design updates
   - Quality validation gates

3. **Phase 3** (Copilot CLI integration):
   - Orchestrate design-to-code workflow via CLI

4. **Phase 4** (End-to-end example):
   - ProgressCard: Figma → Angular → GraphQL → C# → SQL

---

## Security Notes

⚠️ **Important**:

- Never commit `.env.local` or tokens to git
- GitHub Secrets are encrypted and never exposed in logs
- Rotate tokens periodically
- Use different tokens per environment (dev, staging, prod)
- Review token permissions: only grant what's needed

---

## Reference

- [Figma API Documentation](https://www.figma.com/developers/api)
- [Builder.io Documentation](https://www.builder.io/docs)
- [Builder.io CLI Documentation](https://www.builder.io/docs/cli)
- [Figma Code Connect](https://www.figma.com/developers/docs/code-connect)

---

**Related Documentation**:

- `docs/implementation-planning/issue-9-design-to-code-workflow.md` - 4-phase roadmap
- `docs/implementation-planning/issue-9-phase-1-status.md` - Phase 1 completion
- `.builderio/config.json` - Builder.io configuration template
- `.figma-code-connect-config.json` - Figma sync configuration

**Setup Time**: ~15 minutes  
**Questions?** Check troubleshooting section above or create a GitHub issue.
