# Standard Operating Procedure (SOP) for Agent Interaction & Workflow

This Standard Operating Procedure (SOP) defines how **You (the human developer)** must coordinate, interact with, and hand over tasks across the 10-Agent Standard Pathway team. Follow these steps strictly to ensure zero context leakage, clear progression, and robust E2E verification.

---

## 🔄 Step-by-Step Agent Workflow Lifecycle

Every new feature request, visual QA ticket, or bug report must traverse this lifecycle. Never skip any step. Downstream handovers are strictly conditional: if any testing step fails, the pipeline halts immediately and returns to the Developer.

```text
   You (Raw Request)
        ↓
   [1_PromptAgent] ──────────► Translates request & scans code context
        ↓
   [2_ProductOwner] ─────────► Decides ROI, roadmap phase, and task priority
        ↓
   [3_Architect] ────────────► Creates DB schema, API design, & runs Security Check
        ↓
   [4_UIUX] ─────────────────► Maps wireframes, padding, dynamic loading UI states
        ↓
   [5_Developer] ────────────► Implements code & verifies compile build ◄───────────┐
        ↓                                                                    │
   [6_GUITester] ───────────► Audits layout alignment ──► [FAIL] ────────────┤
        ↓ [PASS]                                                             │
   [7_CLITester] ───────────► Runs automated smoke suites ──► [FAIL] ────────┤ (Bug Fix Re-Test Loop)
        ↓ [PASS]                                                             │
   [8_HumanTester] ─────────► Simulates role switching ──► [FAIL] ───────────┘
        ↓ [PASS]
   [9_Supervisor] ──────────► Audits logs and signs off approval
        ↓
   [10_DevOpsMonitor] ──────► Deploys code to Staging/Production
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
2.  **Bootstrap the Next Agent**: Supply the path to their respective central prompt file, feed them the exact output of the *previous* agent, and **always supply the active `.ai-control/feature_spec.json` requirements contract**:
    > *"Aapko is chat me **[Agent Role]** ka role play karna hai. Apne rules aur constraints ke liye is path par view karein: **[Agent_System_Prompt_Path]**. Preceding Agent ki output ye rahi: [Paste output here]. Active Requirements Contract (feature_spec.json) ye raha: [Paste JSON contents]. Ab iske basis par apna task execute karein."*
3.  **Provide Live Preview Info (Developer to GUI Tester)**: When handing over from the Developer to the GUI Tester, you must supply the active local preview URL (e.g. `[Local Preview URL]`) or screenshot paths to allow the GUI Tester to perform visual checks.
4.  **Strict Conditional Handover Lock**: If any testing agent (GUI, CLI, or Human) outputs a **FAIL** status, the human operator **must immediately halt** the progression. Never bootstrap any downstream tester or supervisor agent on a failed build. The only permitted next action is to route the failed ticket back to the Developer.
5.  **Keep `.ai-control` Updated**: Make sure the Developer or Supervisor agent updates status files (like `workflow_state.json` and `next_action.md`) at each stage.

---

## 📋 SOP 3: Handling Bug Fixes & Rollbacks (The Re-Test Loop)

If the **GUI Tester**, **CLI Tester**, or **Human Tester** flags any visual, structural, or logical failure:
1.  **Halt the Pipeline**: Do not proceed to Supervisor Approval or any downstream testing step.
2.  **Document the Bug**: Copy the exact error logs, screen fail summaries, or failing test specs.
3.  **Rollback to Developer**: Open a chat context with the **Developer Agent**:
    > *"Aapko **Developer Agent** ke system rules: **[AgentTeamRoot]/AgentTeam/5_Developer.md** ke path ke basis par is bug ko fix karna hai. Tester agent ki fail reports ye hain: [Paste failing test logs/GUI issues]. Bug fix karne ke baad naye code changes aur active preview URL details provide karein."*
4.  **Restart Verification Loop**: Once the Developer completes the fix, **you must route the code back to the GUI Tester and CLI Tester** to run automated suites from scratch. Do not bypass the GUI and CLI testers.

---

## 📋 SOP 4: Final Sign-off & Infrastructure Handover

Before deployment:
1.  **Bootstrap the Supervisor**: Give the Supervisor all pass reports from the GUI, CLI, and Human testers:
    > *"Aapko **Supervisor Agent** **[AgentTeamRoot]/AgentTeam/9_Supervisor.md** ke context me all testing reports ko audit karke final approval/deployment status output karna hai. Reports: [Paste GUI, CLI, Human pass reports]"*
2.  **DevOps & Monitor Run**: Once the Supervisor allows deployment, bootstrap the **DevOps & Monitor Agent** with the approved build details:
    > *"Aap **DevOps & Monitor Agent** **[AgentTeamRoot]/AgentTeam/10_DevOpsMonitor.md** ke context me build pipelines, system telemetry check, aur environment variables check verify karke deploy kijiye."*

