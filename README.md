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

**Role**: The conductor. Understands requests, breaks them into tasks, delegates to the right specialists, coordinates outputs, reports back.

| Aspect | Details |
|--------|---------|
| **Core task** | Analyze user request → decompose → delegate (Planner/Designer/Coder) → reconcile → report |
| **Authority** | Decides *what* needs to be done; never decides *how* |
| **CANNOT do** | Write code, design UI, or implement anything directly |
| **Key rule** | Always end each subagent prompt with a question (e.g., "What do you think?") |
| **Parallelization** | Can send independent tasks to multiple agents simultaneously |

**Example workflow** (Orchestrator view):
```
User: "Fix the infinite loop error in the side menu AND add voice input to chat."

Orchestrator thinks:
1. These are independent tasks → parallelize
2. "Fix bug" → Planner (triage + reproduction plan)
3. "Add voice feature" → Designer (UX spec) + Coder (implementation)
4. Wait for all outputs
5. Reconcile & report back
```

---

### Planner

**Role**: The strategist. Researches, verifies assumptions, identifies edge cases, produces a detailed plan (no code).

| Aspect | Details |
|--------|---------|
| **Core task** | Research codebase → verify external APIs → list edge cases → output ordered steps |
| **Authority** | Decides *what must change*, not how to code it |
| **CANNOT do** | Write code or suggest specific function signatures |
| **Key discipline** | Always verify external APIs and docs; never handwave |
| **Repo knowledge** | Search patterns, find existing conventions, extend instead of invent |

**Output format (always)**:
1. **Summary**: One paragraph overview.
2. **Implementation steps**: Numbered, in order, describing *what* not *how*.
3. **Edge cases**: Bullet list of failure modes and constraints to respect.
4. **Open questions**: Only if actually blocking; otherwise make safest assumption and state it.

**Example plan output**:
```
SUMMARY
Add voice input to chat. User speaks → audio captured on device → 
sent to backend for transcription (or local if offline). Text 
returned to chat input field.

IMPLEMENTATION STEPS
1. Add AudioRecorder component to chat interface.
2. Add VoiceInputService (handles device audio, stores locally, 
   forwards to sync queue).
3. Extend ChatMessage model to support voice_input_url field.
4. Add transcription handler (listen for voice blobs in sync queue).
5. Wire voice output → text input field.

EDGE CASES
- Offline: voice blob queued locally; transcription happens on 
  reconnect (Planner confirms sync queue supports this).
- Permission denied: graceful fallback to text input.
- Large audio files: chunking strategy (check existing media 
  constraints).
- Network timeout during transcription: preserve blob, retry 
  (verify retry policy in repo).

OPEN QUESTIONS
- Is transcription local (device) or remote (backend)?
  *Assumption: backend (Coder can verify with Designer).*
```

---

### Designer

**Role**: The expert in UX/UI. Owns visual decisions, accessibility, consistency, and interaction design.

| Aspect | Details |
|--------|---------|
| **Core task** | Produce UX/UI spec: layout, colors, states, accessibility, assets |
| **Authority** | All visual and interaction decisions; prioritizes *user experience* over tech convenience |
| **CANNOT do** | Implement code (though may sketch visual structure) |
| **Key discipline** | Stay within existing design system unless asked to redesign |
| **Constraints** | Respect accessibility; ensure contrast; reuse existing tokens; keep UX consistent |

**Output includes**:
- Layout decisions (wireframe or clear description).
- Color/contrast notes (WCAG compliance, accessibility).
- Interaction states (focus, hover, disabled, error, success).
- Any tokens/assets needed from design system.
- Connection to existing app patterns.

**Example design output**:
```
VOICE INPUT UX SPEC

Layout
- Add microphone button to right side of chat input field 
  (near send button).
- Align with existing button sizing and spacing.

States
- IDLE: Gray icon, 45% opacity, pointer cursor.
- HOVER: Icon brightens, tooltip "Hold to record" appears.
- RECORDING: Icon turns red; animated pulsing border; timer 
  shows elapsed seconds.
- PROCESSING: Icon grayed; spinner overlay; disabled state.
- SUCCESS: Icon briefly flashes green; text appears in input.
- ERROR: Icon turns red; inline error message "Mic not available".

Accessibility
- ARIA role: button.
- ARIA label: "Record voice message".
- Keyboard: Tab to focus; Space/Enter to toggle recording.
- Screen reader: Announce recording status ("Recording... 10 seconds elapsed").
- Color contrast: 7:1 ratio (red to white); 5.5:1 for gray idle state.

Design system tokens
- Colors: Use $color-primary (recording state), $color-error (error state).
- Spacing: Use $spacing-md for button margin.
- Icons: Use existing microphone icon from icon library (verify 
  with Coder).
```

---

### Coder

**Role**: The implementer. Writes code, follows repo conventions, verifies with tests, reports results.

| Aspect | Details |
|--------|---------|
| **Core task** | Implement based on Planner's plan + Designer's spec; test; report what changed |
| **Authority** | How code is written (structure, naming, testing); respects plan & spec |
| **CANNOT do** | Redesign the plan or UI on the fly; ignore repo constraints |
| **Key discipline** | Always consult docs (Context7) for external APIs; assume training data is outdated |
| **Mandatory principles** | Offline-first; sync integrity; MVVM patterns; minimal regressions; deterministic behavior |

**Mandatory coding principles**:
1. **Structure**: Consistent, predictable layout; group by feature; keep shared utilities minimal.
2. **Architecture**: Flat, explicit code over deep hierarchies; avoid unnecessary indirection.
3. **Control flow**: Linear, readable; avoid deeply nested logic; pass state explicitly.
4. **Naming/comments**: Descriptive names; comment only for invariants/assumptions/external requirements.
5. **Logging/errors**: Emit structured logs at key boundaries; errors must be explicit and actionable.
6. **Regenerability**: Write so modules can be rewritten safely; avoid spooky action at a distance.
7. **Platform conventions**: Use .NET/WPF patterns directly; don't over-abstract.
8. **Modifications**: Follow existing repo patterns; prefer full-file rewrites when clarity improves (unless asked for micro-edits).
9. **Quality**: Deterministic, testable behavior; tests verify observable outcomes.

