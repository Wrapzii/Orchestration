# Orchestration: A Multi-Agent Pattern for AI-Driven Development

> **Coordinate specialized AI agents (Orchestrator, Planner, Designer, Coder) to break down complex software tasks, parallelize work intelligently, and deliver higher-quality results faster.**

This repository documents a **battle-tested orchestration pattern** for teams adopting AI agents in their development workflows. Instead of a single all-purpose AI agent, we use a **team of specialists** with clear roles, constraints, and handoff protocols. The result: clearer decision-making, faster iteration, and production-ready code.

---

## 1. Hero Section / Opening Hook

### Why Orchestration Matters

A single all-purpose AI agent context-switches between planning, design, and coding, leading to:
- Unclear accountability and mixed output quality
- Poor parallelization (design waits for plan; code waits for design)
- Weak error recovery when any aspect goes wrong

**Orchestration flips this**: A lightweight *Orchestrator* delegates to specialist agents (Planner, Designer, Coder), each with focused expertise and clear handoff rules.

### The Win

- **30–50% faster complex task completion** (planning & design run in parallel)
- **Higher code quality** (dedicated Coder enforces repo conventions)
- **Clearer decision trails** (each role produces structured output)
- **Easier debugging** (accountability is clear)
- **Offline-first workflows** (repo constraints enforced across all agents)

---

## 2. Why This Pattern?

### The Problem We Solved

Single-agent approaches fail because agents context-switch between planning, design, and coding, losing quality and parallelization. Multi-agent approaches without coordination create output conflicts and broken repo constraints.

### Our Solution: Specialist Agents + Smart Orchestration

Each agent focuses on **one dimension**, respects **repo constraints**, hands off via **structured protocol**:
- **Orchestrator**: Understands request, breaks into tasks, delegates wisely, coordinates results. Never writes code.
- **Planner**: Researches codebase, confirms APIs, identifies edge cases, produces ordered steps. No code.
- **Designer**: Owns UX/UI specs, accessibility, consistency, interaction design. No implementation.
- **Coder**: Implements from plan & spec, tests, reports results. Follows repo conventions.

---

## 3. The Four Agents

### Orchestrator

**Role**: The conductor. Analyzes requests, breaks into tasks, delegates to specialists, reconciles outputs.

- **Core task**: Parse request → decompose → delegate → coordinate → report
- **Authority**: Decides *what* needs doing; never *how*
- **Cannot**: Write code, design UI, or implement directly
- **Key rule**: End each subagent prompt with a clarifying question
- **Parallelization**: Send independent tasks to multiple agents simultaneously

**Example**: "Fix infinite loop in menu AND add voice input" → Planner (bug triage) + Designer+Coder (voice feature) in parallel.

---

### Planner

**Role**: The strategist. Researches, verifies assumptions, identifies edge cases, produces plans (no code).

- **Core task**: Research codebase → verify APIs → list edge cases → output ordered steps
- **Authority**: *What* must change, not how to code it
- **Cannot**: Write code or suggest function signatures
- **Key discipline**: Always verify external APIs; never handwave

**Output format** (always):
1. **Summary**: One-paragraph overview
2. **Implementation steps**: Numbered, describing *what* not *how*
3. **Edge cases**: Bullet list of constraints and failure modes
4. **Open questions**: Only if blocking; otherwise state safest assumption

---

### Designer

**Role**: The UX/UI expert. Owns visual decisions, accessibility, consistency, interaction design.

- **Core task**: Produce UX/UI spec (layout, colors, states, accessibility, assets)
- **Authority**: All visual and interaction decisions, prioritizing user experience
- **Cannot**: Implement code
- **Key discipline**: Stay within existing design system; don't invent new patterns

**Output includes**:
- Layout decisions and interaction states
- WCAG accessibility notes (contrast, ARIA, keyboard nav)
- Design system tokens referenced (colors, spacing, icons)
- Connection to existing app patterns

---

### Coder

**Role**: The implementer. Writes code, follows repo conventions, verifies with tests, reports results.

