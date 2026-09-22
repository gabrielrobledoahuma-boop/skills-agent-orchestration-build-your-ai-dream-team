# Agent team

The Project Pulse dashboard will be built by a custom agent team orchestrated through GitHub Copilot CLI in a Codespace.

- **Orchestrator** - Uses **Claude Opus 4.7 (copilot)** to coordinate the work, delegate tasks to the specialist agents, manage file ownership and dependencies, and verify the integrated result. Definition: [.github/agents/orchestrator.agent.md](../.github/agents/orchestrator.agent.md).
- **Planner** - Uses **Claude Opus 4.7 (copilot)** to research the repository, documentation, dependencies, risks, and edge cases, then produce an implementation plan with file assignments, sequencing, parallel work, and validation expectations. Definition: [.github/agents/planner.agent.md](../.github/agents/planner.agent.md).
- **Coder** - Uses **GPT-5.5 (copilot)** to implement assigned code, fix bugs, create the Project Pulse launch configuration when requested, and validate deterministic, testable behavior. Definition: [.github/agents/coder.agent.md](../.github/agents/coder.agent.md).
- **Designer** - Uses **Gemini 3.1 Pro (copilot)** to shape the dashboard's UI/UX, accessibility, information hierarchy, interaction flow, responsive behavior, and polished visual design. Definition: [.github/agents/designer.agent.md](../.github/agents/designer.agent.md).

The Orchestrator starts with the Planner, assigns implementation work to the Coder and visual work to the Designer where their file scopes permit parallel execution, and then integrates and validates the result for Mona's Project Pulse dashboard.
