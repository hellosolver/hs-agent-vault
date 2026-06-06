# Troubleshooting SOP & Root-Cause Knowledge Base

Use this folder as the reusable template for project-level troubleshooting records. In each active project, repeated issues and major issues must be documented under:

```text
docs/troubleshooting/
```

## When to Create a Record

Create or update a troubleshooting record when:
- The same issue appears more than once.
- A production, staging, or dev URL issue blocks the Common Gate Pipeline.
- A defect causes rollback, failed promotion, data inconsistency, broken login, failed payment/transaction, broken deployment, or user-visible outage.
- The fix required investigation across multiple files, services, agents, or environments.

## File Naming

Use this format:

```text
YYYY-MM-DD-short-issue-title.md
```

Example:

```text
2026-06-03-staging-login-redirect-loop.md
```

## Required Sections

Each record must include:

1. **Issue Summary**
2. **Severity**
3. **Environment**
4. **Affected Flow / Module**
5. **Reproduction Steps**
6. **Observed Result**
7. **Expected Result**
8. **Root Cause**
9. **Fix Applied**
10. **Files / Services Changed**
11. **Verification Evidence**
12. **Prevention / SOP Update**
13. **Owner**
14. **Status**

## Supervisor Gate Rule

Supervisor must block final approval if a repeated or major issue was fixed but no root-cause record exists.