- **Core task**: Implement from Planner's plan + Designer's spec; test; report
- **Authority**: How code is structured; respects plan & spec
- **Cannot**: Redesign the plan or UI; ignore repo constraints
- **Key discipline**: Consult docs for external APIs (assume training data is outdated)

**Mandatory principles**:
1. Flat, explicit code over deep hierarchies
2. Linear, readable control flow
3. Descriptive names; comments only for invariants/assumptions
4. Structured logging at key boundaries
5. Deterministic, testable behavior
6. Write modules so they can be rewritten safely

**Repo constraints** (non-negotiable):
- Offline-first workflows
- Sync is data-integrity critical (never drop local JSON data)
- MVVM patterns (business logic in ViewModels/Services)
- No UI regressions (selection resets, object replacements)

**Delivery checklist**:
- [ ] What files changed?
- [ ] Did build and tests pass?
- [ ] What are the risks?
- [ ] How to validate in practice?

---

## 4. How to Use This Pattern

### Step-by-Step Workflow

**Phase 1: Clarify scope** (Orchestrator decides if additional questions are needed)

**Phase 2: Delegate to Planner** (Always first step)
- Provide: user request, repo context, constraints
- Receive: structured plan with summary, steps, edge cases, open questions

**Phase 3: Delegate to Designer** (If UI/UX is involved)
- Provide: Planner output
- Receive: UX/UI spec with layout, states, accessibility, design tokens

**Phase 4: Delegate to Coder** (With plan and spec)
- Provide: Planner plan + Designer spec
- Receive: implemented feature, test results, risks, validation steps

**Phase 5: Orchestrator Reconciles & Reports**
- Verify: plan/spec alignment, tests passed, risks documented
- Report back: summary, files changed, validation steps

### Decision Tree: When to Delegate What

```
Request arrives
├─ Need clarification? Ask minimal questions
├─ Send to PLANNER (always first)
├─ UI/UX change? → Send Designer Planner output
├─ Send to CODER (with plan + spec)
└─ Reconcile & report
```

---

## 5. Key Rules & Principles

### Golden Rules (Non-Negotiable)

1. **Orchestrator never implements** — Clear separation; prevents context collapse
2. **Planner produces plans, not code** — Plans are reviewable; code is not
3. **Designer owns all UI decisions** — Prevents ad-hoc UX; ensures consistency
4. **Coder respects the plan & spec** — Ensures accountability; enables recovery
5. **All handoffs are structured** — Reduces overhead; avoids miscommunication
6. **Repo constraints are universal** — Offline-first and sync safety apply everywhere

### The Handoff Protocol

Every agent passes **structured inputs/outputs**:

| Stage | Input | Output |
|-------|-------|--------|
| Orchestrator → Planner | Request + repo context + constraints | Summary \| Steps \| Edge cases \| Open questions |
| Planner → Designer/Coder | Plan document | Usable input for next agent |
| Designer → Coder | UX/UI spec (layout, states, accessibility, tokens) | Template for implementation |
| Coder → Orchestrator | Implementation + tests + build results | Files changed, risks, validation steps |

### Red Flags: Patterns to Avoid

- 🚨 Orchestrator writes code
- 🚨 Planner provides code snippets
- 🚨 Designer specifies implementation details ("use Button control with x:Name...")
- 🚨 Coder ignores the plan midway without discussion
- 🚨 Feature works online but breaks offline; nobody notices until production

---

## 6. Default Orchestration Workflow

Use this workflow for 80% of requests:

```
┌──────────────────────────────────────────────────────────────┐
│ REQUEST ARRIVES                                              │
│ Orchestrator reads & decides: clarify scope needed?          │
└──────────────────────────────────────────────────────────────┘
                               │
                    ┌──YES─────┤─────NO───┐
                    ▼                      ▼
          [ASK CLARIFYING            [PROCEED TO
           QUESTIONS]                 PLANNER]
                    │                      │
                    └──────────────────────┘
                               │
                    ┌──────────▼───────────┐
                    │ SEND TO PLANNER      │
                    │ (Always first step)  │
                    └──────────┬───────────┘
                               │
                          [PLAN + EDGE CASES]
                               │
                    ┌──────────▼──────────────┐
                    │ Does request involve   │
                    │ UI/UX changes?         │
                    └──────────┬──────────────┘
                               │
                    ┌──YES─────┤──────NO──┐
                    │          │          │
                    ▼          │          ▼
              [SEND TO DESIGNER]    [SEND TO CODER]
                    │          │          │
                [UX SPEC]      │          │
                    │          │          │
                    └──────────┤──────────┘
                               │
                    ┌──────────▼────────────┐
                    │ SEND TO CODER         │
                    │ (with plan + spec)    │
                    └──────────┬────────────┘
                               │
                    [IMPLEMENTATION + TESTS]
                               │
                    ┌──────────▼────────────────┐
                    │ ORCHESTRATOR VERIFIES:    │
                    │ - Coder ran tests?       │
                    │ - Build passed?          │
                    │ - Risks documented?      │
                    └──────────┬────────────────┘
                               │
                    ┌──────────▼────────────────┐
                    │ REPORT TO USER           │
                    │ Summary + what changed + │
                    │ how to validate          │
                    └──────────────────────────┘
```

---

## 7. Delegation Patterns & Example

### Pattern: Feature with Bug Triage (Parallel Execution)

**User request**: "Fix infinite loop in menu AND add voice input to chat."

**Orchestrator breaks it into**:
- Task 1 (Planner): Bug triage + reproduction plan
- Task 2 (Designer): Voice input UX spec
- Task 3 (Coder): Implement voice feature

**Parallelization**: Run Planner without waiting for Designer; once Planner plan exists, send Designer the output. Coder waits for Plan+Spec. Results reconcile quicker than serial delegation.

### Anti-Pattern: Serial Bottleneck

❌ **Bad**: Orchestrator → Planner (wait) → Coder (wait) = 3x longer

✅ **Good**: Orchestrator → Planner + Designer (parallel tasks) → Coder (when ready) = faster

**Key insight**: Identify independent work items; delegate simultaneously.

---

## 8. Best Practices & Pitfalls

### Best Practices

1. **Parallelize independent work** — "Add dark mode + fix button styling" → Designer + Coder simultaneously
2. **Planner always comes first** — Even simple requests benefit from upfront research (are there existing patterns? edge cases?)
3. **Designer enforces consistency** — Use existing design system tokens; don't invent new patterns
4. **Coder validates the plan** — If blockers arise, loop in Orchestrator; don't silently deviate
5. **Document assumptions** — Planner and Designer state assumptions explicitly (e.g., "assumes X is supported; need to verify")

### Common Pitfalls

| Pitfall | Bad | Good |
|---------|-----|------|
| Orchestrator micromanages | "Use struct for this, name it ThemeState..." | "Implement per plan & spec; respect repo conventions" |
| Coder implements before Planner researches | Reimplements existing features | Extend existing patterns; no rework |
| Designer ignores existing patterns | "New color palette + button style for unique branding" | Use existing tokens; WCAG compliance comes first |
| Repo constraints ignored | Feature works online but breaks offline | Verify offline-first in tests; document constraints |

---

## 9. Troubleshooting & FAQ

**Q: An agent outputs something unclear. What do I do?**
A: Loop back to Orchestrator for clarification. Orchestrator asks focused follow-up questions, then resends to agent.

**Q: Designer and Coder disagree on implementation.**
A: Escalate to Orchestrator. Orchestrator mediates: can we meet both requirements? If not, Designer leads (UX takes priority over code convenience).

**Q: Planner's assumption turns out wrong mid-implementation.**
A: Coder raises it; Orchestrator loops back to Planner for plan revision. Avoid surprise deviations.

**Q: How do I know when the pattern is working?**
A: Watch for: parallel work completing faster, clear decision trails, no rework surprises, repo constraints enforced, easy debugging when issues arise.

**Q: When should I skip Orchestration?**
A: Trivial requests ("fix typo") can skip directly to Coder. For anything complex or multi-domain, use the full pattern.

---

## 10. Getting the Agents Running

### Prerequisites

- **Coder mode**: File reading, editing, testing, docs access (Context7)
- **Designer mode**: Design system access, WCAG guidelines, app patterns
- **Planner mode**: Research tools, documentation, codebase search
- **Orchestrator mode**: Ability to call subagents and aggregate outputs

