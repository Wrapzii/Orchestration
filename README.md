# Orchestration

Coordinate specialized AI agents to break down complex tasks, parallelize work, and deliver production-ready code with clear accountability.

---

## Quick Start

1. **Copy agent files**: Clone this repo, copy `.github/agents/` into your project.

2. **Configure VS Code**: Install GitHub Copilot extension (v1.206+) and GitHub Copilot Chat. Open VS Code settings and verify `github.copilot.enable` and `github.copilot.chat.enabled` are true.

3. **Enable experimental sub-agents**: Go to GitHub Copilot extension settings, search for "experimental", and enable `github.copilot.advanced.experimental.subagents`.

4. **Customize agent files**: Update each `.agent.md` with your repo's constraints, patterns, and APIs.

5. **Test**: Open Copilot Chat in VS Code and ask Orchestrator to break down a feature request.

---

## Agents

| Agent | Model | Role |
|-------|-------|------|
| **Orchestrator** | Claude Opus 4.6 | Decomposes requests, delegates, coordinates, never implements |
| **Planner** | GPT-5.3-Codex | Research, edge cases, ordered implementation plan (no code) |
| **Designer** | Gemini 3 Pro | Designs **and implements** all UI/UX changes |
| **MasterCoder** | Claude Haiku 4.6 | Classifies tasks, dispatches Coder/FastCoder in parallel |
| **Coder** | GPT-5.3-Codex | Complex implementation, multi-file changes, architecture |
| **FastCoder** | Claude Haiku 4.6 | Simple, single-file tasks (≤5 min); escalates if ambiguous |
| **Reviewer** | Claude Opus 4.6 | End-of-session multi-model review (parallel Opus + Gemini + Codex) |

---

## How It Works

```
User → Orchestrator
         ├── Planner (plan, no code)
         ├── Designer (UI/UX design + implementation, parallel if independent)
         ├── MasterCoder (coding coordinator)
         │     ├── FastCoder (simple tasks, parallel)
         │     └── Coder (complex tasks, parallel)
         └── Reviewer (end-of-session, parallel 3-model review)
```

**Example**: "Add CSV export with offline queueing."
1. Orchestrator → Planner: plan data patterns and offline queue.
2. Orchestrator → MasterCoder: implement per plan.
3. MasterCoder → FastCoder (update config) + Coder (queue logic) in parallel.
4. Orchestrator → Reviewer: review all changes before reporting back.

---

## Core Rules

- **Orchestrator never implements** — prevents context collapse.
- **Designer implements** — owns both design decisions and their code changes.
- **MasterCoder parallelizes** — runs FastCoder and Coder concurrently on independent tasks.
- **Reviewer runs at session end** — three models in parallel, deduplicated findings.
- **All handoffs are structured** — input/output formats are explicit.

---

## Model Configuration

Models are set per-agent in the frontmatter of each `.agent.md` file. To swap a model, edit the `model:` field in the relevant agent file — no code changes needed.

Current assignments:
- Orchestrator/Reviewer: Claude Opus 4.6 (reasoning, coordination)
- MasterCoder/FastCoder: Claude Haiku 4.6 (speed, lightweight dispatch)
- Planner/Coder: GPT-5.3-Codex (deep code understanding)
- Designer: Gemini 3 Pro (visual/UI strength)

---

## Contributing

Test the pattern on real tasks in your codebase. Open GitHub issues or PRs with results, improved templates, and repo-specific agent prompts.

---

**Last updated**: February 2026  
**Status**: Production-tested, evolving based on community feedback
