# Orchestration

Coordinate specialized AI agents (Orchestrator, Planner, Designer, Coder) to break down complex tasks, parallelize work, and deliver production-ready code with clear accountability.

---

## Quick Start

1. **Copy agent files**: Clone this repo, copy `.github/agents/` (Orchestrator.agent.md, Planner.agent.md, Designer.agent.md, Coder.agent.md) into your project.

2. **Configure VS Code**: Install GitHub Copilot extension (v1.206+) and GitHub Copilot Chat. Open VS Code settings and verify `github.copilot.enable` and `github.copilot.chat.enabled` are true.

3. **Enable experimental sub-agents**: Go to GitHub Copilot extension settings, search for "experimental", and enable `github.copilot.advanced.experimental.subagents`. (Critical: this allows agents to use optimal models.)

4. **Customize agent files**: Update each `.agent.md` with your repo's constraints, patterns, and APIs. Example: Update Coder.agent.md with your stack (C#/WPF, Python, etc.), test framework, build command, and offline-first rules.

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

## Contributing

Test the pattern on real tasks in your codebase. Share what works: Which delegation patterns are most effective? Where did agents struggle? Open GitHub issues or PRs with results, improved templates, and repo-specific agent prompts. Include task summary, agents used, what went well, what could improve, and suggested changes.

---

**Last updated**: February 2026  
**Status**: Production-tested, evolving based on community feedback
