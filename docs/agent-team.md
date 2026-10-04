# Agent Team

This exercise uses the custom agents located in `.github/agents/` and GitHub Copilot CLI running in a GitHub Codespace to orchestrate the Project Pulse dashboard work.

## Orchestrator
File: `.github/agents/orchestrator.agent.md`
Model: Claude Opus 4.7
Responsibility: Coordinates the Planner, Designer, and Coder agents, manages handoffs, and orchestrates the Project Pulse workflow.

## Planner
File: `.github/agents/planner.agent.md`
Model: Claude Opus 4.7
Responsibility: Creates implementation phases, defines dependencies, ownership information, validation criteria, and project planning.

## Designer
File: `.github/agents/designer.agent.md`
Model: Gemini 3.1 Pro
Responsibility: Designs the dashboard layout, user experience, responsive behavior, accessibility guidance, and visual structure.

## Coder
File: `.github/agents/coder.agent.md`
Model: GPT-5.5
Responsibility: Implements the dashboard files, creates configuration files, generates project assets, and validates the solution.

## Team Collaboration

The Orchestrator coordinates the overall process.

The Planner creates the implementation plan.

The Designer defines the Project Pulse dashboard design and user experience.

The Coder implements the dashboard files and supporting configuration.

All custom agent definitions are stored under `.github/agents/` and work together to build Mona's Project Pulse dashboard.
