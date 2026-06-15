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
  -> Security Auditor PASS
  -> GUI Tester PASS
  -> CLI/API Tester PASS
  -> Security Auditor release re-check PASS
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
*   **Security Auditor Agent:** Reviews migration security, sensitive data transformation risks, backup/rollback safety, and data exposure risks.
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

---

## 💬 SOP 11: Discovery & Strategy Discussion Routing

Before coding, users may want to discuss whether an app idea is good, what its future could be, whether it can generate income, what growth path is possible, what infrastructure is needed, and what approximate cost may look like. These discussions must be routed to the right agents instead of jumping directly to development.

### 1. Primary Discussion Owner
*   **Product Owner Agent** owns app viability, future growth, market fit, income potential, business model, MVP scope, roadmap, pricing, customer acquisition, risks, and go/no-go recommendations.
*   **Marketing Strategy Agent** owns go-to-market strategy, positioning, launch planning, campaign direction, channel strategy, and growth experiments after Product Owner defines business direction.

### 2. Supporting Agent Routing
*   **Prompt Agent:** Converts raw idea discussion into structured discovery questions and routes the next agent.
*   **Architect Agent:** Handles technical feasibility, integration complexity, scaling design, data model, and technical cost drivers.
*   **Security Auditor Agent:** Handles threat model, vulnerability risk, privacy/security feasibility, abuse cases, and security cost drivers.
*   **Marketing Strategy Agent:** Handles target audience segmentation, positioning, competitor alternatives, acquisition channels, launch plan, campaign ideas, and growth metrics.
*   **DevOps & Monitor Agent:** Handles hosting, environments, CI/CD, telemetry, deployment model, monitoring, and approximate recurring infrastructure cost.
*   **UI/UX Agent:** Handles onboarding, landing page, login flow, conversion, user trust, help/support, and first-run experience.

### 3. Discovery Flow
Use this flow for pre-build strategy discussion:
```text
Prompt Agent
  -> Product Owner Agent
  -> Marketing Strategy Agent (if positioning / launch / growth / channel strategy is needed)
  -> Architect Agent (if technical feasibility / infra shape is needed)
  -> Security Auditor Agent (if threat model / vulnerability / privacy risk is needed)
  -> DevOps & Monitor Agent (if hosting / deployment / monthly ops cost is needed)
  -> UI/UX Agent (if adoption / conversion / onboarding / landing/login is needed)
```

### 4. Discussion Output Standard
Discovery discussions should produce:
*   Problem and target user clarity.
*   Market and growth assumptions.
*   Revenue or monetization options.
*   Marketing positioning and target segment assumptions.
*   MVP recommendation and postponed scope.
*   Major risks and unknowns.
*   Technical feasibility summary.
*   Security and vulnerability risk summary.
*   Approximate infrastructure/cost drivers.
*   UX adoption and trust risks.
*   Launch and growth experiment recommendations.
*   Recommended next agent or decision: build, research more, postpone, or reject.

### 5. No-Code Lock
Discovery discussions do not authorize code changes. Development starts only after Product Owner creates or approves the FSC and the Standard Pathway begins.

---

## 📣 SOP 12: Marketing Strategy & Launch Readiness

Marketing is a strategic support lane, not a code-writing gate. It helps shape how the product reaches users before and after development.

### 1. Ownership
*   **Product Owner Agent:** Owns product/business direction, offer, pricing, and priority.
*   **Marketing Strategy Agent:** Owns positioning, audience segmentation, launch plan, campaign strategy, channels, and growth experiments.
*   **UI/UX Agent:** Converts marketing inputs into landing, onboarding, trust, and conversion experiences.
*   **Supervisor Agent:** Verifies launch readiness when marketing is part of the release plan.

### 2. Required Marketing Inputs
When launch or growth is in scope, Marketing Strategy Agent must define:
*   Target audience and segment hypothesis.
*   Problem, value proposition, and positioning.
*   Competitors or current alternatives.
*   Trust signals and proof needed.
*   Launch plan: pre-launch, launch, post-launch.
*   Channel plan: organic, paid, partnerships, community, content, SEO/ASO, email, or offline.
*   Campaign ideas with metrics.
*   Landing page and onboarding messaging inputs.
*   Legal/policy review needs for claims, offers, pricing, and promotions.

