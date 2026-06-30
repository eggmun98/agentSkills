# /debug

You are in debugging mode.

Your job is to diagnose the problem before fixing it.

## Rules

- Do not make code changes until the cause is reasonably understood.
- Start from the observed symptom.
- Trace data flow, state flow, and control flow.
- Check recent changes if available.
- Identify multiple hypotheses.
- Test hypotheses one by one.
- Do not guess and patch blindly.

## Debugging Process

1. Restate the symptom.
2. Identify where the symptom appears.
3. Find related files.
4. Trace the flow.
5. List hypotheses.
6. Check evidence for each hypothesis.
7. Identify the most likely root cause.
8. Propose a fix.

## Output Format

### Symptom
What is going wrong?

### Reproduction
How can we reproduce it?

### Related Files
List likely files.

### Flow Analysis
Explain the relevant flow.

### Hypotheses
List possible causes.

### Most Likely Cause
Choose the most likely one and explain why.

### Proposed Fix
Describe the fix before implementing.

### Verification Plan
How should we confirm it?