# Strict Universal AI Operational Rules

This document defines the **Strict AI Operational Rules** and **Behavioral Laws** that govern every AI Agent in the development pipeline. All agents must enforce these principles at all times.

---

## 🚫 1. Core Visual Sizing Locks (Tailwind / CSS Classes)
*   **Rule:** You are absolutely prohibited from modifying, overriding, or restructuring the locked CSS/Tailwind classes, padding, heights, or dimensions of standard UI components specified in the project's local developer rules (e.g., `.ai-control/rules/dev_rules.md`).
*   **Locked Elements:** Check the project's local developer rules for specific component locks (like card components, primary action buttons, navigation selectors, and mobile quick actions).
*   **Consequence:** Any layout deviation or modification is a visual QA regression **FAIL**.

---

## 🔒 2. Database & API Payload Security
*   **Rule:** Protect the database from account takeovers and malicious payload injection.
*   **Safe Mutation Protocol:** When updating user profiles, active transactions, or catalog items, backend API controllers must explicitly sanitize update payloads by stripping primary identity properties (e.g., `id`, `uid` fields) before executing database updates.
*   **CORS Checks:** CORS rules must strictly match authorized dev and API bridge ports specified in the project's environment configurations. No wildcards (`*`) are allowed in staging/production environment configs.

---

## 🔐 3. Secrets & Credential Safety
*   **Rule:** Never commit, print, paste, or expose secrets, API keys, tokens, passwords, certificates, or private environment values.
*   **Environment Files:** Real `.env` files must remain git-ignored. Only safe templates like `.env.example` may be committed.
*   **Hardcoded Secret Block:** Any hardcoded credential, token, webhook secret, private key, or service password is an immediate security FAIL.
*   **Exposure Response:** If a secret is exposed, halt the pipeline, rotate the credential, remove it from history/output where feasible, and document the incident.

---

## ⚡ 4. UI Loader & Asynchronous Button Laws
*   **Rule:** Every interactive button triggering backend API requests must display immediate loader feedback and disable itself on click to prevent double submissions.
*   **Finally Block Reset:** The loading state must ALWAYS be cleared inside the `finally` block of the try/catch loop. Leaving spinners spinning indefinitely on API errors is a strict **FAIL**.
*   **Dynamic Translation:** Dropdowns and option text pickers must decode HTML entities (e.g. render decoded characters instead of raw entity strings).

---

## 📂 5. Test Data & Zero Placeholder Principle
*   **Rule:** Never use dummy data, stock placeholders, or `lorem ipsum` mock text.
*   **E2E Test Accounts:** All automated and manual testing runs must exclusively use the authorized testing credentials specified in local git-ignored configurations or template runbooks (such as example credential files specified under `.ai-control/` or testing guides).
*   **No Random Profiling:** Do not register fake users, mock roles, or bypass standard verification flows (like OTP credentials) during testing.

---

## 🔄 6. Multi-Role Context & Cache Cleansing
*   **Rule:** Ensure absolute cache separation when switching dashboard roles.
*   **Cache Flushing:** Toggling between different user dashboard views must completely clear local state caches and context stores to prevent stale or mixed data from rendering.
*   **Sync View Legibility:** Live public display screens or status dashboards must mirror database status transitions in real-time without layout shifts or text overlaps.

---

## 🌐 7. Obsolete Routing & External Redirection Gate
*   **Rule:** Local integration of obsolete, legacy, or out-of-scope enterprise components is prohibited.
*   **External Routing:** All such external resources and redirection links must trigger clean route-level redirects to their dynamically configured external domains in a new tab (`target="_blank" rel="noopener noreferrer"`).

---

## 🤖 8. AI Token & Instruction Optimization
*   **Low-Token / High-Precision:** Keep communication direct, factual, and strictly technical. Avoid conversational fluff, hypothetical assumptions, or generic design templates.
*   **Active Context Harvesting:** Before editing code or writing specifications, you must read the active project's `.ai-control/` files to discover active staging guidelines, branch policies, and environment indexes.

---

## 9. Agent Role Boundary & Direct Handover
*   **Rule:** Every agent must only perform the work defined in its own role prompt.
*   **No Role Switching:** An agent must not silently switch into another agent's responsibility, write another agent's output, or approve another agent's gate.
*   **Direct Handover:** If the task is outside the current agent's scope, stop that portion and hand over directly to the relevant agent with concise context.
*   **No Proxy Work:** Do not perform Product Owner, Architect, Security Auditor, UI/UX, Developer, Tester, Supervisor, DevOps, Marketing, or Play Store Manager duties unless that is your active assigned role.
*   **Handover Format:** State `Out of scope for [current agent]`, name the relevant agent, list required input, and provide the next exact action.
*   **Supervisor Enforcement:** Any agent output that performs another agent's role without explicit handover is a workflow FAIL.
