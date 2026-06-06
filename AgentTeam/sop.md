# Universal Standard Operating Procedures (SOP) Handbook

This document compiles the official, mandatory **Standard Operating Procedures (SOPs)** for the application codebase. All builders (Developer, DevOps) and validators (CLI, GUI, Human Testers) must strictly adhere to these protocols.

---

## 🛡️ SOP 1: Button Actions & UI Loader Integrity

To prevent duplicate database mutations, infinite spinner freezes, and visual layout shifts, follow these guidelines:

### 1. The Try/Catch/Finally Loader Loop
Every button that triggers an asynchronous backend API request must immediately toggle an active loading state and explicitly disable itself to prevent double-clicks:
```javascript
const handleAction = async () => {
  try {
    setLoading(true);
    await apiCall();
    showToast("Action completed.");
  } catch (error) {
    showToast(error.message || "An error occurred.");
  } finally {
    setLoading(false); // ALWAYS clear loading in the finally block
  }
};
```
*Mandatory Rule:* The `loading` state must be reset inside a `finally` block to ensure that even on API failures, the UI never freezes or leaves the button permanently stuck.

### 2. Option Text Entity Decoding
All select pickers, dropdowns, and option lists must naturally decode HTML characters. Never render raw escaped entities to the user:
*   *Incorrect:* `Nearest &amp; Top Rated`
*   *Correct:* `Nearest & Top Rated`

---

## 🔑 SOP 2: Transaction & Token Integrity Safeguards

To prevent database clutter, concurrent write overrides, and duplicate queue slots:

### 1. Single Active Token Safeguard
Before creating or inserting a new transaction or token, both the frontend client and the backend API controller must explicitly check if the user already holds an active token/transaction with the same entity:
```javascript
const activeToken = await db.collection('transactions').findOne({
  userId: userId,
  entityId: entityId,
  status: { $in: ['pending', 'confirmed', 'active'] }
});

if (activeToken) {
  throw new Error("You already have an active transaction/token with this entity.");
}
```
If an active transaction/token exists, block the transaction immediately.

### 2. Transient Write Retries
Under heavy concurrent load, database operations can fail due to write locks or transient database connection lags. All transaction-critical database writes must implement a retry loop (up to 3 attempts with exponential backoff) before throwing a failure.

---

## 📡 SOP 3: Notification & Registration Token Pruning

To protect the alert delivery system and ensure push notification reliability:

### 1. Stale Token Pruning (Silent Alert Prevention)
*   **The Issue:** Stale or inactive registration tokens stored in the database cause notification failure loops, slow down queue dispatches, and trigger silent alerts.
*   **The Solution:** When a push notification dispatch returns an inactive or unregistered device error from the configured notification gateway, immediately prune that stale registration token from the database.
*   **Audit Logging:** Every notification trigger must write an audit record to the `notification_triggers` collection/table in the database for backend verification.

---

## 🔄 SOP 4: Realtime Synchronization & Context Cleansing

To maintain consistent state representation across multiple concurrent roles:

### 1. Role-Switching Cache Purging
*   **The Issue:** When switching between different user roles or dashboard views (such as Customer, Provider, Admin, etc.), stale context caches can leak private states.
*   **The Solution:** Every role-switch action must explicitly purge local state stores, context providers, and data fetch hooks, ensuring only fresh, authorized, role-appropriate queues/views are rendered.

### 2. Live Public Dashboard Synchronicity
*   The **Live Public Dashboard** must sync live status transitions immediately upon database mutations without requiring a manual refresh. Spacing and component sizes on public displays must be optimized for dense, long-distance readability.

---

## 📋 SOP 5: Requirements Traceability & FSC Management

To ensure 100% architectural and implementation alignment from requirements drafting to deployment:

### 1. Requirements Serialization
Every new build backlog must be serialized by the Product Owner Agent into `.ai-control/feature_spec.json`. This contract defines numbered requirements (`REQ-xxx`) and explicit acceptance criteria.

### 2. Upstream & Downstream Alignment Checks
*   **The Architect and UI/UX Agents** must map database schemas, APIs, and layouts back to the respective `REQ-xxx` IDs in their designs.
*   **The Developer Agent** must map modified code blocks to the specific `REQ-xxx` IDs inside their commit/diff report.
*   **The Tester Agents** must organize visual, backend, and human QA logs as an RTM checklist (e.g. `REQ-001: PASS`).
*   **The Supervisor Agent** must perform a **Traceability Audit** before approving. If any `REQ-xxx` lacks an implementation trace or is missing a PASS report from GUI, CLI, or Human Testers, the Supervisor must strictly reject the build.

---

## 🧭 SOP 6: Git Ownership & Release Control

To prevent unapproved releases, accidental branch changes, and unsafe production pushes:

