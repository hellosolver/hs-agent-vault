# Standard Operating Procedure (SOP) for Agent Interaction & Workflow

This Standard Operating Procedure (SOP) defines how **You (the human developer)** must coordinate, interact with, and hand over tasks across the 12-Agent Standard Pathway team. Follow these steps strictly to ensure zero context leakage, clear progression, security validation, marketing readiness, and robust E2E verification.

---

## 🔄 Step-by-Step Agent Workflow Lifecycle

Every new feature request, visual QA ticket, security ticket, or bug report must traverse this lifecycle. Never skip any step. Downstream handovers are strictly conditional: if any security or testing step fails, the pipeline halts immediately and returns to the correct upstream agent.

```text
   You (Raw Request)
        ↓
   [1_PromptAgent] ─────────────► Translates request & scans code context
        ↓
   [2_ProductOwner] ────────────► Decides ROI, roadmap phase, and task priority
        ↓
   [3_Architect] ───────────────► Creates DB schema, API design, migration plan
        ↓
   [4_SecurityAuditor] ─────────► Threat model & vulnerability gate
        ↓ [PASS]
   [5_UIUX] ────────────────────► Maps wireframes, padding, dynamic loading UI states
        ↓
   [6_Developer] ───────────────► Implements code & verifies compile build ◄────────┐
        ↓                                                                          │
   [7_GUITester] ───────────────► Audits layout alignment ──► [FAIL] ──────────────┤
        ↓ [PASS]                                                                   │
   [8_CLITester] ───────────────► Runs API/security negative tests ──► [FAIL] ─────┤
        ↓ [PASS]                                                                   │
   [4_SecurityAuditor] ─────────► Release security re-check ──► [FAIL] ────────────┤
        ↓ [PASS]                                                                   │
   [9_HumanTester] ─────────────► Simulates role switching ──► [FAIL] ─────────────┘
        ↓ [PASS]
   [10_Supervisor] ─────────────► Audits gates and signs off approval
        ↓
   [11_DevOpsMonitor] ──────────► Executes approved git/deploy operations
```

---

## 📋 SOP 1: Initiating a Task (The Bootstrap Phase)

When starting a fresh feature or bug fix:
1.  **Open a New Chat** in your IDE/assistant workspace inside the target project directory.
2.  **Bootstrap the Prompt Agent**: Paste the new request and point the assistant to the central prompt file:
    > *"Aapko is chat me **Prompt Agent** ka role play karna hai. Apne standard instructions ko read karne ke liye is path par view karein: **[AgentTeamRoot]/AgentTeam/1_PromptAgent.md**. Usne read karne ke baad, is project ke active files aur mere raw request ko analyze karke optimized implementation plan aur downstream system prompts generate karein. Raw request: [Enter raw request here]"*
3.  **Save Output & Create FSC**: Copy the Prompt Agent's implementation plan into your notes. When bootstrapping the **Product Owner**, ensure they write the official requirements contract into `.ai-control/feature_spec.json` (modeled after `feature_spec.json.example`).

---

## 📋 SOP 2: Managing Handovers (Agent-to-Agent Transition)

To move a task from one agent to the next:
1.  **Start a New Chat** (or clear active context) to prevent historical chat contamination.
2.  **Bootstrap the Next Agent**: Supply the path to their respective central prompt file, feed them the exact output of the previous agent, and always supply the active `.ai-control/feature_spec.json` requirements contract.
3.  **Security Auditor Required**: After Architect output, always route to `4_SecurityAuditor.md` before UI/UX or Developer work. After CLI/API PASS, route back to `4_SecurityAuditor.md` for release re-check before Human Tester.
4.  **Provide Live Preview Info**: When handing over from Developer to GUI Tester, supply the active local preview URL (e.g. `[Local Preview URL]`) or screenshot paths.
5.  **Strict Conditional Handover Lock**: If GUI, CLI/API, Security Auditor, or Human Tester outputs FAIL, halt progression. Never bootstrap downstream agents on a failed build.
6.  **Keep `.ai-control` Updated**: Make sure Developer or Supervisor updates status files like `workflow_state.json` and `next_action.md` at each stage.
7.  **Shared Worktree Continuity Lock**: Codex, Claude, Antigravity, and Copilot may use one worktree only when one active editor lock is recorded, project-root boundary is confirmed, file ownership is clear, and every handoff includes changed files, tests run, current branch, blockers, and next action.
8.  **Token Economy Lock**: Keep handovers short and actionable. Use targeted search, avoid repeated history, avoid full-file dumps, summarize long logs, and ask before expensive deep scans or rewrites.

---

## 📋 SOP 3: Handling Bug Fixes, Security Fixes & Rollbacks

If the **GUI Tester**, **CLI Tester**, **Security Auditor**, or **Human Tester** flags any visual, structural, security, or logical failure:
1.  **Halt the Pipeline**: Do not proceed to Supervisor Approval or downstream testing.
2.  **Document the Issue**: Copy exact error logs, screen fail summaries, vulnerability notes, or failing test specs.
3.  **Route to Correct Owner**:
    * Architecture/security-design flaw -> `3_Architect.md`
    * Code/security implementation flaw -> `6_Developer.md`
    * UX/security confusion or trust flaw -> `5_UIUX.md`
4.  **Restart Verification Loop**: Once fixed, route back through GUI Tester, CLI/API Tester, Security Auditor release re-check, and Human Tester from scratch.

---

## 📋 SOP 4: Final Sign-off & Infrastructure Handover

Before deployment:
1.  **Bootstrap the Supervisor**: Give the Supervisor all PASS reports from GUI, CLI/API, Security Auditor pre-build, Security Auditor release re-check, and Human Tester:
    > *"Aapko **Supervisor Agent** **[AgentTeamRoot]/AgentTeam/10_Supervisor.md** ke context me all testing and security reports ko audit karke final approval/deployment status output karna hai. Reports: [Paste GUI, CLI/API, Security, Human pass reports]"*
2.  **DevOps & Monitor Run**: Once Supervisor allows deployment, bootstrap the **DevOps & Monitor Agent** with the approved build details:
    > *"Aap **DevOps & Monitor Agent** **[AgentTeamRoot]/AgentTeam/11_DevOpsMonitor.md** ke context me approved git promotion, build pipelines, system telemetry check, aur environment variables verify karke deploy kijiye."*