**Repo constraints (non-negotiable)**:
- Offline-first core workflows.
- Sync is data-integrity critical: never drop local JSON data.
- MVVM conventions: business logic in ViewModels/Services; UI bindings stay stable.
- Don't introduce UI regressions (selection resets, object replacement pitfalls).

**Delivery checklist**:
- [ ] What files changed?
- [ ] Did build and tests pass?
- [ ] What are the risks?
- [ ] How to validate in practice?

---

## 4. How to Use This Pattern

### Step-by-Step Workflow

#### Phase 1: Orchestrator Clarifies Scope

```
User input: "Add dark mode to the app."

Orchestrator decides:
- Does the request need clarification?
  If NO → skip to Phase 2.
  If YES → ask minimal clarifying questions only.
  
Example minimal question:
"Dark mode across all screens or just specific areas? 
And should preferences persist between sessions?"
```

#### Phase 2: Delegate to Planner (Always First)

**Send to Planner**:
```
You are the Planner. Create a plan for: Add dark mode to the app.

Context:
- The app is WPF/MVVM with existing theme system.
- Offline-first constraints apply.

Constraints:
- Match existing MVVM patterns.
- Identify edge cases (sync, theme persistence).
- List impacted components.

Output format: Summary | Implementation steps | Edge cases | Open questions.

What do you think? Should we research further or proceed with the plan?
```

**Planner produces**: A structured plan (no code).

#### Phase 3: Delegate to Designer (If UI/UX Is Involved)

**Send to Designer**:
```
You are the Designer. Produce a UX/UI spec for: 
Add dark mode theme switching to the app (see attached Planner output).

Requirements:
- Stay within existing design system.
- Ensure WCAG accessibility (7:1 contrast for dark mode).
- Show interaction states: toggle, applying, restoring saved preference.
- Identify any new tokens needed (or reuse existing).

What do you think? Are there accessibility or usability concerns?
```

**Designer produces**: A detailed UX/UI spec (no code).

#### Phase 4: Delegate to Coder

**Send to Coder**:
```
You are the Coder. Implement dark mode based on:
- Planner's plan (see attached)
- Designer's UX/UI spec (see attached)

Requirements:
- Follow existing MVVM conventions.
- Persist theme preference (respect sync integrity).
- Run existing tests; add new tests if needed.
- Report: files changed, build results, validation steps.

What do you think? Any technical blockers or risks?
```

**Coder produces**: Implemented feature, verified with tests, risks documented.

#### Phase 5: Orchestrator Reconciles & Reports

**Orchestrator review**:
- Do plan and spec align?
- Are risks documented?
- Did Coder run all necessary tests?
- Anything contradictory or missing?

**Report back to user**:
```
Dark mode implementation complete.

Summary: Added theme switching (light/dark) to all screens. 
User preference persists via local JSON (sync-safe).

Files changed:
- ThemeService (MVVM service layer)
- ThemePanel (UI for toggle)
- App.xaml (theme resources)
- Tests: Added 5 new tests for persistence, contrast.

Build & tests: ✓ Passed

What to validate:
1. Toggle theme button in settings.
2. Close app; reopen → preference restored.
3. Check contrast ratios with accessibility checker.

Risks none noted.
```

---

### Decision Tree: When to Delegate What

```
User request arrives
│
├─ Clarify scope? 
│  └─ YES → Ask minimal questions → Planner
│  └─ NO → Go to Planner
│
├─ PLANNER: Create plan + edge cases
│  └─ Produced: Ordered steps, edge cases, assumptions
│
├─ Designer involvement?
│  ├─ YES (UI/UX change) → Send Designer the Planner output
│  │  └─ Produced: UX/UI spec, accessibility notes
│  └─ NO (backend/infra only) → Skip to Coder
│
├─ CODER: Implement from plan + spec
│  └─ Produced: Code, tests, build results, risks
│
└─ ORCHESTRATOR: Reconcile + report
   └─ Delivered: Final summary to user
```

---

## 5. Key Rules & Principles

### Golden Rules (Non-Negotiable)

| Rule | Why | Violating This Looks Like |
|------|-----|--------------------------|
| **Orchestrator never implements** | Clear separation of concerns; prevents context collapse | Orchestrator writes code directly; skips delegation |
| **Planner produces plans, not code** | Plans are reviewable; code is not | Planner suggests "add this method X" or provides snippets |
| **Designer owns all UI decisions** | Prevents ad-hoc UX; ensures consistency | Coder changes button colors; Designer finds out later |
| **Coder respects the plan & spec** | Ensures accountability; enables recovery | Coder redesigns logic mid-implementation; ignores Designer spec |
| **All handoffs are structured** | Reduces coordination overhead; avoids miscommunication | Agent outputs are vague; next agent can't act on them |
| **Repo constraints are universal** | Offline-first and sync safety apply to all work | Coder adds feature that breaks offline workflows |

### The Handoff Protocol

Every agent **must** receive and produce **structured inputs/outputs**:

**Planner input** (from Orchestrator):
```
Task: [user request]
Repo context: [relevant files/patterns]
Constraints: [repo rules]
Format: Summary | Steps | Edge cases | Open questions
```

**Planner output** → **Designer/Coder input**:
```
Summary: [one paragraph]
Steps: [numbered, describing WHAT not HOW]
Edge cases: [bullet list]
Open questions: [only if blocking]
```

**Designer output** → **Coder input**:
```
Layout: [clear description or wireframe]
States: [interaction design]
Accessibility: [WCAG compliance, ARIA, keyboard navigation]
Design tokens: [colors, spacing, icons from existing system]
```

**Coder output** → **Orchestrator**:
```
Files changed: [list with brief description]
Tests: [what changed, pass/fail]
Build: [command run, output, pass/fail]
Risks: [anything worth noting]
How to validate: [steps user can follow]
```

### Red Flags: Patterns to Avoid

🚨 **Red Flag #1: Orchestrator writes code**
```
WRONG: "Here's the dark mode implementation... [500 lines of XAML]"
RIGHT: "Let me ask Coder to implement this based on a plan..."
```

🚨 **Red Flag #2: Planner provides code snippets**
```
WRONG: "Here's the plan: Create a class ThemeService with method ApplyTheme()"
RIGHT: "Here's the plan: Add a service layer to manage theme state and persistence."
```