### 1. Role Ownership
*   **Developer Agent:** Edits code, checks git status, summarizes diffs, and reports changed files. It must not commit, push, tag, merge, or rollback.
*   **Supervisor Agent:** Audits changed files, branch state, test reports, and RTM coverage. It approves or rejects git progression.
*   **DevOps & Monitor Agent:** Executes approved git actions, including staging, committing, pushing, tagging, deployment, and rollback.

### 2. Approval Lock
No git release action may occur unless the Supervisor output explicitly states **Git/Deployment Allowed: Yes**. If approval is missing, ambiguous, or rejected, DevOps must halt and request Supervisor clearance.

### 3. Standard Branch Pathway
All reusable projects must follow this branch promotion order:
```text
develop ---> staging ---> main
```
*   **develop:** Active development branch where approved Developer changes are collected.
*   **staging:** Pre-production validation branch promoted from `develop` only after Supervisor approval.
*   **main:** Production branch promoted from `staging` only after staging validation and final Supervisor approval.
*   **DevOps & Monitor Agent:** Executes approved branch creation, merge, push, tag, and deployment actions across this pathway.

### 4. Common Gate Pipeline
Every branch push or promotion must pass the same reusable gate. This gate applies before `develop`, before `staging`, and before `main`:
```text
Common Gate Pipeline
  -> current environment build completed
  -> lint/test/build passed
  -> GUI Tester PASS
  -> CLI/API Tester PASS
  -> Human Tester PASS
  -> RTM traceability PASS
  -> Supervisor manual approval action
  -> DevOps executes approved git/deploy action
```
*   **Before develop:** Local code must pass the Common Gate Pipeline before DevOps may push to `develop`.
*   **Before staging:** Dev URL must pass the Common Gate Pipeline, then Supervisor must trigger the manual staging approval action.
*   **Before main:** Staging URL must pass the Common Gate Pipeline, then Supervisor must trigger the manual production approval action.
*   **Same Gate Rule:** `develop`, `staging`, and `main` use the same gate checklist. No environment gets a lighter process.
*   **No Skip Rule:** No agent may reuse prior environment results as proof for the next environment.

### 5. Environment Promotion Sequence
The branch pathway is not a single approval. It is a repeated full-validation ecosystem gate:
```text
Local Common Gate PASS
  ---> Supervisor manual approval to push develop
  ---> DevOps pushes to develop
  ---> Dev URL Common Gate PASS
  ---> Supervisor manual approval to promote staging
  ---> DevOps promotes to staging
  ---> Staging URL Common Gate PASS
  ---> Supervisor manual approval to promote main
  ---> DevOps promotes to main / production
```
*   **Local Approval Gate:** Developer must complete local lint, test, build, and preview checks before Supervisor allows DevOps to push to `develop`.
*   **Develop Gate:** After `develop` is updated, all validator agents must repeat full testing against the dev URL. Any FAIL returns to Developer.
*   **Staging Gate:** After promotion to `staging`, all validator agents must repeat full testing against the staging URL. Any FAIL returns to Developer through the standard bug-fix loop.
*   **Production Gate:** `main` promotion is allowed only after staging has a full PASS from GUI Tester, CLI/API Tester, Human Tester, and Supervisor.
*   **Manual Approval Action Lock:** Staging and production promotions require explicit Supervisor manual approval action records before DevOps executes git promotion.

---

## 📚 SOP 7: Documentation, Runbooks & Root-Cause Knowledge Base

To build a stable product ecosystem and a strong reusable team, important operational knowledge must be documented instead of remaining only in chat history.

### 1. Documentation Ownership
*   **Developer Agent:** Documents changed behavior, setup changes, new commands, known limitations, and code-level decisions in the implementation summary.
*   **Tester Agents:** Document failed scenarios, reproduction steps, screenshots/log paths, affected URLs, and final PASS evidence.
*   **Supervisor Agent:** Verifies that important decisions, repeated issues, and major incidents have documentation before final approval.
*   **DevOps & Monitor Agent:** Maintains deployment runbooks, release notes, rollback notes, environment notes, and production operation records.

### 2. Required Documentation Artifacts
Each project should maintain these documentation areas when applicable:
```text
docs/
  runbooks/
  troubleshooting/
  release-notes/
  decisions/
```
*   **runbooks:** Repeatable operational steps for deploy, rollback, test, environment setup, and incident response.
*   **troubleshooting:** Repeated issues, big issues, root causes, fixes, verification steps, and prevention notes.
*   **release-notes:** User-facing and technical release summaries for meaningful changes.
*   **decisions:** Important architecture, product, security, and operational decisions with reasoning.

