# Security Policy

## Reporting Security Vulnerabilities

This is a solo developer project managing manufacturing workflows and customer data. Security is critical.

**Please report security vulnerabilities privately** rather than opening a public GitHub issue, to avoid disclosing the vulnerability before a fix is available.

### Reporting Process

1. Do NOT create a public GitHub issue for security vulnerabilities
2. Email security details to: [maintainer contact - add when available]
3. Include:
   - Description of the vulnerability
   - Steps to reproduce (if applicable)
   - Potential impact
   - Suggested fix (if you have one)

4. Expect response within **48 hours**
5. Security fix will be deployed promptly once confirmed

---

## Supported Versions

| Version | Supported | Status |
|---------|-----------|--------|
| Latest (main branch) | ✅ | Active development |
| Older releases | ❌ | Not supported |

---

## Security Features

This repository implements comprehensive security controls:

- ✅ **Secret Scanning**: Push protection enabled (prevents accidental credential leaks)
- ✅ **Pull Request Review**: AI Code Review auto-approval for solo developer
- ✅ **Dependency Security**: Dependabot monitors for vulnerabilities
- ✅ **Code Scanning**: CodeQL analysis on all PRs (TypeScript & C#)
- ✅ **Branch Protection**: Enforced for main branch
  - Requires PR approval
  - Status checks must pass
  - Force pushes disabled
  - Deletion protection enabled
- ✅ **CI/CD Security**: GitHub Actions with minimal token scopes

---

## Development Security Guidelines

### Never Commit Secrets

Never commit the following to the repository:

- ❌ API keys or tokens
- ❌ Database passwords or connection strings
- ❌ Private encryption keys
- ❌ AWS access keys or similar credentials
- ❌ Third-party service secrets
- ❌ OAuth tokens or refresh tokens

**Always use:**
- ✅ `.env.local` files (git-ignored, for local development)
- ✅ `.env.local.example` templates (documenting environment variables)
- ✅ GitHub repository secrets (for CI/CD workflows)
- ✅ Environment variables (in deployment systems)

### Environment Variable Management

1. **Development:** Use `.env.local` (not committed to git)
   ```bash
   # .env.local (DO NOT COMMIT)
   DATABASE_PASSWORD=your-actual-password
   API_KEY=your-actual-key
   ```

2. **Documentation:** Update `.env.local.example` (commit this)
   ```bash
   # .env.local.example (COMMIT THIS)
   DATABASE_PASSWORD=change_me
   API_KEY=change_me
   ```

3. **Production:** Use GitHub repository secrets or deployment environment secrets
   - Settings → Secrets and variables → Actions
   - Reference in workflows: `${{ secrets.SECRET_NAME }}`

### Code Quality & Security

- Avoid logging sensitive data (passwords, tokens, PII)
- Use prepared statements/parameterized queries (no SQL injection)
- Validate all user input
- Follow OWASP security guidelines
- Review code for security issues before committing

---

## CI/CD & Automation Security

### GitHub Actions

All workflows use minimal token scopes:

```yaml
permissions:
  contents: read          # Only read repository contents
  pull-requests: write    # Only write to PR reviews (when needed)
  # NOT: write to contents, admin, deployments, etc.
```

### Dependencies

- Dependabot automatically scans npm, NuGet, and GitHub Actions
- Security updates merged automatically (patch/minor versions)
- Major version updates reviewed manually
- Review Dependabot PRs before merging

### Secrets Management

- BOT_TOKEN: Personal access token for PR automation
  - Scopes: `repo`, `workflow`, `pull-requests:write`
  - Expiration: 90 days (renewal reminder set)
  - Never logged or displayed in workflow output

---

## Branch Protection Rules

The `main` branch is protected with:

- ✅ Requires 1 approving review (via AI Code Review workflow)
- ✅ Requires status checks to pass
- ✅ Requires branches to be up to date before merging
- ✅ Blocks force pushes
- ✅ Blocks branch deletion
- ✅ Auto-deletes head branches after merge

**Implication:** No direct pushes to `main` allowed; all changes via PR

---

## Vulnerability Disclosure Timeline

1. **Vulnerability reported** (Day 0)
2. **Initial assessment** (within 24 hours)
3. **Fix implementation** (24-72 hours depending on severity)
4. **Testing** (24 hours)
5. **Deploy fix** (via normal PR process)
6. **Public disclosure** (if appropriate, after fix deployed)

---

## Security Best Practices for Contributors

1. **Keep dependencies current** — Accept Dependabot updates promptly
2. **Run security tools locally** — Use `git secrets` to catch issues
3. **Review Dependabot PRs carefully** — Check for breaking changes
4. **Test thoroughly** — All tests must pass before merge
5. **Use strong authentication** — GPG signing recommended
6. **Report issues privately** — Use security@example.com for vulnerabilities

---

## Known Security Limitations

- Solo developer project: No external code review process
- Auto-approval enabled: PRs from maintainer auto-approved
- CI checks are required: Lint, build, tests must pass regardless
- Branch protection enforced: All merges via PR, not direct push

---

## Security Updates & Patches

### Dependency Updates

Dependabot creates PRs weekly:

| Type | Frequency | Auto-Merge | Manual Review |
|------|-----------|-----------|---------------|
| Patch (1.0.1 → 1.0.2) | Weekly | Optional | Minimal |
| Minor (1.0 → 1.1) | Weekly | No | Recommended |
| Major (1.0 → 2.0) | Weekly | No | Required |

Security updates bypass normal versioning and merge ASAP.

### Critical Vulnerabilities

If a critical vulnerability is discovered:

1. Emergency branch created from main
2. Fix implemented and tested immediately
3. Deployed via emergency PR (overrides normal process)
4. Public disclosure and user notification follow

---

## Compliance & Standards

This project follows:

- ✅ OWASP Top 10 security principles
- ✅ GitHub's recommended security practices
- ✅ Microsoft security guidelines for .NET applications
- ✅ Angular security best practices for TypeScript

---

## Related Documentation

- [CONTRIBUTING.md](CONTRIBUTING.md) — Development guidelines
- [docs/SECURITY-CHECKLIST.md](docs/SECURITY-CHECKLIST.md) — Pre-commit security checklist
- [GitHub Security Best Practices](https://docs.github.com/en/code-security)

---

## Contact & Support

**Questions about security?**
- Check [CONTRIBUTING.md](CONTRIBUTING.md) for development setup
- Review GitHub Issues for known security topics
- See [docs/SECURITY-CHECKLIST.md](docs/SECURITY-CHECKLIST.md) for common issues

**Found a vulnerability?**
- Report privately (do not open public issue)
- Email [security contact]
- Include details and steps to reproduce

---

**Last Updated:** 2026-05-20  
**Status:** Active  
**Owner:** @pluto-atom-4
