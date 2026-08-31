# Standard Pathway: Agent Team Workflow

This document defines the finalized **Standard Pathway Lifecycle Workflow** and gatekeeping rules for the multi-agent development team. The Master Orchestrator (`0_OrchestratorLoop.md`) may coordinate this workflow automatically while preserving the same gates. Every feature, bug fix, and structural modification must adhere strictly to this pipeline.

---

## 🔄 The 11-Stage Standard Pathway Workflow

The lifecycle starts at Prompt engineering, sequences through prioritization, architecture, dedicated security auditing, design, and implementation, passes a multi-layered verification loop, and concludes with deployment and production telemetry. If any testing or security step fails, the pipeline halts immediately and routes back to the correct upstream agent.

```text
       You (User Request)
        ↓
  0. Master SDLC Orchestrator (Automatic Router)
        ↓
  1. Prompt Agent (Requirement Translator)
        ↓
  2. Product Owner Agent (Priority Manager)
        ↓
  3. Architect Agent (Solution Designer)
        ↓
  4. Security Auditor Agent (Threat Model & Vulnerability Gate)
        ↓ [PASSED]
  5. UI/UX Agent (Design Expert)
        ↓
  6. Full Stack Developer Agent (Initial Code Builder) ◄─────────────────────┐
        ↓                                                                    │
  7. GUI Tester Agent (Visual QA) ──────► [FAILED] ──────────────────────────┤
        ↓ [PASSED]                                                           │
  8. CLI & API Tester Agent (Backend & DB Validator) ──────► [FAILED] ───────┤
        ↓ [PASSED]                                                           │
  4. Security Auditor Agent (Release Re-Check) ──────► [FAILED] ─────────────┤
        ↓ [PASSED]                                                           │
  9. Human Tester Agent (User Simulator) ──────► [FAILED] ───────────────────┘
        ↓ [PASSED]
  10. Supervisor Agent (Final Approval)
        ↓
  11. DevOps & Monitor (Common Gate -> develop -> staging -> main)
        ↓
  Production Stack
```

```text
Common Gate Pipeline:
Build PASS
  -> Security Auditor PASS
  -> GUI Tester PASS
  -> CLI/API Tester PASS
  -> Security Release Re-Check PASS
  -> Human Tester PASS
  -> RTM Traceability PASS
  -> Documentation / runbook / root-cause check PASS
  -> Supervisor manual approval action
  -> DevOps git/deploy execution
```

```text
Promotion Sequence:
Local Common Gate PASS
  -> Supervisor approves develop push
  -> DevOps pushes to develop
  -> Dev URL Common Gate PASS
  -> Supervisor manually approves staging promotion
  -> DevOps promotes to staging
  -> Staging URL Common Gate PASS
  -> Supervisor manually approves production promotion
  -> DevOps promotes to main / production
```

---

## 🛡️ Operational Directives & Handover Criteria

*   **Step 1: Prompt Agent**
    *   *Input:* Raw request / bug report.
    *   *Action:* Gathers workspace context and outputs optimized system prompts and file implementation plans.
    *   *Next:* `2_ProductOwner.md`
*   **Step 2: Product Owner Agent**
    *   *Input:* Prompt Agent plans and options.
    *   *Action:* Determines priority backlogs, schedules feature phases, and performs ROI analysis.
    *   *Next:* `3_Architect.md`
*   **Step 3: Architect Agent**
    *   *Input:* Prioritized roadmaps.
    *   *Action:* Approves schemas, API contracts, data flow, migration safety requirements, and technical feasibility.
    *   *Next:* `4_SecurityAuditor.md`
*   **Step 4: Security Auditor Agent**
    *   *Input:* Architecture, API contracts, auth/session design, dependency context, and environment rules.
    *   *Action:* Runs threat modeling, vulnerability review, secrets review, auth/session review, and security test planning.
    *   *Next:* `5_UIUX.md` **(Only if PASS; if FAIL, return to `3_Architect.md`)**
*   **Step 5: UI/UX Agent**
    *   *Input:* Technical architecture, API specs, and Security Auditor constraints.
    *   *Action:* Designs user flows, audits typography/spacing rules against `dev_rules.md`, and standardizes components.
    *   *Next:* `6_Developer.md`
