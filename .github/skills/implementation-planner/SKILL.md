---
name: implementation-planner
description: |
  Generate a detailed, structured implementation plan from spec files using the Implementation Plan subagent.
  Use when: creating implementation plans, planning new features, starting a new project build cycle,
  preparing plans for ralph-loop execution, converting specifications into actionable tasks.
  Triggers: implementation plan, plan from specs, generate plan, spec to plan, ralph plan, create plan.
argument-hint: 'Optional: specific spec file or feature area to focus on (default: all specs)'
---

# Implementation Planner

Generate a fully structured implementation plan by feeding specification files to the `Implementation Plan Generation Mode` subagent. The output is a single `IMPLEMENTATION_PLAN.md` at the project root with checkbox-tracked phases and tasks, designed for consumption by the ralph-loop build process.

## When to Use

- Starting a new project and need a structured build roadmap
- Converting specification documents into phased, executable tasks
- Preparing `IMPLEMENTATION_PLAN.md` for ralph-loop execution
- Updating an existing plan after spec changes
- Planning a feature area from a subset of specs

## Procedure

### Step 1: Gather Specifications

Read all specification files from the `spec/` directory at the project root. If the user specifies a focus area, filter to relevant specs only.

```
spec/           ← Specification source (project root)
```

Also read:
- Existing `IMPLEMENTATION_PLAN.md` (if present) to understand prior progress
- `src/` directory structure to understand what's already implemented

### Step 2: Analyze Current State

Before invoking the subagent, perform a gap analysis:

1. **Read all spec files** — collect requirements, constraints, and feature descriptions
2. **Scan `src/`** — identify what's already built vs. what's missing
3. **Check existing plan** — if `IMPLEMENTATION_PLAN.md` exists, note completed tasks (items marked `[x]`)

Compile a context summary:
- Full spec content
- List of existing source files and their purpose
- Completed vs. remaining work (if plan exists)

### Step 3: Invoke Subagent

Invoke the **`Implementation Plan Generation Mode`** subagent with a prompt structured as follows:

```
You are generating an implementation plan for the project.

## Specifications
<paste full spec content here>

## Existing Code
<list of existing files in src/ with brief descriptions>

## Existing Plan Status
<completed tasks from current IMPLEMENTATION_PLAN.md, or "No existing plan">

## Instructions
1. Analyze all specifications and identify implementation phases
2. Create a phased plan following your mandatory template structure
3. Each task must reference specific files, components, and implementation details
4. Order phases by dependency — foundational work first
5. Include testing tasks for each phase
6. Write EVERYTHING into a single file: IMPLEMENTATION_PLAN.md at the project root
7. CRITICAL — use markdown checkboxes for every phase and every task:
   - Phase-level: `- [ ] **Phase 1: Project Setup**`
   - Task-level:  `  - [ ] TASK-001: Description`
   The ralph-loop will mark completed items as `[x]`.
   Preserve any existing `[x]` marks from a prior plan.
```

### Step 4: Validate Output

After the subagent completes, verify:

1. **`IMPLEMENTATION_PLAN.md`** exists at project root
2. **Every phase heading** has a `- [ ]` or `- [x]` checkbox
3. **Every task** under a phase has a `- [ ]` or `- [x]` checkbox
4. **All spec requirements** are covered by at least one task (REQ traceability)
5. **Phases are ordered** by dependency — no forward references
6. **Tasks are atomic** — each can be completed in a single ralph-loop iteration

### Step 5: Present Summary

Report to the user:
- Number of phases and tasks generated
- Key requirements covered
- Any spec ambiguities or gaps discovered
- Suggested next step: `python main.py build` to start ralph-loop execution

## Output Format

A single file — `IMPLEMENTATION_PLAN.md` in the project root — containing the full structured plan with checkbox tracking.

Example structure:

```markdown
# Implementation Plan: KubbTracker

- [ ] **Phase 1: Project Scaffolding**
  - [ ] TASK-001: Initialize Vite + React + TypeScript project
  - [ ] TASK-002: Configure Capacitor for Android
  - [ ] TASK-003: Set up Tailwind CSS with high-contrast palette

- [ ] **Phase 2: Core Game State**
  - [ ] TASK-004: Implement game state machine (Context/Reducer)
  - [ ] TASK-005: Model Kubb positions (baseline vs field)
  - [ ] TASK-006: Add turn workflow logic

- [x] **Phase 3: UI Components**
  - [x] TASK-007: Build pitch top-down view
  - [x] TASK-008: Create Kubb and King SVG icons
  - [ ] TASK-009: Implement turn indicator banner
```

The ralph-loop checks `[x]` to skip completed work and picks the next `[ ]` task.

## Integration with ralph-loop

The ralph-loop (`main.py`) operates in two modes:

- **plan mode** (`python main.py plan`): Reads specs and updates `IMPLEMENTATION_PLAN.md`
- **build mode** (`python main.py build`): Picks top unchecked `[ ]` task from `IMPLEMENTATION_PLAN.md` and implements it

This skill replaces the plan mode with a more thorough, subagent-driven planning process that produces a richer initial plan. The ralph-loop build mode then consumes the plan iteratively, marking `[x]` as tasks complete.