🚨 **Red Flag #3: Designer implementation details invade spec**
```
WRONG: "Use Button control with x:Name='ThemeButton' set Opacity to 0.45"
RIGHT: "Show idle state with 45% opacity; use existing button component."
```

🚨 **Red Flag #4: Coder ignores the plan**
```
WRONG: Coder adds 10 new classes; changes Planner's architecture mid-way; no discussion
RIGHT: "I need to deviate from the plan here because [constraint]. Running by Orchestrator."
```

🚨 **Red Flag #5: Repo constraints ignored**
```
WRONG: Feature works online but breaks offline; nobody notices until production
RIGHT: Coder verifies offline-first behavior in tests; updates ProjectState.md
```

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

## 7. Delegation Patterns & Examples

### Pattern A: Bug Fix + Feature

**User request**:
> "Fix the infinite loop in the left sidebar AND add voice input to chat."

**Orchestrator analysis**:
- Two independent tasks → parallelize.
- Bug fix: involves triage → Planner.
- Voice feature: involves UX + code → Designer + Coder.

**Delegation**:

| Agent | Task | Input | Output |
|-------|------|-------|--------|
| Planner | Bug triage | Error description + repo context | Reproduction steps, likely cause, fix strategy |
| Designer | Voice UX | Feature requirements | Interaction spec, accessibility, button state |
| Coder | Implement voice | Planner plan + Designer spec | Working feature, tests, build results |

**Reconciliation**:
- Check: voice feature respects offline-first (ask Coder to verify).
- Check: no new UI regressions from bug fix.
- Report: both done, no blockers.

---

### Pattern B: Complex Refactor

**User request**:
> "Refactor the sync engine to improve performance and reduce memory usage. Current approach is too slow."

**Orchestrator analysis**:
- Complex, potentially risky → Planner leads.
- No UI change → no Designer needed.
- Implementation can follow plan → Coder.

**Delegation**:

1. **Planner**: Research sync engine → identify bottlenecks → propose refactor strategy (no code).
2. **Coder**: Implement from Planner's plan → benchmark → verify sync integrity → test offline scenarios.
3. **Orchestrator**: Reconcile → ensure no regressions → report performance gains.

**Key Planner work**:
- Profile existing implementation.
- Identify data structures causing slowdown.
- Propose alternative approaches (pros/cons).
- Call out risks (sync integrity, offline behavior).

**Key Coder work**:
- Choose best approach from Planner options.
- Benchmark: before/after metrics.
- Verify sync queue still works offline.
- Add performance tests.

---

### Pattern C: Multi-Screen Feature

**User request**:
> "Add a settings panel to manage app preferences (theme, notifications, data syncing)."

**Orchestrator analysis**:
- Multiple screens, significant UX → both Designer and Coder.
- Complex preferences logic → Planner.

**Delegation**:

1. **Planner**: Research existing settings patterns → identify new pref storage → plan screen flow → edge cases (sync conflicts? offline behavior?).
2. **Designer**: Produce settings UX spec → layout for all screens → accessibility → interaction states.
3. **Coder**: Wire settings screens → store prefs → sync → tests.

**What could go wrong**:
- Designer doesn't check existing app theme system → Coder must rework colors.
- Planner forgets offline-first → Coder discovers prefs don't sync when offline.
- Coder changes settings structure mid-way → UI breaks.

**How Orchestration prevents this**:
- Planner researches upfront → uses existing patterns.
- Designer sees Planner output → aligns with app constraints.
- Coder follows Designer spec → no surprises.

---

### Anti-Pattern: Serial Handoff Bottleneck

❌ **Bad**:
```
Orchestrator → Planner | (wait for output)
              Planner done → Designer | (wait for output)
                           Designer done → Coder | (wait for output)
                                         Coder done → Report
```
**Result**: 3x longer delivery time due to sequential waiting.

✅ **Good**:
```
Orchestrator → Planner ────────────┐
            → Designer ────────────┼─ (all parallel, wait for all)
            → Coder ───────────────┘
                      (if independent)
           
OR if Designer needs Planner first:
Orchestrator → Planner ──┐
              (output)───→ Coder + Designer (parallel)
                      (combined output) → Report
```

---

## 8. Best Practices & Pitfalls

### Best Practice #1: Always Parallelize When Possible

**Good breakdown**:
- "Add dark mode + fix button styling" → Designer + Coder can work simultaneously.
- "Plan refactor + design UI" → Planner + Designer parallel (if UI is independent of refactor).

**Bad breakdown**:
- Serial delegation when tasks are independent.
- Waiting for Planner when Coder could research existing code.

**Implementation**:
```
# Parallel subagents in Orchestrator
Send Planner task #1
Send Designer task #1  ← simultaneously
Send Coder task... ← only if needed Plan/Spec first
Wait for all
Reconcile
```

---

### Best Practice #2: Planner Always Comes First

Even if the task seems simple, **Planner should research**:
- What existing patterns exist?
- Are there edge cases I missed?
- Does this break offline-first?

```
❌ BAD: User asks "Add a button." 
   Orchestrator → Coder: "Add a button."
   Coder: "Where? How styled?"
   
✅ GOOD: User asks "Add a button."
   Orchestrator → Planner: "Where should this button go? 
   What does it do? What are edge cases?"
   Planner: "Add button to settings panel, respects 
   existing button styling, no sync impact..."
   Orchestrator → Coder: "Implement per Planner spec."
```

---

### Best Practice #3: Designer Ensures Consistency

Designer's job is **not** to invent new patterns—it's to **enforce existing ones**.

```
✅ "Use existing button component from design system."
❌ "Create a new custom button."

✅ "Reuse existing color token $color-primary."
❌ "Add a new shade of blue."

✅ "Follow the interaction pattern from [existing screen]."
❌ "Invent a new interaction pattern."
```

---

### Best Practice #4: Coder Validates the Plan

Even though Planner did the research, Coder brings **implementation reality**. If Coder discovers a blocker:

```
Coder: "Planner's plan assumes feature X exists, 
        but I found that X was removed in v2. 
        I need to deviate here. Loop in Orchestrator?"
        
Orchestrator: "Good catch. We have two options: [A] revert to 
        using X (but this breaks something else), or [B] new 
        approach. What does Planner think?"
```

