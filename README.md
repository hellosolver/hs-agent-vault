# Robust Multi-Agent Standard Pathway Vault

This repository contains the centralized master prompts, standard operating procedures, and lifecycle workflows for the **10-Agent Standard Pathway Team**. It is designed to be fully generic, credential-free, and reusable across development codebases.

## 📂 Repository Structure

*   `AgentTeam/` - Contains the 10 role-based system prompts, lifecycle workflow mappings, operational guidelines, and AI behavioral rules.
    *   `1_PromptAgent.md` - Raw request translation and context mapping instructions.
    *   `2_ProductOwner.md` - Priority logic, ROI analysis, and FSC creation.
    *   `3_Architect.md` - Schema, API, migration, and security design specifications.
    *   `4_UIUX.md` - Mobile-first UX and grid system guidelines.
    *   `5_Developer.md` - Primary builder code rules and local verification.
    *   `6_GUITester.md` - Spacing, alignment, and translation visual test rules.
    *   `7_CLITester.md` - API contract and automated test suite runner guidelines.
    *   `8_HumanTester.md` - Usability trust audits.
    *   `9_Supervisor.md` - Lifecycle coordinator and gatekeeper instructions.
    *   `10_DevOpsMonitor.md` - Git promotion, deployment, telemetry, and runbook operations.
    *   `agents_workflow_sop.md` - Steps for human-agent coordination and rollback bug fixes.
    *   `workflow.md` - Flowcharts and recursive retest loop mapping.
    *   `sop.md` - Loader, traceability, git, gate, documentation, secrets, and migration rules.
    *   `ai_rules.md` - Strict AI behavioral constraints and visual locks.
    *   `dev_rules.md` - Standard CSS layout spacing grid and dimension locks.
    *   `runbooks/` - Reusable runbook SOP and templates.
    *   `troubleshooting/` - Repeated issue and root-cause templates.

## 🚀 How to Use

To use these centralized agent roles in any coding workspace, bootstrap your active assistant context by referencing the local path of the desired agent:

```text
Aapko is chat me Full Stack Developer Agent ka role play karna hai. 
Apne standard laws aur coding rules ko read karne ke liye is path par view karein: 
[AgentTeamRoot]/AgentTeam/5_Developer.md
Use complete read karne ke baad, mere active project files ko analyze karein aur task execute karein.
```

---
*Created and maintained by the AI Team Vault Prompt Engineer Agent.*
