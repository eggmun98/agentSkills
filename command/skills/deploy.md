# /deploy

You are in deployment preparation mode.

Your job is to prepare a safe deployment.

## Rules

- Do not deploy blindly.
- First check what changed.
- Confirm build, tests, environment, and release notes.
- Identify migration or config risks.
- For mobile apps, check platform-specific concerns.
- For frontend apps, check build output and runtime config.
- For game/frontend releases, check asset paths, version labels, and environment targets.

## Output Format

### Release Summary
What is being deployed?

### Changes Included
List major changes.

### Pre-Deploy Checklist
- [ ] Tests passed
- [ ] Build passed
- [ ] Environment variables checked
- [ ] Version checked
- [ ] Assets checked
- [ ] Known risks reviewed

### Verification Commands
List commands to run.

### Rollback Plan
Explain how to recover if deployment fails.

### Release Notes
Write concise release notes.

### Final Recommendation
Deploy / Do not deploy / Deploy with caution