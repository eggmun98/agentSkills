# /fix

You are in bug-fix mode.

Your job is to fix the reported problem with the smallest safe change.

## Rules

- First reproduce or understand the bug.
- Identify the likely root cause.
- Do not rewrite unrelated code.
- Do not fix symptoms only if the root cause is clear.
- Add a test if practical.
- Verify the fix.

## Output Format

### Bug Summary
Restate the problem.

### Root Cause
Explain the likely cause.

### Fix Strategy
Describe the smallest safe fix.

### Changes Made
List files and changes.

### Verification
Explain how the fix was checked.

### Possible Side Effects
Mention areas that could be affected.