---

### Best Practice #5: Document Assumptions

Both Planner and Designer should state assumptions explicitly:

```
PLANNER OUTPUT:
"Edge case: Assumes sync queue supports large blobs. 
 How to verify: grep repo for blob size limits."

DESIGNER OUTPUT:
"ARIA labels assume English locale. For i18n support, 
 labels should be strings from translation file."

CODER OUTPUT:
"This implementation assumes ThemeService already 
 persists to JSON (I verified it does). If that changes, 
 update here: [link to code]."
```

---

### Pitfall #1: Orchestrator micromanages

❌ **Bad**:
```
Orchestrator to Coder: "Use a struct for this, not a class. 
Name it ThemeState. Put persistence logic in Apply() method. 
Store in %appdata%\MyApp\themes.json. Use XmlSerializer, not JSON."
```

✅ **Good**:
```
Orchestrator to Coder: "Implement dark mode per Planner's 
plan and Designer's spec. Respect repo conventions for storage 
and MVVM structure. What do you think?"
```

---

### Pitfall #2: Coder implements before Planner researches

❌ **Bad**:
```
Orchestrator → Coder: "Add pagination to the feed."
Coder writes code, then Planner discovers: "Wait, there's 
already pagination logic in ServiceA.cs that should be reused, 
not reimplemented."
Result: Rework.
```

✅ **Good**:
```
Orchestrator → Planner: "Add pagination to feed."
Planner: "Found existing pagination in ServiceA. We should 
extend it."
Orchestrator → Coder: "Coder, extend existing pagination per 
Planner output."
Result: Clean reuse, no rework.
```

---

### Pitfall #3: Designer doesn't respect existing patterns

❌ **Bad**:
```
Designer: "Let's use a completely new color palette and 
button style for the new dark mode. Unique branding!"
Coder: "But this breaks WCAG contrast. And users expect 
consistency with other buttons in the app."
Result: Redesign needed.
```

✅ **Good**:
```
Designer: "Dark mode uses existing theme tokens, recolored 
for dark background. Button styling remains consistent. 
Verified WCAG 7:1 contrast."
Coder: "Got it, straightforward theming."
Result: Clean implementation.
```

---

### Pitfall #4: Ignoring repo constraints

❌ **Bad**:
```
Coder: "I added a real-time cloud sync library for instant 
collaboration features."
Orchestrator: "Wait, this breaks offline-first workflows. 
Rework needed."
```

✅ **Good**:
```
Coder: "I added sync support for collaboration, but it respects 
offline-first: changes queue locally, sync on reconnect."
Orchestrator: "Verified. Good."
```

---

## 9. Troubleshooting & FAQ

### Q: An agent outputs something unclear. What do I do?

**A**: Loop back to Orchestrator. Orchestrator should ask clarifying questions before proceeding.

```
Orchestrator: "Your plan shows Step 3 as 'Handle edge case.' 
   What specifically is the edge case, and how should it be 
   handled?"
   
Agent: "Edge case: User preference conflicts with server state. 
   Solution: Local preference wins; server will sync on next 
   reconnect."
   
Orchestrator: "Got it. Thanks."
```

---

### Q: Designer and Coder disagree on the implementation.

**A**: Escalate to Orchestrator.

```
Designer: "The button must have rounded corners and shadow."
Coder: "WCAG accessibility requires higher contrast and no 
shadow effect; shadow reduces perceived button size."

Orchestrator: "Designer, can we meet these accessibility 
requirements? Should we adjust the design?"

Designer: "Yes. Let's use subtle rounded corners without 
shadow, and increase border contrast."

Coder: "Implementing now."
```

---

### Q: Planner's assumption turns out to be wrong mid-implementation.

**A**: Coder raises it; Orchestrator loops back to Planner.

```
Coder: "Planner assumed feature X exists, but it was removed 
in v2. Should I implement a workaround?"

Orchestrator: "Planner, can you revise the plan given this 
change?"

Planner: "Revised output: Two options for workaround. 
Option A is safer; Option B is faster. Coder, your call."
```

---

### Q: How do I know when the pattern is working?

**A**: Watch for these signs:

| Sign | Meaning |
|------|---------|
| **Parallel work completes faster** | Agents delegated independently. Good. |
| **Clear decision trails** | Each agent output is documented. Reviews are easier. |
| **No surprises mid-implementation** | Planner researched, Designer specified, Coder followed. No rework. |
| **Repo constraints respected** | Offline-first, sync safety, MVVM patterns enforced. |
| **Easy to debug** | When something goes wrong, you know which agent got it wrong. |

---

### Q: When should I **not** use Orchestration?

**A**: Use simpler patterns for:
- **Trivial requests**: "Fix a typo" → Coder directly (skip Orchestrator/Planner/Designer overhead).
- **Single-domain tasks**: "Optimize this SQL query" → Planner/Coder (no Designer needed).
- **One-person teams**: If it's just you, you are all four agents. Document your decisions clearly.

For anything complex, multi-domain, or requiring consensus → **Orchestration pays dividends**.

---

### Q: How do I get teams to adopt this pattern?

**A**: Start small:
1. Use Orchestration for one complex task.
2. Document what improved (speed, clarity, quality).
3. Iterate: refine handoff templates based on what works.
4. Publish your learnings back to this repo.

---

## 10. Getting the Agents Running

### Prerequisites

- **Coder mode**: You need access to a Coder agent with tools for reading files, editing, running tests, and consulting docs (Context7).
- **Designer mode**: Designer agent with access to design systems, accessibility guidelines (WCAG), and existing app patterns.
- **Planner mode**: Planner agent with research tools, documentation access, and codebase search.
- **Orchestrator mode**: Orchestrator agent with the ability to call subagents and aggregate outputs.

### Quick Start: A Simple Multi-Agent Task

**Setup**:
1. Clone this repo or reference the agent definitions: `.github/agents/`
2. Use the agent definitions as system prompts for your chosen LLM (Claude, GPT, Gemini, etc.).
3. Create a simple task to test the pattern.

**Example task**:
```
"Add an export button to the data table. When clicked, 
 user downloads data as CSV. Should work offline."
```

**Run**:

