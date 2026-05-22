# GitHub Copilot Configuration & Procedures

This directory contains GitHub Copilot-specific configurations, procedures, and guidelines for the ng-graphql-playground project.

## Directory Structure

```
.github/copilot/
├── README.md (this file)
└── rules/
    └── pr-review-workflow.md — Automated GitHub PR review procedure
```

## Quick Reference

| Document | Purpose | Audience |
|----------|---------|----------|
| **[pr-review-workflow.md](rules/pr-review-workflow.md)** | Defines how Copilot reviews PRs and posts GitHub comments | Developers, Copilot users, PR reviewers |

## Key Procedures

### PR Review Workflow

When reviewing a GitHub PR, Copilot follows a **mandatory three-phase workflow**:

1. **Phase 1**: Gather PR details, examine changes, check CI/CD status
2. **Phase 2**: Analyze code against architecture patterns and requirements
3. **Phase 3**: **Post review as GitHub PR comment** ← MANDATORY final step

**Key Rule**: Review outcomes MUST be posted as GitHub PR comments for team visibility.

For details, see: [pr-review-workflow.md](rules/pr-review-workflow.md)

## Related Files

- **Main Instructions**: See `../../copilot-instructions.md`
- **Claude Code Guide**: See `../../CLAUDE.md`
- **Contribution Guide**: See `../../CONTRIBUTING.md`
- **Architecture Guide**: See `../../docs/research-architecuture-design.md`

## Future Procedures (Planned)

- [ ] Code change approval workflow
- [ ] Security review checklist
- [ ] Performance verification procedure
- [ ] Documentation update guidelines

## Best Practices

When working with Copilot in this repository:

✅ **DO**:
- Follow procedures exactly as documented
- Post review outcomes to GitHub
- Reference requirements and issues
- Maintain audit trail via comments
- Escalate unclear requirements before reviewing

❌ **DON'T**:
- Skip final comment posting step
- Post incomplete reviews
- Make assumptions about requirements
- Comment on style alone
- Skip verification checklists

## GitHub Copilot CLI Integration

These procedures apply to:
- 🤖 Claude Code sessions (`.claude/settings.json`)
- 📝 GitHub Copilot CLI sessions (`gh copilot ...`)
- 🔄 Automated Copilot workflows

All tools follow the same procedures to ensure consistency.

## Questions or Suggestions?

If you have improvements to these procedures:
1. Create an issue with the `copilot-procedure` label
2. Reference the specific section that needs updating
3. Provide rationale for the change
4. Link any supporting documentation

---

**Last Updated**: 2026-05-22  
**Maintained By**: Development Team  
**Status**: Active & in use
