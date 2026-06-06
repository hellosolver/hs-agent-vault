# Project Start Readiness Checklist

Use this checklist before starting development on a new project, a major module, or an inherited existing project.

## 1. Product Foundation

- Project purpose is clear.
- Target users are defined.
- User roles and permissions are known.
- MVP scope is defined.
- Postponed scope and non-goals are defined.
- Success metrics are defined.

## 2. Default Product Surfaces

- Onboarding is planned or marked not-applicable.
- Landing/home page is planned or marked not-applicable.
- Login/authentication is planned or marked not-applicable.
- Dashboard/main entry is planned or marked not-applicable.
- Profile/settings are planned or marked not-applicable.
- Help and support are planned or marked not-applicable.
- Terms/privacy/trust pages are planned or marked not-applicable.
- Empty/error/loading states are planned.

## 3. Existing Project Intake

- Routes/pages reviewed.
- APIs/controllers reviewed.
- Database/schema reviewed.
- Authentication and roles reviewed.
- Environment files and `.env.example` reviewed.
- Build/test commands identified.
- Deployment target identified.
- Known issues and repeated failures reviewed.
- Legacy/out-of-scope modules identified.

## 4. Engineering Readiness

- Repository structure understood.
- Coding conventions understood.
- Secrets policy confirmed.
- Migration/rollback need assessed.
- Test strategy defined.
- Common Gate Pipeline ready.
- Branch pathway confirmed: `develop ---> staging ---> main`.

## 5. Documentation Readiness

- Setup/run instructions location known.
- Runbook location known.
- Troubleshooting/root-cause location known.
- Release notes location known.
- Decision log location known.

## 6. Supervisor Gate

Supervisor must mark each item as:

```text
Ready / Missing / Not Applicable / Postponed by Product Owner
```

Development or promotion must be blocked if critical readiness items are missing without explicit Product Owner approval.
