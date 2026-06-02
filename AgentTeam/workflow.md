# HelloSolver Standard Pathway: Agent Team Workflow

This document defines the finalized **Standard Pathway Lifecycle Workflow** and gatekeeping rules for the multi-agent development team. Every feature, bug fix, and structural modification must adhere strictly to this pipeline.

---

## 🔄 The 10-Stage Standard Pathway Workflow

The lifecycle starts at Prompt engineering, sequences through prioritization, design, and initial implementation, passes a multi-layered verification testing loop, and concludes with deployment and production telemetry.

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
  5. Full Stack Developer Agent (Initial Code Builder)
        ↓
  6. GUI Tester Agent (Visual QA) ◄───────────────────────────┐
        ↓                                                     │
  7. CLI & API Tester Agent (Backend & DB Validator)           │ (Bug Fix Re-Test Loop)
        ↓                                                     │
  8. Human Tester Agent (User Simulator)                      │
        ↓                                                     │
        ├─► [PASSED] ─► 9. Supervisor Agent (Final Approval)  │
        │                       ↓                             │
        │               10. DevOps & Monitor (Deployment)     │
        │                       ↓                             │
        │                 Production Stack                    │
        │                                                     │
        └─► [FAILED] ──► Developer Agent (Bug Fixes) ─────────┘
```

---

## 🛡️ Operational Directives & Handover Criteria

*   **Step 1: Prompt Agent**
    *   *Input:* Raw request / bug report.
    *   *Action:* Gathers workspace context and outputs optimized system prompts and file implementation plans.
    *   *Next:* `3_ProductOwner.md`
*   **Step 2: Product Owner Agent**
    *   *Input:* Prompt Agent plans and options.
    *   *Action:* Determines priority backlogs, schedules feature phases, and performs ROI analysis.
    *   *Next:* `4_Architect.md`
*   **Step 3: Architect Agent**
    *   *Input:* Prioritized roadmaps.
    *   *Action:* Approves database schemas, defines API response contracts, and executes a pre-build Security Audit.
    *   *Next:* `5_UIUX.md`
*   **Step 4: UI/UX Agent**
    *   *Input:* Technical architecture and API specs.
    *   *Action:* Designs user flows, audits typography/spacing rules, and standardizes components.
    *   *Next:* `6_Developer.md`
*   **Step 5: Full Stack Developer Agent**
    *   *Input:* Design flows and API schemas.
    *   *Action:* Writes, edits, and optimises frontend and backend code files. Must run `npm run lint` and `npm run build` cleanly.
    *   *Next:* `7_GUITester.md`
*   **Step 6: GUI Tester Agent**
    *   *Input:* Live URL or screenshots.
    *   *Action:* Audits visual responsiveness, escapes HTML entities in dropdowns, and checks button sizing locks.
    *   *Next:* `8_CLITester.md`
*   **Step 7: CLI/API Tester Agent**
    *   *Input:* Codebase and E2E test suites.
    *   *Action:* Runs the automated smoke test runner (`npm run test:p0`) and validates database states.
    *   *Next:* `9_HumanTester.md`
*   **Step 8: Human Tester Agent**
    *   *Input:* Tested application state.
    *   *Action:* Simulates live multi-role operations, checks context cache resets, and tests chat portals.
    *   *Next:* `1_Supervisor.md`
*   **Step 9: Supervisor Agent**
    *   *Input:* Approvals from all preceding agents.
    *   *Action:* Inspects workflow state logs, signs off final gate clearance, and initiates deployment.
    *   *Next:* `10_DevOpsMonitor.md`
*   **Step 10: DevOps & Monitor Agent**
    *   *Input:* Approved builds.
    *   *Action:* Manages server deployments, audits environment sync files, tracks Sentry logs, and cleans FCM stale tokens.

---

## 🚫 Standard Pathway Failure Triggers & Pipeline Blocks

If any validator agent (GUI, CLI, or Human Tester) flags any of the following critical failures, the pipeline must immediately halt, revert to the **Developer Agent**, and re-trigger the verification loop after the fix is implemented:

1.  **Sizing Lock Mismatches:** Deviation from the locked classes, padding, or min-height of ServiceCard, ProductCard, or Nav button configs.
2.  **Stuck UI Loaders:** Buttons that do not clear loading spinners inside a `finally` block or fail to prevent double-clicks.
3.  **Stale Dashboard Context:** Failure to clear local caches when switching dashboard roles.
4.  **Unescaped HTML entities:** Dropdowns or option text nodes rendering raw entity characters (e.g. `&amp;`).
5.  **Chat Drawer Bugs:** Chat boxes missing Close buttons or ignoring keyboard `Escape` exits.
6.  **IT Solutions Leaking:** Attempting to render B2B IT consultancies inside the standard catalog instead of linking externally to `https://www.hellosolvertech.com/`.
