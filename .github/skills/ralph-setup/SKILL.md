---
name: ralph-setup
description: |
  Bootstrap a new repository for AI-driven ralph-loop automation using GitHub Copilot.
  Use when: starting a new app project, setting up ralph-loop, bootstrapping a repo for automated building,
  initializing a copilot-driven development workflow.
  Triggers: ralph setup, new app, bootstrap project, setup ralph, setup automation, new ralph project.
argument-hint: 'Optional: brief description of the app to build'
---

# Ralph-Loop Project Setup

Bootstrap a new repository for fully automated AI-driven development using the ralph-loop (`main.py` + Copilot). Produces a ready-to-build project with a virtual environment, automation scaffolding, a polished specification, and a phased implementation plan.

## When to Use

- Starting a brand new app project from scratch
- Setting up an existing repo for ralph-loop automation
- Onboarding a project idea into the automated build pipeline

## Procedure

### Step 1: Create Python Virtual Environment

Create a `.venv` in the project root and install the `copilot` package needed by `main.py`.

```bash
python -m venv .venv
.venv\Scripts\python.exe -m pip install github-copilot-sdk
```

Verify `main.py` can import the `copilot` package:

```bash
.venv\Scripts\python.exe -c "import copilot; print('OK')"
```

### Step 2: Scaffold Automation Files

The ralph-loop requires these files at the project root. Copy them from the template repository:

**Source**: `https://github.com/adam-kleski_harman/my.github.copilot.data.git`

Clone or fetch the template files from the repository above.

**Required files** (create only if missing):

| File | Purpose |
|------|---------|
| `main.py` | Ralph-loop entry point (plan + build modes) |
| `PROMPT_build.md` | Build-mode prompt (picks next `[ ]` task, implements, marks `[x]`) |
| `PROMPT_plan.md` | Plan-mode prompt (gap analysis, generates checkboxed plan) |
| `AGENTS.md` | Build & validation commands for the project |
| `.gitignore` | Ignore `.venv/`, `node_modules/`, etc. |

Also ensure these directories exist:
- `spec/` — for specification documents
- `src/` — for source code

### Step 3: Capture Application Specification

Use the **ask-questions** tool to ask the user to describe their application idea:

> **What application do you want to build?** Describe the purpose, target platform, key features, and any technology preferences.

Once the user provides their description, invoke the **`Thinking Beast Mode`** subagent with the following prompt:

```
You are a technical specification writer.

## Raw Input
<paste user's app description here>

## Instructions
1. Transform the raw description into a detailed, structured specification
2. Organize into clear sections: Overview, Functional Requirements,
   Technical Stack, UI Components, Constraints, and Next Steps
3. Use tables where appropriate for component inventories
4. Be specific about technology choices (frameworks, libraries, versions)
5. Add any obvious requirements the user may have missed
6. Write in a clear, professional tone suitable for AI consumption
7. Output ONLY the specification content (no preamble)
```

Save the output to `spec/general.md`.

### Step 4: Generate Implementation Plan

Invoke the **`implementation-planner`** skill with `spec/general.md` as context. This will:

1. Read the specification from `spec/general.md`
2. Invoke the `Implementation Plan Generation Mode` subagent
3. Produce `IMPLEMENTATION_PLAN.md` at the project root with `[ ]` checkboxes

### Step 5: Finalize and Prompt User

After all steps complete, present the summary:

```
✅ Ralph-loop project setup complete!

📁 Project structure:
   spec/general.md          — Application specification
   IMPLEMENTATION_PLAN.md   — Phased plan with [ ] checkboxes
   main.py                  — Ralph-loop automation
   PROMPT_build.md          — Build-mode instructions
   PROMPT_plan.md           — Plan-mode instructions
   .venv/                   — Python virtual environment

🚀 To start building, run:
   .venv\Scripts\python.exe .\main.py build
```

## Output Artifacts

| File | Purpose |
|------|---------|
| `.venv/` | Python virtual environment with `copilot` package |
| `spec/general.md` | Polished application specification |
| `IMPLEMENTATION_PLAN.md` | Checkboxed implementation plan for ralph-loop |
| `main.py`, `PROMPT_*.md` | Automation scaffolding (if not already present) |