1. **Orchestrator** reads the request and decides: delegate to Planner.
2. **Planner** researches existing export patterns, offline constraints, and produces a plan.
3. **Orchestrator** decides: Designer needed for button UX. Send Designer the Planner output.
4. **Designer** produces UX spec: button placement, states, accessibility.
5. **Orchestrator** sends Coder the Planner plan + Designer spec.
6. **Coder** implements, tests, and reports results.
7. **Orchestrator** reconciles and reports back.

**Expected output**:
- Planner: 1 ordered plan (no code).
- Designer: 1 UX spec (no code).
- Coder: Working feature, tests passing, validation steps.
- Orchestrator: Summary of what changed and how to validate.

---

### Deployment Checklist

Before running agents on your real codebase:

- [ ] **Read the agent files**: `.github/agents/Orchestrator.agent.md`, etc.
- [ ] **Test with a small task**: Export button, dark mode, simple feature.
- [ ] **Verify Planner researches repo correctly**: Does it find existing patterns?
- [ ] **Verify Coder respects repo constraints**: Does it follow MVVM? Respect offline-first?
- [ ] **Review handoff outputs**: Are they structured and clear?
- [ ] **Document any repo-specific constraints**: Add them to the Orchestrator prompt.
- [ ] **Set up a feedback loop**: How will you iterate if something goes wrong?

---

## 11. Extending the Pattern: Custom Agents

### The Core 4 Is a Foundation, Not a Ceiling

The **Orchestrator, Planner, Designer, and Coder** form a proven foundation for most AI-assisted development tasks. But they're not a straitjacket. Your team may benefit from **specialized agents** that handle repetitive, high-value work:

- **Validation**: Checking implementation against requirements (fast, iterative).
- **Testing**: Black-box testing, edge case discovery, test planning.
- **Security**: Scanning for vulnerabilities, compliance issues, best practices.
- **DevOps/Infrastructure**: Deployment, CI/CD, infrastructure-as-code.
- **QA Leadership**: Test strategy, coverage analysis, QA framework integration.
- **Documentation**: API references, changelog generation, docs synthesis.

**Key insight**: Add custom agents **only when the work is repetitive and high-value**. Avoid agent proliferation—each new agent adds coordination overhead.

---

### Example Custom Agents

#### 1. Validator Agent

**What it does**: Checks implementation against requirements. Runs faster, iterative passes over Coder output.

| Aspect | Details |
|--------|---------|
| **Purpose** | Verify Coder's output matches Planner's plan and Designer's spec WITHOUT implementing changes |
| **Best for** | Complex features where you want an extra review; iterative validation loops |
| **Model** | Faster model (Haiku, GPT-3.5) because it's checking concrete output, not generating |
| **Input** | Requirement list (from Planner) + design spec (from Designer) + implementation (from Coder) |
| **Output** | Validation report: Passes/failures, where issues are, severity (blockers vs. nice-to-have) |
| **Tools** | Code reading, spec comparison, checklist matching |

**Example workflow**:
```
Coder completes implementation → Orchestrator sends to Validator
Validator: "✓ All steps from Planner implemented. 
           ✓ Button styling matches Designer spec. 
           ✗ BLOCKER: Offline behavior not tested; needs test before merge."
Orchestrator → Coder: "Fix offline test."
Coder → Validator: "Test added, results: [pass]"
Validator: "✓ Validated. Ready."
```

---

#### 2. Independent Tester Agent

**What it does**: Black-box testing. Finds edge cases, stress-tests, verifies behavior without seeing implementation details.

| Aspect | Details |
|--------|---------|
| **Purpose** | Discover bugs that domain-knowledge testing might miss. "What if user does X while Y is happening?" |
| **Best for** | Critical features; concurrency-heavy code; user-facing workflows |
| **Model** | Faster model (Haiku, GPT-3.5) running many test iterations |
| **Input** | Feature description + Designer spec + Coder's feature |
| **Output** | Test report: scenarios tried, edge cases found, reproduction steps, severity |
| **Tools** | Running code, observing output, generating test scenarios |

**Example scenario**:
```
Feature: Voice input to chat (uploaded by Coder)

Tester tries:
1. Click record, speak, send → works? ✓
2. Click record, close tab → cleanup? ✓
3. Record, network drops, reconnect → resume? ✗ FOUND BUG
4. Record, app in background, OS reclaims mic → graceful fail? ✗ FOUND BUG
5. Record 10 messages rapid-fire → sync queue order preserved? Need test.

Report: 2 bugs, 1 question. Severity: 1 blocker, 1 low-priority.
```

---

#### 3. Security Auditor Agent

**What it does**: Scans for vulnerabilities, compliance issues, security best practices.

| Aspect | Details |
|--------|---------|
| **Purpose** | Catch common security pitfalls (injection, auth bypasses, data exposure) before production |
| **Best for** | Features handling user data, auth, API integrations, or touching infrastructure |
| **Model** | Can use consistent-quality model (capable but focused) |
| **Input** | Coder's implementation + threat model (if available) + relevant compliance standards |
| **Output** | Security report: vulnerabilities, risk level, remediation suggestions |
| **Tools** | Code review, OWASP checklist, security best-practice database |

**Example checklist**:
```
Checking: New API endpoint for user data export

Security checks:
✓ Input validation: All params checked
✓ Auth: Endpoint requires login
✓ Rate limiting: Implemented (5 requests/min)
✗ CRITICAL: CSV export doesn't escape quotes → CSV injection risk
✗ MEDIUM: Logs contain PII (user email in debug logs)
✓ Data retention: CSV deleted after 24h
✓ TLS: Enforced

Report: 2 issues found. Fix before merge.
```

---

#### 4. DevOps/Infrastructure Agent

**What it does**: Handles deployment, CI/CD configuration, infrastructure-as-code, environment setup.

| Aspect | Details |
|--------|---------|
| **Purpose** | Deploy features safely; manage CI/CD pipelines; infrastructure updates |
| **Best for** | Backend features, infrastructure changes, deployment to new environments |
| **Model** | Right-size to complexity (Haiku for routine deploys, stronger for infrastructure redesigns) |
| **Input** | Coder's implementation + deployment requirements + target environment spec |
| **Output** | Deployment plan, CI/CD changes, rollout strategy, rollback plan |
| **Tools** | Cloud CLI access, container building, pipeline scripting, environment management |