*   **Step 6: Full Stack Developer Agent**
    *   *Input:* Design flows, API schemas, and security requirements.
    *   *Action:* Writes, edits, and optimizes frontend and backend code. Must run standard linting, tests, and build commands cleanly. Must document important changes and repeated/major issue root causes.
    *   *Next:* `7_GUITester.md`
*   **Step 7: GUI Tester Agent**
    *   *Input:* Live local preview URL/port and visual screenshots from the developer.
    *   *Action:* Audits visual responsiveness, decoded option text, and button sizing locks using `dev_rules.md`.
    *   *Next:* `8_CLITester.md` **(Only if PASS; if FAIL, return to `6_Developer.md`)**
*   **Step 8: CLI/API Tester Agent**
    *   *Input:* Codebase, API controllers, database config, security abuse cases, and E2E test suites.
    *   *Action:* Runs automated tests, validates database states, verifies migration/rollback behavior, and executes Security Auditor negative tests.
    *   *Next:* `4_SecurityAuditor.md` for release re-check **(Only if PASS; if FAIL, return to `6_Developer.md`)**
*   **Step 9: Human Tester Agent**
    *   *Input:* Tested application state after security release re-check.
    *   *Action:* Simulates live multi-role operations, checks context cache resets, and tests interaction portals.
    *   *Next:* `10_Supervisor.md` **(Only if PASS; if FAIL, return to `6_Developer.md`)**
*   **Step 10: Supervisor Agent**
    *   *Input:* Approvals from all preceding agents, including pre-build and release Security Auditor reports.
    *   *Action:* Inspects workflow state logs, git status, branch pathway, Common Gate Pipeline results, changed files, RTM coverage, documentation/runbook/root-cause records, security gates, marketing launch readiness when in scope, and local/dev/staging reports before manual approval.
    *   *Next:* `11_DevOpsMonitor.md` **(Strict reject if any preceding test/security gate was bypassed or failed)**
*   **Step 11: DevOps & Monitor Agent**
    *   *Input:* Supervisor-approved builds.
    *   *Action:* Executes only Supervisor-approved git stage/commit/push/tag operations after each Common Gate Pipeline pass and maintains deployment runbooks/release notes.

---

## 🚫 Standard Pathway Failure Triggers & Pipeline Blocks

If any validator or security agent flags critical failures, the pipeline must halt, route back to the correct upstream agent, and re-trigger the verification loop after the fix:

1.  **Security Gate Failure:** Unresolved critical/high vulnerability, auth bypass, injection risk, data exposure, unsafe secret, or missing security test.
2.  **Sizing Lock Mismatches:** Deviation from locked classes, padding, or min-height of UI cards or buttons defined in `dev_rules.md`.
3.  **Stuck UI Loaders:** Buttons that do not clear loading spinners inside a `finally` block or fail to prevent double-clicks.
4.  **Stale Dashboard Context:** Failure to clear local caches when switching dashboard roles.
5.  **Unescaped HTML entities:** Dropdowns or option text nodes rendering raw entity characters (e.g. `&amp;` instead of `&`).
6.  **Chat Drawer Bugs:** Dynamic communication panels missing Close buttons or ignoring keyboard `Escape` exits.
7.  **Obsolete Routing / Link Leakage:** Attempting to render legacy or out-of-scope pages locally rather than redirecting externally to configured domains.
8.  **Environment Test Bypass:** Reusing local test results for dev or staging approval, or promoting without fresh URL-specific PASS reports.
9.  **Manual Approval Bypass:** Pushing to `develop`, promoting to `staging`, or promoting to `main` without a recorded Supervisor manual approval action.
10. **Missing Documentation:** Repeated issues, major issues, deployment changes, or important decisions lacking runbook, troubleshooting, release-note, or decision records.
11. **Unsafe Secrets:** Hardcoded credentials, committed real environment files, or unredacted secret values in logs, docs, screenshots, or test reports.
12. **Unsafe Migration:** Schema/data changes without migration plan, rollback plan, and consistency verification.
