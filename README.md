# Orchestration

Coordinate specialized AI agents (Orchestrator, Planner, Designer, Coder) to break down complex tasks, parallelize work, and deliver production-ready code with clear accountability.

**Branch:** This documentation covers the `Free-Orchestration` branch, optimized for free-tier models.

---

## Quick Start

1. **Copy agent files**: Clone this repo (use the `Free-Orchestration` branch), copy `.github/agents/` (FOrchestrator.agent.md, FPlanner.agent.md, FDesigner.agent.md, FCoder.agent.md) into your project.

2. **Configure VS Code**: Install GitHub Copilot extension (v1.206+) and GitHub Copilot Chat. Open VS Code settings and verify `github.copilot.enable` and `github.copilot.chat.enabled` are true.

3. **Enable experimental sub-agents**: Go to GitHub Copilot extension settings, search for "experimental", and enable `chat.customAgentInSubagent.enabled`. (Critical: this allows agents to use optimal models.)

4. **Customize agent files**: Update each `.agent.md` with your repo's constraints, patterns, and APIs. Example: Update FCoder.agent.md with your stack (C#/WPF, Python, etc.), test framework, build command, and offline-first rules.

5. **Test**: Open Copilot Chat in VS Code and ask Orchestrator to break down a feature request. Verify it delegates to Planner → Designer (if UI) → Coder.

---

## The Four Agents

**Orchestrator**: Analyzes requests, decomposes into tasks, delegates to specialists, reconciles outputs. Never writes code. Ensures Planner always goes first and identifies parallelizable work.

**Planner**: Researches codebase, verifies APIs, identifies edge cases, outputs ordered implementation steps. Never writes code. Produces structured plans (summary, steps, edge cases, open questions).

**Designer**: Owns all visual and interaction decisions—layout, accessibility, design system consistency. Never implements. Outputs UX/UI spec with WCAG notes and design token references.

**Coder**: Implements from Planner's plan and Designer's spec, runs tests, reports results. Follows repo constraints (offline-first, MVVM, data integrity). Consult docs for external APIs (assume training data is outdated).

---

## Core Rules

- **Orchestrator never implements** — Clear separation prevents context collapse.
- **Planner produces plans, not code** — Reviewable plans enable recovery; code generation is Coder's job.
- **Designer owns all UI decisions** — Prevents ad-hoc UX; ensures consistency.
- **Coder respects plan & spec** — Ensures accountability; never silently deviate.
- **All handoffs are structured** — Reduces overhead; input/output formats are explicit.
- **Repo constraints are universal** — Offline-first, sync safety, MVVM, no UI regressions apply everywhere.

---

## How It Works

User asks Orchestrator: "Add CSV export with offline queueing." → Orchestrator delegates: Planner researches data patterns and offline queue; Designer specs button placement and states; Coder implements all three in parallel where possible; Orchestrator verifies plan/spec alignment, tests pass, and risks are documented.

---

## Extending the Pattern

The Core 4 are a foundation, not a ceiling. Add custom agents for high-value, repetitive work:

- **Validator**: Check Coder's output against Planner requirements and Designer spec. Use a faster model. Invoke after Coder on data/security/offline-critical features.
- **Tester**: Black-box testing, edge case discovery.
- **SecurityAuditor**: Vulnerability scanning.

**Principle**: Add only agents that reduce Orchestrator load, parallelize work, and avoid redundancy. Target: Core 4 + 1–2 custom agents max.

---

## Lightweight Agents

For simple or high-frequency tasks, create lightweight agents with a single, focused responsibility (e.g., Linter, Formatter, DocGenerator). These agents use minimal instructions and run on fast, free models. They should never block or duplicate the work of the Core 4.

## Free Model Usage

The Free-Orchestration branch is designed to work with free-tier models (e.g., GPT-3.5, Claude Instant). All agent prompts are optimized for compatibility and speed. For best results, ensure your Copilot settings allow fallback to free models.

## Review Agents

Add review agents to automate code review, plan validation, or spec compliance checks. These agents should run after Coder and before merge, using fast models to catch regressions and enforce standards. Example: PRReviewer.agent.md for automated pull request review.

## Contributing

Test the pattern on real tasks in your codebase. Share what works: Which delegation patterns are most effective? Where did agents struggle? Open GitHub issues or PRs with results, improved templates, and repo-specific agent prompts. Include task summary, agents used, what went well, what could improve, and suggested changes.

---

## Branch

This documentation and agent set apply to the `Free-Orchestration` branch. For paid model features, see the main branch.

## PR Checklist

- [ ] All agent files updated as needed (Orchestrator, Planner, Designer, Coder, custom agents)
- [ ] Plans and specs reviewed before implementation
- [ ] Tests run and results included
- [ ] Repo constraints (offline-first, MVVM, etc.) followed
- [ ] Summary of changes and agent handoffs provided

**Last updated**: February 2026  
**Status**: Production-tested, evolving based on community feedback