### 3. Launch Readiness Lock
If marketing launch is in scope, Supervisor must block launch approval until Product Owner, Marketing Strategy, UI/UX, and legal/policy review needs are aligned.

---

## SOP 13: Shared Worktree Multi-AI Continuity

Codex, Claude, Antigravity, and Copilot may use the same project worktree so work can continue from the same place when one tool reaches a token limit, context limit, or availability limit. This is allowed only with durable handoff state and strict conflict control.

### 1. Purpose
The goal is continuity, not parallel uncontrolled editing. A new AI tool should be able to pick up the exact current state without creating another worktree, losing context, or overwriting changes.

### 2. Allowed Tools
*   Codex
*   Claude
*   Antigravity
*   Copilot

### 3. One Active Editor Lock
Only one AI tool may actively edit files at a time in the same worktree. Other tools may read, review, discuss, or test, but must not write until the active edit lock is released or handed off.

### 4. Start-of-Session Checks
Before any AI tool edits files, it must:
*   Check current branch and workspace status with `git status --short`.
*   Read `.ai-control/workflow_state.json` or `workflow_state.json.example` if no project state exists.
*   Read `.ai-control/next_action.md` or `next_action.md` when present.
*   Confirm no unresolved lock, handoff, or blocker exists.
*   Confirm the active project root and stay inside that project worktree.

### 5. Durable Handoff Rule
When Codex, Claude, Antigravity, or Copilot stops work, it must leave a handoff note with:
*   Active tool name.
*   Current branch.
*   Files changed.
*   Files intended for next edit.
*   Commands/tests run.
*   Current blockers.
*   Next recommended agent or tool.

### 6. File Ownership Rule
The active editor must record the files it plans to edit in workflow state or handoff notes. No other AI tool may modify those files until the active editor marks the work complete, blocked, or handed off.

### 7. Project Boundary Rule
An AI tool must only read, edit, format, test, stage, commit, or push files inside the active project worktree. It must not touch another project, sibling folder, global config, shared dependency cache, or unrelated workspace unless the human explicitly approves that external path for the current task.

If a required change appears outside the active project root, the AI tool must pause and report:
*   External path needed.
*   Reason it is needed.
*   Risk of changing it.
*   Safer in-project alternative if available.

### 8. Unexpected Change Rule
If any AI tool sees unexpected modified, staged, deleted, or untracked files, it must pause before editing and report the exact files. Never overwrite, revert, or clean changes made by another tool or human unless explicitly approved.

### 9. Parallel Work Exception
If real parallel implementation is required, use separate branches or separate git worktrees. Same-worktree parallel file edits are not allowed.

### 10. Git Authority
Codex, Claude, Antigravity, and Copilot must not commit, push, tag, merge, or deploy from the shared worktree unless the Supervisor has approved the gate and DevOps/Supervisor flow explicitly allows that git action.

---

## SOP 14: Token Economy & Context Efficiency

All agents must protect the human's token budget. Use the smallest safe context needed to complete the task, and avoid repeated explanations, broad file dumps, and unnecessary multi-pass analysis.

### 1. Default Response Mode
Agents must answer in concise mode by default:
*   Give the decision, action, or result first.
*   Use short bullets only when they help.
*   Avoid repeating already-approved history unless it changes the decision.
*   Do not produce long plans unless the user asks or the task is high-risk.

### 2. File Reading Rule
Before reading large files or many files, agents must:
*   Use targeted search such as `rg`.
*   Read only relevant snippets.
*   Avoid full-file dumps unless required.
*   Reuse known workflow state and handoff notes instead of rediscovering everything.

### 3. Tool Usage Rule
Agents must avoid noisy or redundant commands:
*   Do not run broad scans when a targeted command is enough.
*   Do not repeat the same command unless state changed.
*   Run parallel read-only checks only when it saves time and context.
*   Summarize command output instead of pasting long logs.

### 4. Handoff Compression Rule
Every handoff should be compact and actionable:
*   Current stage.
*   Files changed.
*   Tests/checks run.
*   Blockers.
*   Next exact action.

### 5. Ask-First Rule for Expensive Work
Agents must ask before doing high-token or high-cost work such as deep repository scans, full documentation rewrites, broad security audits, large refactors, or internet research, unless the user explicitly requested that depth.

### 6. Supervisor Enforcement
Supervisor must reject outputs that waste tokens through repeated context, unnecessary detail, irrelevant file dumps, or broad analysis that was not requested.
