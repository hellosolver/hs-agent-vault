# Standard Pathway: Agent Team Workflow

This document defines the finalized **Standard Pathway Lifecycle Workflow** and gatekeeping rules for the multi-agent development team. Every feature, bug fix, and structural modification must adhere strictly to this pipeline.

---

## 🔄 The 10-Stage Standard Pathway Workflow

The lifecycle starts at Prompt engineering, sequences through prioritization, design, and initial implementation, passes a multi-layered verification testing loop, and concludes with deployment and production telemetry. Handovers between testers are strictly conditional: if any testing step fails, the pipeline halts immediately and routes back to the Developer.

```text
       You (User Request)
        ↓
  1. Prompt Agent (Requirement Translator)
        ↓
  2. Product Owner Agent (Priority Manager)
        ↓
  3. Architect Agent (Solution Designer & Security Audit)
        ↓
  4. UI/UX Agent (Design Expert)
        ↓
  5. Full Stack Developer Agent (Initial Code Builder) ◄─────────────────────┐
        ↓                                                                    │
  6. GUI Tester Agent (Visual QA) ──────► [FAILED] ──────────────────────────┤
        ↓ [PASSED]                                                           │
  7. CLI & API Tester Agent (Backend & DB Validator) ──────► [FAILED] ───────┤ (Bug Fix Re-Test Loop)
        ↓ [PASSED]                                                           │
  8. Human Tester Agent (User Simulator) ──────► [FAILED] ───────────────────┘
        ↓ [PASSED]
  9. Supervisor Agent (Final Approval)
        ↓
  10. DevOps & Monitor (Common Gate -> develop -> staging -> main)
        ↓
  Production Stack
```

```text
Common Gate Pipeline:
Build PASS
  -> GUI Tester PASS
  -> CLI/API Tester PASS
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
    *   *Action:* Approves database schemas, defines API response contracts, executes a pre-build Security Audit, and defines migration safety requirements when needed.
    *   *Next:* `4_UIUX.md`
*   **Step 4: UI/UX Agent**
    *   *Input:* Technical architecture and API specs.
    *   *Action:* Designs user flows, audits typography/spacing rules against `dev_rules.md`, and standardizes components.
    *   *Next:* `5_Developer.md`
*   **Step 5: Full Stack Developer Agent**
    *   *Input:* Design flows and API schemas.
    *   *Action:* Writes, edits, and optimizes frontend and backend code files. Must run standard linting, tests, and compilation/build commands cleanly. Must document important changes and repeated/major issue root causes.
    *   *Next:* `6_GUITester.md`
*   **Step 6: GUI Tester Agent**
    *   *Input:* Live local preview URL/port and visual screenshots from the developer.
    *   *Action:* Audits visual responsiveness, escapes HTML entities in dropdowns, and checks button sizing locks using `dev_rules.md`.
    *   *Next:* `7_CLITester.md` **(Only if 100% PASS; if FAIL, immediately return to `5_Developer.md`)**
*   **Step 7: CLI/API Tester Agent**
    *   *Input:* Codebase and E2E test suites.
    *   *Action:* Runs the automated smoke test runner, validates database states, and verifies migration/rollback behavior when applicable.
    *   *Next:* `8_HumanTester.md` **(Only if 100% PASS; if FAIL, immediately return to `5_Developer.md`)**
*   **Step 8: Human Tester Agent**
    *   *Input:* Tested application state / staging deployment.
    *   *Action:* Simulates live multi-role operations, checks context cache resets, and tests interaction portals.
    *   *Next:* `9_Supervisor.md` **(Only if 100% PASS; if FAIL, immediately return to `5_Developer.md`)**
*   **Step 9: Supervisor Agent**
    *   *Input:* Approvals from all preceding agents.
    *   *Action:* Inspects workflow state logs, git status, branch pathway, Common Gate Pipeline results, changed files, RTM coverage, documentation/runbook/root-cause records, and separate local/dev/staging test reports before manual approval.
    *   *Next:* `10_DevOpsMonitor.md` **(Strict reject if any preceding test was bypassed or FAILed)**
*   **Step 10: DevOps & Monitor Agent**
    *   *Input:* Approved builds.
    *   *Action:* Executes only Supervisor-approved git stage/commit/push/tag operations after each Common Gate Pipeline pass and maintains deployment runbooks/release notes.

---

## 🚫 Standard Pathway Failure Triggers & Pipeline Blocks

If any validator agent (GUI, CLI, or Human Tester) flags any of the following critical failures, the pipeline must immediately halt, revert to the **Developer Agent**, and re-trigger the verification loop after the fix is implemented:

1.  **Sizing Lock Mismatches:** Deviation from the locked classes, padding, or min-height of UI cards or buttons defined in `dev_rules.md`.
2.  **Stuck UI Loaders:** Buttons that do not clear loading spinners inside a `finally` block or fail to prevent double-clicks.
3.  **Stale Dashboard Context:** Failure to clear local caches when switching dashboard roles.
4.  **Unescaped HTML entities:** Dropdowns or option text nodes rendering raw entity characters (e.g. `&amp;` instead of `&`).
5.  **Chat Drawer Bugs:** Dynamic communication panels missing Close buttons or ignoring keyboard `Escape` exits.
6.  **Obsolete Routing / Link Leakage:** Attempting to render legacy or out-of-scope pages locally rather than redirecting externally to the project's dynamically configured external domains.
7.  **Environment Test Bypass:** Reusing local test results for dev or staging approval, or promoting without fresh URL-specific GUI, CLI/API, and Human Tester PASS reports.
8.  **Manual Approval Bypass:** Pushing to `develop`, promoting to `staging`, or promoting to `main` without a recorded Supervisor manual approval action.
9.  **Missing Documentation:** Repeated issues, major issues, deployment changes, or important decisions lacking runbook, troubleshooting, release-note, or decision records.
10. **Unsafe Secrets:** Hardcoded credentials, committed real environment files, or unredacted secret values in logs, docs, screenshots, or test reports.
11. **Unsafe Migration:** Schema/data changes without migration plan, rollback plan, and consistency verification.