**Example task**:
```
Feature: Export data as CSV (needs backend + frontend)

DevOps handles:
1. Add CSV generation job to background queue
2. Update CI/CD to test CSV handling in pipeline
3. Add monitoring: track export job duration and failure rate
4. Deploy to staging first; verify export works; then production
5. Rollback plan: if exports fail, revert to v1.0 code

Output: Updated Dockerfile, .github/workflows/deploy.yml, monitoring queries
```

---

#### 5. QA Lead Agent

**What it does**: Test planning, coverage analysis, QA framework integration, test strategy.

| Aspect | Details |
|--------|---------|
| **Purpose** | Design comprehensive test strategy; ensure coverage; align tests with QA tooling |
| **Best for** | Features with complex test requirements; new test frameworks; coverage goals |
| **Model** | Right-size to strategy complexity |
| **Input** | Requirement list (from Planner) + implementation (from Coder) |
| **Output** | Test plan, coverage targets, test matrix, QA framework integration |
| **Tools** | Test framework knowledge, QA metrics, coverage analysis |

**Example plan**:
```
Feature: Voice input to chat

Test strategy:
- Unit tests: VoiceInputService (record, stop, error handling) → Target 90% coverage
- Integration tests: VoiceInputService + ChatViewModel → Target 85% coverage
- E2E tests: Record voice, send, verify in chat, sync → 5 scenarios
- Performance: Record time, transcription latency → Benchmarks
- Accessibility: Keyboard navigation, ARIA labels → WCAG checklist

Framework: xUnit for unit/integration, Selenium for E2E, lighthouse for perf

Coverage today: 45% → Target after: 88%
```

---

#### 6. Documentation Agent

**What it does**: Generates API docs, user guides, changelogs from code and commits.

| Aspect | Details |
|--------|---------|
| **Purpose** | Keep documentation in sync with implementation; reduce manual doc burden |
| **Best for** | Public APIs, user-facing features, changelog generation, SDK docs |
| **Model** | Faster model (Haiku) for generation; can iterate quickly |
| **Input** | Coder's implementation + Designer's UX spec + comments/docstrings |
| **Output** | Generated API docs, user guide section, changelog entry, example code |
| **Tools** | Code parsing, documentation template rendering, example generation |

**Example output**:
```
# Voice Input API

## Method: ChatViewModel.RecordVoice()

Starts recording audio from device mic. Returns task that completes 
when recording finishes.

### Parameters
- (none)

### Returns
- Task<VoiceRecording> — Recording object with audio blob URL

### Throws
- MicrophoneAccessDeniedException — User rejected mic permission
- DeviceBusyException — Another app is using the microphone

### Example
\`\`\`csharp
var recording = await chatVM.RecordVoice();
await chatVM.SendVoiceMessage(recording);
\`\`\`

### Offline behavior
If offline, voice blob queues locally. Sent on reconnect.

---

## User Guide: Sending Voice Messages

1. Click the microphone icon in the chat input.
2. Speak your message (timer shows elapsed seconds).
3. Click again or wait 2 seconds of silence to stop.
4. Microphone icon flashes green; transcription starts.
5. Text appears in chat input; click send to send or edit first.

**Tips:**
- Works offline; message syncs when reconnected.
- Clear audio works best (quiet environment, normal speaking voice).
```

---

### How to Add a Custom Agent

#### Step 1: Create the Agent Definition File

Create a new file in `.github/agents/YourAgentName.agent.md`:

```markdown
# YourAgentName Agent

## Role & Purpose
One-sentence summary of what this agent does.

## Constraints
- MUST always [rule 1]
- MUST never [rule 2]
- Respects [repo constraint]

## Input
- What does this agent receive?
- What context does it need?

## Output Format
- What structure should it produce?
- Example output

## Model Preference
- Recommended model: Haiku|GPT-3.5|Opus|GPT-4 and why
- Budget/cost tradeoff

## Authority & Scope
- What can it decide?
- What must it escalate to Orchestrator?

## Tools Required
- Code reading
- Execution
- External API calls
- ...

## Red Flags (Anti-patterns)
- Doing X would be wrong because Y
- If output looks like Z, ask for clarification

## Example Workflow
[Real example of this agent in action]
```

#### Step 2: Define Integration Points

Decide **when** to invoke the custom agent:

```
IF [condition] THEN invoke CustomAgent
```

**Examples**:

```
IF Coder finishes implementation AND feature involves user data
THEN invoke SecurityAuditor after Coder

IF Coder finishes implementation AND testing is critical
THEN invoke Tester parallel with Coder's final review

IF Planner proposes changes requiring infrastructure
THEN invoke DevOps after Planner planning
```

#### Step 3: Update Orchestrator Instructions

Add the custom agent to the Orchestrator's decision tree:

```
Orchestrator decision tree (updated):

User request arrives
│
├─ Orchestrator → Planner (always first)
├─ [Planner output]
├─ Orchestrator decides: Designer? Coder? CustomAgent?
│  ├─ Is this a feature? → Designer
│  ├─ Does it need implementation? → Coder
│  ├─ Does it involve validation loops? → Validator
│  ├─ Is testing critical? → Tester (parallel with Coder)
│  ├─ Touches security/data? → SecurityAuditor (after Coder)
│  ├─ Infrastructure needed? → DevOps (after Coder)
│  ├─ Need test strategy? → QALead (after Planner)
│  └─ User-facing output? → DocumentationAgent (after Coder)
│
└─ [Orchestrator reconciles and reports]
```

#### Step 4: Test the Integration

**First task**:
```
Run a real-world task that would benefit from the custom agent.

Example (for Validator):
"Build export feature. After Coder finishes, run Validator to 
check the implementation against requirements."

Verify:
1. Custom agent receives the right input.
2. Output is structured and actionable.
3. Orchestrator can process the output.
4. Did it catch real issues? Did it speed up validation?
```

---

### When to Use Faster vs. Slower Models

**Model choice depends on the task complexity and creativity required**, not just the agent type:

#### Slower/More Capable Models (Opus, GPT-4, Claude 3.5 Sonnet)

