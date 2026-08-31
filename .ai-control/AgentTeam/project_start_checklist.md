# Project Start Readiness Checklist

Use this checklist before starting development on a new project, a major module, or an inherited existing project.

## 1. Product Foundation

- Project purpose is clear.
- Target users are defined.
- User roles and permissions are known.
- MVP scope is defined.
- Postponed scope and non-goals are defined.
- Success metrics are defined.
- Growth assumptions are discussed.
- Revenue or monetization options are discussed.
- Target segment and positioning assumptions are discussed.
- Marketing channel and launch assumptions are discussed.
- Go/no-go recommendation is documented when the idea is still uncertain.

## 2. Default Product Surfaces

- Onboarding is planned or marked not-applicable.
- Landing/home page is planned or marked not-applicable.
- Login/authentication is planned or marked not-applicable.
- Dashboard/main entry is planned or marked not-applicable.
- Profile/settings are planned or marked not-applicable.
- Role management or permission model is planned or marked not-applicable.
- Notifications or communication path is planned or marked not-applicable.
- Search, filter, or discovery flow is planned or marked not-applicable.
- Help and support are planned or marked not-applicable.
- Feedback/contact path is planned or marked not-applicable.
- Landing page messaging and trust signals are planned or marked not-applicable.
- Terms/privacy/trust pages are planned or marked not-applicable.
- Analytics/telemetry and admin/support tooling are planned or marked not-applicable.
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
- Module-wise structure defined.
- Module responsibilities, routes/screens, APIs, data ownership, permissions, and shared services identified.
- Cross-module boundaries and regression risk areas documented.
- Secrets policy confirmed.
- Threat model and security risk reviewed.
- Vulnerability/security scan strategy defined.
- Migration/rollback need assessed.
- Technical feasibility reviewed.
- Infrastructure and recurring cost drivers reviewed.
- Marketing/growth readiness reviewed if launch is in scope.
- Test strategy defined.
- Common Gate Pipeline ready.
- Branch pathway confirmed: `develop ---> staging ---> main`.
- App version, build number/versionCode, changelog, release notes, and rollback version strategy defined.
- Required release path defined: local server -> develop URL -> staging URL -> main/production URL -> Android APK/AAB when applicable.

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
