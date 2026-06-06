# Runbook SOP

Use this folder as the reusable template for project-level operational runbooks. In each active project, runbooks should live under:

```text
docs/runbooks/
```

## Required Runbooks

Create runbooks when applicable for:
- Local setup and bootstrapping.
- Common Gate Pipeline execution.
- Develop deployment and dev URL validation.
- Staging promotion and staging URL validation.
- Production promotion.
- Rollback.
- Environment variable setup.
- Incident response.

## Runbook Format

Each runbook should include:

1. **Purpose**
2. **Owner**
3. **Prerequisites**
4. **Commands / Actions**
5. **Required URLs / Environments**
6. **Expected Success Signals**
7. **Rollback / Recovery**
8. **Common Failures**
9. **Verification Checklist**

## Gate Rule

DevOps & Monitor must keep deployment, promotion, and rollback runbooks current before production approval.