### Quick Start

1. Clone this repo; reference agent definitions in `.github/agents/`
2. Use agent definitions as system prompts for your chosen LLM (Claude, GPT, Gemini, etc.)
3. Run the pattern on a simple task to test (e.g., "Add export button to data table with offline support")
4. Observe: Does Planner find existing patterns? Does Coder respect constraints? Did Designer UX align with code?
5. Iterate: Refine handoff templates based on real experience

### Deployment Checklist

- [ ] Read the agent definitions in `.github/agents/`
- [ ] Test with a small, real task
- [ ] Verify Planner researches existing patterns correctly
- [ ] Verify Coder respects offline-first and MVVM conventions
- [ ] Review handoff outputs for clarity and structure
- [ ] Document any repo-specific constraints in Orchestrator prompt
- [ ] Set up feedback loop for continuous improvement

---

## 11. Extending the Pattern: Custom Agents

The Core 4 (Orchestrator, Planner, Designer, Coder) are a **foundation, not a ceiling**. You can add specialized agents for **repetitive, high-value work**:

- **Validator**: Check implementation against requirements (fast iteration)
- **Tester**: Black-box testing, edge case discovery
- **SecurityAuditor**: Vulnerability scanning, compliance checking
- **DevOps**: Deployment, CI/CD configuration
- **DocumentationAgent**: Generate API docs, changelogs

### When to Add a Custom Agent

Ask these questions:

1. **Is this repetitive?** ("Every feature needs security review" → Yes, add SecurityAuditor)
2. **Does it reduce Orchestrator load?** ("Validator saves Orchestrator from manually checking" → Yes)
3. **Does it parallelize or add overhead?** (Parallel → Good; adds serial delay → Reconsider)
4. **Would Core 4 already be doing this?** ("Designer already handles accessibility" → Probably redundant)

### Example: Validator Agent

**Purpose**: Check Coder's output against Planner requirements and Designer spec (no implementation changes).

**Input**: Requirement list + spec + implementation

**Output**: Validation report (✓ passes / ✗ failures with severity)

**Model choice**: Faster model (Haiku, GPT-3.5) — checking concrete output, not generating.

**When to invoke**:
```
IF Coder finishes + feature involves data/security/offline sync
THEN invoke Validator after Coder
```

**Example workflow**:
```
Coder: "Voice input feature implemented"
├─ Validator: "✓ Offline blob sync verified ✓ ARIA labels present 
                 ✗ BLOCKER: Space/Enter keyboard binding missing"
└─ Coder fixes → Validator re-checks → PASS
```

### Avoiding Agent Proliferation

**The risk**: Add too many agents; coordination overhead explodes.

**The principle**: Add custom agents **only for high-value, repetitive work**. Sweet spot is **2–4 custom agents max** (Core 4 + 1–2 custom).

| Situation | Action |
|-----------|--------|
| Core 4 handles it fine | Don't add agent |
| Work is not repetitive | Don't add agent |
| Would create a bottleneck | Don't add agent |
| High-value + repetitive + parallelizes | Good fit |

---

## 12. Contributing & Feedback

We're eager to learn from your experience. If you adopt Orchestration, **share what works**:

- Which delegation patterns are most effective for your codebase?
- Where did agents struggle? What guardrails would help?
- Any new roles or edge cases discovered?

**How to contribute**:
1. Test the pattern on a real task.
2. Open an issue with results: What worked? What failed?
3. Submit PRs with improved templates, new agent definitions, or documentation updates.
4. Share your repo-specific agent prompts if they're shareable.

**Feedback template**:
- Task summary
- Agents used (Orchestrator → Planner → Designer/Coder)
- What went well (parallelization? clear handoffs?)
- What could improve (unclear outputs? repo constraint enforcement?)
- Suggested changes

---

## Recommended Reading

- [The Four Agent Definitions](.github/agents/)
  - `Orchestrator.agent.md`
  - `Planner.agent.md`
  - `Designer.agent.md`
  - `Coder.agent.md`

---

**Last updated**: February 2026  
**Status**: Production-tested, evolving based on community feedback
