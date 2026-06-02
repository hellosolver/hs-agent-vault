# HelloSolver: Agents Interaction & Workflow SOP

This Standard Operating Procedure (SOP) defines how **You (the human developer)** must coordinate, interact with, and hand over tasks across the 10-Agent Standard Pathway team. Follow these steps strictly to ensure zero context leakage, clear progression, and robust E2E verification.

---

## 🔄 Step-by-Step Agent Workflow Lifecycle

Every new feature request, visual QA ticket, or bug report must traverse this lifecycle. Never skip any step.

```text
  You (Raw Request)
       ↓
  [2_PromptAgent] ──────────► Translates request & scans code context
       ↓
  [3_ProductOwner] ─────────► Decides ROI, roadmap phase, and task priority
       ↓
  [4_Architect] ────────────► Creates DB schema, API design, & runs Security Check
       ↓
  [5_UIUX] ─────────────────► Maps wireframes, padding, dynamic loading UI states
       ↓
  [6_Developer] ────────────► Implements code & verifies compile build
       ↓
  [7_GUITester] ◄──────────┐► Audits layout alignment & escaped HTML entities
       ↓                   │
  [8_CLITester] ───────────┼► Runs Playwright smoke suite (npm run test:p0)
       ↓                   │
  [9_HumanTester] ─────────┼► Simulates role switching & chat portal close CTAs
       ↓                   │
       ├─► [FAIL] ─────────┘ (Bugs detected ──► developer fixes ──► re-test loops)
       │
       └─► [PASS] ──► [1_Supervisor] ──► Audits logs and signs off approval
                            ↓
                      [10_DevOpsMonitor] ──► Deploys code to Staging/Production
```

---

## 📋 SOP 1: Initiating a Task (The Bootstrap Phase)

When starting a fresh feature or bug fix:
1.  **Open a New Chat** in your IDE/assistant workspace inside the target project directory.
2.  **Bootstrap the Prompt Agent**: Paste the naye request and point the assistant to the central prompt file:
    > *"Aapko is chat me **Prompt Agent** ka role play karna hai. Apne standard instructions ko read karne ke liye is path par view karein: **[2_PromptAgent.md](file:///C:/Users/User/.gemini/antigravity/scratch/AgentTeam/2_PromptAgent.md)**. Use read karne ke baad, is project ke active files aur mere raw request ko analyze karke optimized implementation plan aur downstream system prompts generate karein. Raw request: [Enter raw request here]"*
3.  **Save Output**: Copy the Prompt Agent's implementation plan and generated prompts into a scratch file or notepad.

---

## 📋 SOP 2: Managing Handovers (Agent-to-Agent Transition)

To move a task from one agent to the next:
1.  **Start a New Chat** (or clear active context) to prevent historical chat contamination.
2.  **Bootstrap the Next Agent**: Supply the path to their respective central prompt file and feed them the exact output of the *previous* agent:
    > *"Aapko is chat me **[Agent Role]** ka role play karna hai. Apne rules aur constraints ke liye is path par view karein: **[Agent_System_Prompt_Path]**. Preceding Agent ki output ye rahi: [Paste output here]. Ab iske basis par apna task execute karein."*
3.  **Keep `.ai-control` Updated**: Make sure the Developer or Supervisor agent updates `workflow_state.json` and `next_action.md` at each stage.

---

## 📋 SOP 3: Handling Bug Fixes & Rollbacks (The Re-Test Loop)

If the **GUI Tester**, **CLI Tester**, or **Human Tester** flags any visual, structural, or logical failure:
1.  **Halt the Pipeline**: Do not proceed to Supervisor Approval.
2.  **Document the Bug**: Copy the exact error logs, screen fail summaries, or failing test specs.
3.  **Rollback to Developer**: Open a chat context with the **Developer Agent**:
    > *"Aapko **Developer Agent** ke system rules: **[6_Developer.md](file:///C:/Users/User/.gemini/antigravity/scratch/AgentTeam/6_Developer.md)** ke path ke basis par is bug ko fix karna hai. Tester agent ki fail reports ye hain: [Paste failing test logs/GUI issues]. Bug fix karne ke baad naye code changes provide karein."*
4.  **Restart Verification Loop**: Once the Developer completes the fix, **you must route the code back to the GUI Tester and CLI Tester** to run E2E suites (`npm run test:p0`) from scratch.

---

## 📋 SOP 4: Final Sign-off & Infrastructure Handover

Before deployment:
1.  **Bootstrap the Supervisor**: Give the Supervisor all pass reports from the GUI, CLI, and Human testers:
    > *"Aapko **Supervisor Agent** **[1_Supervisor.md](file:///C:/Users/User/.gemini/antigravity/scratch/AgentTeam/1_Supervisor.md)** ke context me all testing reports ko audit karke final approval/deployment status output karna hai. Reports: [Paste GUI, CLI, Human pass reports]"*
2.  **DevOps & Monitor Run**: Once the Supervisor allows deployment, bootstrap the **DevOps & Monitor Agent** with the approved build details:
    > *"Aap **DevOps & Monitor Agent** **[10_DevOpsMonitor.md](file:///C:/Users/User/.gemini/antigravity/scratch/AgentTeam/10_DevOpsMonitor.md)** ke context me Vercel build pipelines, Sentry log checks, aur environment variables check verify karke deploy kijiye."*
