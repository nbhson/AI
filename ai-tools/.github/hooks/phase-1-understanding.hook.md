# Phase 1 Hook - Understanding

## Goal

Load the correct guidance and build accurate context before proposing or implementing changes.

## Trigger

Run this hook immediately after receiving a developer request.

## Required Steps

1. Determine workflow type:
   - Jira Ticket Review
   - Feature Delivery
   - Bug Fix
   - Refactor
   - Unit Test
   - Code Review
2. Load guidelines for that workflow from `.github/skills/`.
3. Load repository guidance from:
   - `.github/copilot-instructions.md`
   - `.github/knowledge/`
   - `.github/skills/` (as relevant)
   - `.github/report/` (as relevant)
4. Load report context from `.github/reports/`:
   - **Strict Mode**: Must read the latest workflow-relevant `*.ctx.md` (or latest report for the same ticket/component), then carry forward decisions, risks, unresolved items, and validation evidence.
   - **Non-Strict Mode**: For non-trivial/code-change tasks, read the latest relevant report summary first; for trivial/no-code queries, report loading may be skipped.
   - Use report context as planning input to avoid repeated analysis.
5. Investigate code context:
   - Analyze target modules/files and dependencies.
   - Search for reusable constants, models, shared components, and services.
   - Avoid duplicate code.
6. Apply stack rules:
   - Angular 15 module patterns
   - SCSS conventions
   - service import and coding style constraints

## Exit Criteria

- Workflow type is identified.
- Relevant skills/knowledge are loaded.
- Strict Mode: relevant report context from `.github/reports/` is loaded.
- Non-Strict Mode: report context is loaded for non-trivial/code-change tasks.
- Investigation findings are captured and ready for planning.
