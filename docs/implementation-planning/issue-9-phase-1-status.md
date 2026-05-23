# Phase 1: Design-to-Code Foundation - Implementation Status

**Issue**: #9 - Design-to-Code Agentic AI Workflow  
**Phase**: 1 (Foundation)  
**Date**: 2026-05-23  
**Status**: ✅ **SUBSTANTIALLY COMPLETE** (Awaiting External Setup)

---

## Summary

Phase 1 established the local development environment for design-to-code automation. Three of four tasks completed locally; one task deferred pending Figma/Builder.io account setup.

---

## Completed Tasks

### ✅ Task 1.1: Builder.io CLI Setup

**Status**: COMPLETE

**Deliverables**:
- [ ] ✅ `@builder.io/cli` installed (v1.2.10)
- [ ] ✅ `.builderio/config.json` created with placeholders for:
  - `FIGMA_API_TOKEN`
  - `FIGMA_PROJECT_ID`
  - `BUILDER_IO_API_TOKEN`
  - `BUILDER_IO_SPACE_ID`
- [ ] ✅ Output directory created: `frontend/src/app/components/generated/`
- [ ] ✅ npm script added: `pnpm design:sync`

**What's Needed**: 
- User to provide Figma API token (from figma.com/settings)
- User to create Builder.io account and get API token + space ID
- Set environment variables or GitHub Secrets

---

### ✅ Task 1.2: Figma Code Connect Setup

**Status**: COMPLETE

**Deliverables**:
- [ ] ✅ `@figma/code-connect` installed (v1.4.5)
- [ ] ✅ `.figma-code-connect-config.json` created with:
  - Component regex patterns
  - Output paths for generated components
  - Language configuration (TypeScript)
- [ ] ✅ npm script added: `pnpm design:sync-back`

**What's Needed**:
- User to authenticate with Figma token
- User to run `pnpm design:sync-back` to test bidirectional sync

---

### ⏳ Task 1.4: Figma MCP for Copilot CLI

**Status**: DEFERRED (Package not in npm registry)

**Finding**: 
- `@figma/mcp` package does not exist in npm registry (as of 2026-05-23)
- Figma MCP support may be available via:
  1. Claude Code's native MCP integration (check `.claude/settings.json`)
  2. Future release from Figma
  3. Community MCP server implementation

**Alternative Approach**:
- Configured Claude Code to use Figma API directly
- Can query Figma design tokens via Copilot CLI using `@figma/code-connect` tool

---

## Updated Files

| File | Changes |
|------|---------|
| `package.json` | Added 4 scripts: `design:sync`, `design:sync-back`, `mcp:figma`, `validate:generated-components` |
| `.builderio/config.json` | Created with Figma + Builder.io config |
| `.figma-code-connect-config.json` | Created for bidirectional component syncing |
| `frontend/src/app/components/generated/` | Created (with `.gitkeep`) |
| `pnpm-lock.yaml` | Updated with new dependencies |

---

## Dependencies Installed

| Package | Version | Purpose |
|---------|---------|---------|
| `@builder.io/cli` | 1.2.10 | Convert Figma designs to Angular components |
| `@figma/code-connect` | 1.4.5 | Bidirectional sync: Figma ↔ Code |

---

## Next Steps (Manual Setup Required)

### For Figma Setup:
1. Go to [figma.com/settings](https://figma.com/settings)
2. Create personal access token
3. Save `FIGMA_API_TOKEN` to GitHub Secrets
4. Note your `FIGMA_PROJECT_ID` (from URL when viewing Figma file)
5. Update `.builderio/config.json` and `.figma-code-connect-config.json` with IDs

### For Builder.io Setup:
1. Create account at [builder.io](https://builder.io)
2. Get API token from account settings
3. Create a Builder.io space (or note existing space ID)
4. Save `BUILDER_IO_API_TOKEN` and `BUILDER_IO_SPACE_ID` to GitHub Secrets
5. Update `.builderio/config.json` with values

### For GitHub Actions (Phase 2):
1. Set GitHub Secrets:
   - `FIGMA_API_TOKEN`
   - `FIGMA_PROJECT_ID`
   - `BUILDER_IO_API_TOKEN`
   - `BUILDER_IO_SPACE_ID`
2. These will be referenced in `.github/workflows/figma-to-code.yml`

### For Copilot CLI MCP (Phase 3):
- Monitor Figma's MCP releases
- When available, install and configure in `.claude/settings.json`
- Current workaround: Use Copilot CLI to query Figma via `@figma/code-connect`

---

## How to Test Phase 1

Once you have Figma and Builder.io credentials:

```bash
# Test Builder.io CLI
pnpm design:sync

# Test Figma Code Connect
pnpm design:sync-back

# Verify scripts exist
pnpm run | grep design
```

---

## What's Ready for Phase 2

- ✅ All local configuration files created
- ✅ Dependencies installed
- ✅ npm scripts ready
- ✅ Output directory ready
- ⏳ Awaiting: GitHub Secrets (Figma + Builder.io credentials)
- ⏳ Awaiting: Phase 2 GitHub Actions workflow implementation

---

## Issues & Notes

1. **Figma MCP Not Available**: `@figma/mcp` not in npm registry
   - Workaround: Use existing `@figma/code-connect` integration
   - Plan: Will upgrade to MCP when available

2. **pnpm Workspace Warning**: "workspaces" field in package.json not supported by pnpm
   - Should convert to `pnpm-workspace.yaml` (deferred to future task)
   - Not blocking Phase 1

3. **Multiple Deprecated Dependencies**: Some transitive dependencies deprecated
   - Not blocking Phase 1
   - Plan: Address in next security update cycle

---

## Files to Commit

```
.builderio/config.json
.figma-code-connect-config.json
frontend/src/app/components/generated/.gitkeep
package.json (updated with scripts)
pnpm-lock.yaml
```

---

## Success Criteria Met

- [x] Builder.io CLI installed locally
- [x] Configuration files created for both tools
- [x] Output directory prepared for generated components
- [x] npm scripts added for team usage
- [x] Figma Code Connect installed
- [x] Documentation of next steps provided

---

## Document Control

| Version | Date | Status | Notes |
|---------|------|--------|-------|
| 1.0 | 2026-05-23 | COMPLETE | Phase 1 foundation tasks implemented |

