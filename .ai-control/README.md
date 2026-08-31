# Robust Multi-Agent Standard Pathway Vault

This repository contains the centralized master prompts, standard operating procedures, and lifecycle workflows for the **14-Agent Standard Pathway Team** plus the Master SDLC Orchestrator. It is designed to be fully generic, credential-free, and reusable across development codebases.

## 📂 Repository Structure

*   `AgentTeam/` - Contains the Master Orchestrator, 14 role-based system prompts, lifecycle workflow mappings, operational guidelines, and AI behavioral rules.
    *   `0_OrchestratorLoop.md` - Automatic SDLC loop coordinator, agent router, state manager, and gate controller.
    *   `1_PromptAgent.md` - Raw request translation and context mapping instructions.
    *   `2_ProductOwner.md` - Priority logic, ROI analysis, and FSC creation.
    *   `3_Architect.md` - Schema, API, migration, and security design specifications.
    *   `4_SecurityAuditor.md` - Threat modeling, vulnerability review, and security gatekeeping.
    *   `5_UIUX.md` - Mobile-first UX and grid system guidelines.
    *   `6_Developer.md` - Primary builder code rules and local verification.
    *   `7_GUITester.md` - Spacing, alignment, and translation visual test rules.
    *   `8_CLITester.md` - API contract and automated test suite runner guidelines.
    *   `9_HumanTester.md` - Usability trust audits.
    *   `10_Supervisor.md` - Lifecycle coordinator and gatekeeper instructions.
    *   `11_DevOpsMonitor.md` - Git promotion, deployment, telemetry, and runbook operations.
    *   `12_MarketingStrategy.md` - Go-to-market, positioning, launch, and growth strategy.
    *   `13_GooglePlayStoreManager.md` - Google Play Store operations, policy, Data safety, listing, ASO, release, reviews, and monitoring.
    *   `14_LegalComplianceAdvisor.md` - Legal risk mapping, compliance readiness, policy documents, dispute risk, and human lawyer prep.
    *   `agents_workflow_sop.md` - Steps for human-agent coordination and rollback bug fixes.
    *   `workflow.md` - Flowcharts and recursive retest loop mapping.
    *   `sop.md` - Loader, traceability, git, gate, documentation, secrets, and migration rules.
    *   `ai_rules.md` - Strict AI behavioral constraints and visual locks.
    *   `dev_rules.md` - Standard CSS layout spacing grid and dimension locks.
    *   `project_start_checklist.md` - Default readiness checklist before new or existing project development.
    *   `runbooks/` - Reusable runbook SOP and templates.
    *   `troubleshooting/` - Repeated issue and root-cause templates.

## 🚀 How to Use

To run the automatic SDLC loop in any coding workspace, bootstrap the orchestrator:

```text
Aapko is chat me Master SDLC Orchestrator ka role play karna hai.
Apne master rules read karne ke liye is path par view karein:
[AgentTeamRoot]/.ai-control/AgentTeam/0_OrchestratorLoop.md
Meri raw request ko automatic agent workflow se execute karein.
```

To use a specific agent directly, bootstrap your active assistant context by referencing the local path of the desired agent:

```text
Aapko is chat me Full Stack Developer Agent ka role play karna hai. 
Apne standard laws aur coding rules ko read karne ke liye is path par view karein: 
[AgentTeamRoot]/AgentTeam/6_Developer.md
Use complete read karne ke baad, mere active project files ko analyze karein aur task execute karein.
```

---
*Created and maintained by the AI Team Vault Prompt Engineer Agent.*