### 3. Troubleshooting SOP
Repeated issues or major issues must be documented under the project troubleshooting folder using this structure:
```text
docs/troubleshooting/YYYY-MM-DD-short-issue-title.md
```
Each troubleshooting record must include:
*   **Issue Summary:** What failed and who/what was affected.
*   **Environment:** Local, Dev URL, Staging URL, or Production.
*   **Reproduction Steps:** Exact steps, command, URL, or workflow.
*   **Root Cause:** Technical reason the issue happened.
*   **Fix Applied:** Files/modules changed and approach used.
*   **Verification Evidence:** GUI, CLI/API, Human, or Common Gate PASS proof.
*   **Prevention:** Rule, test, monitor, or SOP update to stop recurrence.

### 4. Approval Lock
The Supervisor must block final approval if a repeated issue or major issue was fixed but no troubleshooting/root-cause record was created.

---

## 🔐 SOP 8: Secrets & Environment Safety

To protect every reusable project from credential leaks:

### 1. Secret Handling Rules
*   Real `.env` files, API keys, passwords, tokens, certificates, private keys, and webhook secrets must never be committed.
*   Only sanitized templates such as `.env.example` may be committed.
*   Logs, screenshots, test reports, runbooks, and troubleshooting records must redact secret values.
*   If a secret appears in code, docs, logs, or chat output, the pipeline must halt until the secret is rotated and the exposure is documented.

### 2. Gate Requirement
The Supervisor and DevOps & Monitor Agents must reject promotion if hardcoded secrets, unredacted credentials, or unsafe environment files are present.

---

## 🗃️ SOP 9: Migration, Rollback & Data Consistency

To avoid data loss and unstable releases:

### 1. Required Migration Plan
Any schema, index, seed, or data transformation change must include:
*   Migration purpose and affected data.
*   Pre-migration backup or export requirement when data risk exists.
*   Forward migration steps.
*   Rollback migration steps.
*   Seed or default data strategy.
*   Post-migration consistency checks.

### 2. Ownership
*   **Architect Agent:** Designs migration, rollback, and consistency plan.
*   **Developer Agent:** Implements migration code/scripts and documents changed files.
*   **CLI/API Tester Agent:** Validates migration behavior, rollback behavior, and data consistency checks.
*   **Supervisor Agent:** Blocks approval if migration risk exists without a rollback plan.
*   **DevOps & Monitor Agent:** Executes approved migration steps only after Common Gate Pipeline and manual approval.

---

## 🚦 SOP 10: Project Start Readiness Standard

Before development starts on any new project, major module, or inherited existing project, the team must run a Project Start Readiness check. The goal is to avoid building features before the product foundation, user journey, environments, and support model are clear.

### 1. Product & Business Clarity
Every project must define:
*   Project purpose and target users.
*   Primary user roles and permissions.
*   Core user journeys and success metrics.
*   MVP scope, postponed scope, and non-goals.
*   Monetization, operational, or business constraints when applicable.

### 2. Default User-Facing Surfaces
Each project must explicitly implement, plan, or mark not-applicable for:
*   **Onboarding:** First-run guidance, role selection, setup steps, and activation path.
*   **Landing / Home Page:** Clear value, primary action, secondary action, trust signals, and route into the product.
*   **Login / Authentication:** Sign-in, sign-up, logout, session expiry, password/OTP recovery, and unauthorized states.
*   **Dashboard / Main Entry:** Role-specific landing after login, navigation, and task priority.
*   **Profile / Settings:** Account details, preferences, notification settings, and safe sign-out.
*   **Help & Support:** FAQ, contact path, issue reporting, support escalation, and troubleshooting links.
*   **Legal / Trust:** Terms, privacy, consent, data usage, deletion/export path when applicable.
*   **Empty / Error / Loading States:** No-data states, failed-action states, retries, loaders, and offline/degraded states.

### 3. Existing Project Intake
For existing projects, the Prompt Agent and Architect must inspect:
*   Current routes, pages, APIs, database/schema, authentication, roles, and environment files.
*   Active dependencies, build/test commands, deployment target, branch state, and open issues.
*   Existing documentation, runbooks, troubleshooting records, and known repeated issues.
*   Legacy or obsolete modules that must not leak into new work.

### 4. Engineering Readiness
Before implementation, the team must confirm:
*   Repository structure and coding conventions.
*   Environment variables and safe `.env.example`.
*   Secrets policy and credential redaction.
*   Database/schema ownership and migration plan if data changes are needed.
*   Test strategy for unit, integration, E2E, GUI, CLI/API, and Human validation.
*   Common Gate Pipeline readiness for local, dev, staging, and production.

### 5. Documentation Readiness
Before the first promotion beyond local development, the project must have:
*   Setup/run instructions.
*   Common Gate Pipeline instructions.
*   Runbook location.
*   Troubleshooting/root-cause location.
*   Release notes location.
*   Decision log location for important product, architecture, or operational choices.

### 6. Approval Lock
The Supervisor must block development or promotion if required start-readiness items are missing without an explicit Product Owner decision to postpone them.