**Use for**:
- **Planner**: Researching complex codebases, identifying subtle edge cases, designing refactors.
- **Designer**: Novel UX challenges, design system creation, accessibility-first thinking.
- **Architect decisions**: Planning large restructures, choosing between complex tradeoffs.

**Why**: These tasks need deep reasoning, broad knowledge, and creative solutions. The cost justifies the capability.

```
Task: Plan a major sync engine refactor
Model: Opus/GPT-4
Reason: Must weigh tradeoffs (performance vs. storage vs. 
        complexity), consider edge cases, propose multiple 
        approaches. Deeper reasoning pays off.
```

---

#### Faster/Budget Models (Haiku, GPT-3.5)

**Use for**:
- **Coder**: Once a solid plan/spec exists, implementation is often concrete (follow the plan).
- **Validator**: Checking concrete output against a checklist (no creativity needed).
- **Tester**: Running many test scenarios (high iteration, low cost per test).
- **DocumentationAgent**: Generating docs from code (templated, concrete).
- **SecurityAuditor (routine checks)**: Scanning code against known vulnerability patterns.

**Why**: These tasks are bounded, iterative, or checking concrete output. Faster models are sufficient and cheaper.

```
Task: Validate Coder's feature against a checklist
Model: Haiku/GPT-3.5
Reason: Clear checklist, concrete output to verify. 
        Doesn't need deep reasoning. Can run many 
        validations cheaply.

Task: Run 20 test scenarios on a feature
Model: Haiku/GPT-3.5 × 20 iterations
Reason: Each test iteration is independent. Faster model × 
        high volume > fewer expensive runs.
```

---

#### Right-Sizing: The Tradeoff Matrix

| Task | Creativity? | Reasoning Depth? | Model | Notes |
|------|-------------|-----------------|-------|-------|
| Planner: design refactor | High | High | Opus/GPT-4 | Novel design needed |
| Coder: implement from plan | Low | Low | Haiku/GPT-3.5 | Follow spec, use faster |
| Designer: new UX pattern | High | Medium | Opus/GPT-4 | Novel UX design |
| Validator: check against spec | Low | Low | Haiku/GPT-3.5 | Checklist matching |
| Tester: find edge cases | Medium | Medium | Can iterate with Haiku | Haiku × high volume |
| SecurityAuditor: scan for bugs | Low | Medium | Haiku (routine) / GPT-4 (novel threats) | Routine checks → Haiku; novel → GPT-4 |
| QALead: test strategy | Medium | High | Opus/GPT-4 | Strategy design needs depth |

---

### Avoiding Agent Proliferation

**The risk**: Every task looks like it needs a specialist. You end up with 20 agents, each waiting on the other.

**The principle**: Add a custom agent **only if it's high-value and fits naturally into the workflow**.

#### Start With the Core 4

Use Orchestrator, Planner, Designer, and Coder for at least **3–5 tasks** before adding a custom agent.

**Questions to ask before adding a custom agent**:

1. **Is this work repetitive?**  
   - E.g., "Every feature needs security review" → Yes, add SecurityAuditor.
   - E.g., "This one feature is complex" → No, don't add yet.

2. **Does it reduce Orchestrator cognitive load?**  
   - E.g., "Validator saves Orchestrator from manually checking every Coder output" → Yes.
   - E.g., "Custom agent just parrots Coder's output" → No, skip it.

3. **Can it run in parallel with other agents, or does it add serial time?**  
   - E.g., "Tester runs parallel with Coder's final review" → Parallelizes, good.
   - E.g., "DocumentationAgent runs after Coder, adding 30min" → Worth it if docs are critical.
   - E.g., "Custom agent must wait for 3 others, then blocks 2 more" → Serial bottleneck, reconsider.

4. **Would the core 4 agents already be doing this work?**  
   - E.g., "Designer already specifies accessibility. Do we need a dedicated A11n agent?" → Probably not.
   - E.g., "Coder writes comprehensive tests. Do we need an Independent Tester?" → Maybe (depends on risk).

#### Red Flags: Agent Proliferation

- ❌ **More than 2 custom agents running on routine tasks**: You've added too much overhead.
- ❌ **Custom agent output is just a filtered view of existing agent output**: It's redundant; don't add it.
- ❌ **Orchestrator spends more time coordinating agents than the agents spend working**: You've gone too far.
- ❌ **Adding agents to "be safe"**: Test first; if the core 4 handle it fine, don't add.

#### Sweet Spot: 2–4 Custom Agents Max

For most teams:
- **Core 4** (always)
- **+1–2 custom agents** for your team's highest-value, most-repeated tasks
- **Example**: Core 4 + Validator + SecurityAuditor (for a security-critical fintech app)

---

### Sample Agent Definition Template

Here's a minimal template you can copy and customize:

```markdown
# [AgentName] Agent

## Role & Purpose

[One sentence: What does this agent do? When should Orchestrator call it?]

Example: "Validator checks Coder's implementation against Planner's 
requirements and Designer's spec, catching misalignments before merge."

## Authority & Constraints

### MUST Always
- [Non-negotiable rule 1]
- [Non-negotiable rule 2]
- Respect [repo constraint] (e.g., offline-first, sync safety)

### MUST Never
- [Never do X because Y]
- [Never assume Z without verifying]

### Can Escalate to Orchestrator If
- [Situation 1] (e.g., "Conflict between Planner's plan and reality")
- [Situation 2]

## Input

**Receives**:
- [Source 1]: [Description]
- [Source 2]: [Description]

**Example input**:
```
Planner output: [attached]
Designer spec: [attached]
Coder's implementation: [attached]
Validation checklist: [attached]
```

## Output Format

**Structure**:
- [Section 1]: [What does it include?]
- [Section 2]: [What does it include?]
- Result: PASS | FAIL | NEEDS_REVISION

**Example output**:
```
VALIDATION REPORT

Checklist status:
✓ Requirement A implemented
✓ Requirement B implemented
✗ Requirement C missing (BLOCKER)

Design spec compliance:
✓ Button styling matches spec
✓ Accessibility ARIA labels present
✗ Dark mode colors incomplete (MEDIUM)

Risks:
- Offline behavior not tested (should be before merge)

