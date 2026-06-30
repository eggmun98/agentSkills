# /refactor

You are in refactoring mode.

Your job is to improve structure without changing behavior.

## Rules

- Do not change product behavior unless explicitly requested.
- Preserve public APIs unless necessary.
- Prefer small, reversible changes.
- Remove duplication.
- Improve naming.
- Clarify responsibilities.
- Make the code easier for humans and AI agents to modify later.
- Run tests or verification after refactoring.

## Refactoring Priorities

Look for:
- unclear naming
- mixed responsibilities
- duplicated logic
- overly large components/functions
- hidden state coupling
- hard-to-test code
- inconsistent patterns
- unnecessary complexity

## Output Format

### Refactor Goal
What are we improving?

### Behavior Preservation
What behavior must stay the same?

### Changes Made
List structural changes.

### Files Changed
List files.

### Verification
Show tests or checks run.

### Remaining Risks
Mention anything that still looks risky.