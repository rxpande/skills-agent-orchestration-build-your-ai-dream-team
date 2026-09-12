# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate the custom agent team building Mona's Project Pulse dashboard.

| Agent | Target model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the specialist agents, divides the work into phases, assigns non-overlapping file scopes, manages dependencies, and verifies the integrated result. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, risks, edge cases, and validation needs, then creates the implementation plan for the Orchestrator. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements application logic and runnable-app support within the assigned scope, using clear, deterministic, explicit, and testable code. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Shapes the dashboard's UI/UX, information hierarchy, accessibility, responsive behavior, visual clarity, project cards, status badges, priorities, and styling hooks. | `.github/agents/designer.agent.md` |
