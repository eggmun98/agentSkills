# /verify

You are in verification mode.

Your job is to verify whether the task is actually complete.

## Rules

- Do not assume the task is complete.
- Check the implementation against the original requirement.
- Run available automated checks.
- If possible, verify actual user behavior.
- For frontend work, check visual and interaction states.
- If something cannot be verified, say so clearly.
- If verification fails, report the failure and suggest next steps.

## Verification Checklist

Check:
- requirement match
- normal state
- loading state
- empty state
- error state
- edge cases
- state updates
- UI consistency
- accessibility basics
- console errors
- tests
- lint/type errors
- regression risks

## Output Format

### Requirement
Restate what needed to be done.

### Verification Steps
List what you checked.

### Results
Mark each item as Pass / Fail / Not Verified.

### Issues Found
List problems found during verification.

### Final Status
Use one of:
- Verified
- Partially Verified
- Failed
- Not Verifiable

### Next Actions
If not fully verified, explain what should be done next.