Recommendation: NEEDS_REVISION (fix blockers, then re-validate)
```

## Model Preference

**Recommended**: [Haiku|GPT-3.5|Opus|GPT-4]

**Reasoning**: [Why this model? Cost-quality tradeoff?]

Example: "Haiku/GPT-3.5 sufficient because checking against 
concrete checklist, no creativity required. Can iterate many 
times cheaply."

## Tools Required

- [Tool 1]: for reading [what]
- [Tool 2]: for checking [what]
- [Tool 3]: for [what]

Example:
```
- Code reading: compare implementation against spec
- Checklist matching: verify all requirements present
- Documentation: reference Planner's original plan
```

## Integration: When to Call This Agent

**Orchestrator decision**:
```
IF [condition] THEN invoke [AgentName]

Example:
IF Coder finishes + feature involves external APIs
THEN invoke SecurityAuditor after Coder
```

**Parallel or serial**?
- Serial (blocks next step): When blocking issues must be caught before proceeding
- Parallel: When it provides feedback without blocking others

**Example workflow**:
```
Coder finishes implementation
│
├─ Orchestrator → Validator (quick pass)
├─ Validator finds issues
├─ If BLOCKER: Coder addresses, loop back to Validator
├─ If PASS: Proceed to merge/deploy
```

## Red Flags & Anti-Patterns

🚨 **Red Flag #1**: [Situation that indicates misuse]  
**What to do**: [How to correct it]

Example:
```
🚨 Agent output is just a copy of Coder's output
What to do: Agent is redundant; don't use it.
```

## Example: Real Workflow

**Task**: Build voice input feature (from earlier section)

**When would this agent activate?**
- [If applicable, show realistic scenario]

**Input received**:
- [What does it get?]

**Output**:
- [What does it produce?]

---

## Questions?

If Orchestrator is unsure how to use this agent, what should it ask?

Example:
```
"Should I run Validator after every Coder task, or only for 
high-risk features?"
Answer: "Only for features touching data, security, or offline sync."
```
```

---

### Custom Agent Example: Validator in Action

**Scenario**: Voice input feature ready for validation.

**Orchestrator sends to Validator**:
```
You are the Validator. Review this implementation against 
requirements.

PLANNER REQUIREMENTS (from original plan):
1. User speaks → audio captured on device ✓
2. Audio stored offline locally ✓
3. Sent to backend for transcription on sync
4. Text returned to chat input
5. Works offline (feature syncs on reconnect)

DESIGNER SPEC (from UX spec):
- Button: right of input field, 45% opacity idle
- States: idle, hover, recording, processing, success, error
- ARIA labels: "Record voice message"
- Keyboard: Tab focus, Space/Enter to toggle

CODER OUTPUT (implementation):
[code snippets and test results]

VALIDATION CHECKLIST:
- Does implementation match all Planner requirements?
- Does UI match Designer spec?
- Are tests present for offline behavior?
- Any security concerns (audio data handling)?

OUTPUT FORMAT:
Validation report with pass/fail per requirement.
```

**Validator responds**:
```
VALIDATION REPORT

Planner Requirements:
✓ 1. Audio capture on device (implemented, tested)
✓ 2. Offline storage (local blob queue verified)
✓ 3. Backend transcription (integration test passes)
✓ 4. Text to input (working)
✗ 5. BLOCKER: Offline sync not tested. Feature syncs on reconnect 
       but no test confirms blobs preserve order during sync.

Designer Spec:
✓ Button placement matches (right of input)
✓ States all implemented (hover animation verified)
✓ ARIA labels present
⚠ Keyboard: Tab works, but Space/Enter not wired to start 
           recording. Medium priority.

Accessibility:
✓ WCAG contrast verified (7:1 ratio confirmed)
✓ Screen reader announces "Recording" status

Risks & Notes:
- Audio permission handling: gracefully degrades if denied ✓
- Memory: large audio files chunked? Need to verify max blob size.

RECOMMENDATION: NEEDS_REVISION
- Fix: Add test for offline blob sync ordering
- Fix: Wire Space/Enter to recording toggle
- Optional: Verify max audio file size handling

Estimated time to address: 2–4 hours
Next step: Resubmit after fixes; Validator will re-check.
```

---

### When Not to Add a Custom Agent

✅ **Core 4 handles it fine**: Don't add an agent.  
Example: "Designer already does accessibility review. Don't add separate A11n agent."

✅ **Work is not repetitive**: Don't add an agent.  
Example: "This one feature needs extra testing. Use Tester just for this task, don't formalize it."

✅ **Agent would create a bottleneck**: Don't add it.  
Example: "QA agent would need output from Coder, before anyone can review. That's already what Orchestrator does."

---

## 12. Contributing & Feedback

### We Want to Hear From You

This pattern is tested in production but evolving. If you adopt Orchestration in your team, **share learnings**:

- **What worked?** Which delegation patterns are golden for your codebase?
- **What failed?** Where did agents break down? What guardrails would help?
- **What's missing?** Are there roles beyond Planner/Designer/Coder? Edge cases?

### How to Contribute

1. **Test the pattern** on a real task in your repo.
2. **Document your experience**:
   - Which tasks benefited most from Orchestration?
   - Where did it feel like overhead?
   - Any new anti-patterns discovered?
3. **Open an issue** or **pull request** with:
   - A brief description of your task.
   - How Orchestration helped (or didn't).
   - Any template improvements or missing guidance.
4. **Share agent prompts** if you've customized them for your repo.

### Feedback Template

```
## Task Summary
[What did you build?]

## Orchestration Workflow Used
[Orchestrator → Planner → Designer/Coder]

## What Went Well
- [Parallel execution speeded up planning?]
- [Clear handoff prevented rework?]

## What Could Improve
- [Were handoffs unclear?]
- [Did an agent struggle with repo constraints?]

## Suggested Changes to Pattern
[Any additions to the guide? New templates?]
```

---

## Recommended Reading

- [The Four Agents: Full Definitions](.github/agents/)
  - `Orchestrator.agent.md`
  - `Planner.agent.md`
  - `Designer.agent.md`
  - `Coder.agent.md`

---

## License

This pattern is open for adaptation and use. Cite this repo if you publish your own variations.

---

## Questions?

Open an issue or start a discussion. **Let's build better AI-assisted software together.**

---

**Last updated**: February 2026  
**Pattern version**: 1.0  
**Status**: Production-tested, evolving based on community feedback
