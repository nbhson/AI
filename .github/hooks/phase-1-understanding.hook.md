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
4. Investigate code context:
   - Analyze target modules/files and dependencies.
   - Search for reusable constants, models, shared components, and services.
   - Avoid duplicate code.
5. Apply stack rules:
   - Angular 15 module patterns
   - SCSS conventions
   - service import and coding style constraints

## Exit Criteria

- Workflow type is identified.
- Relevant skills/knowledge are loaded.
- Investigation findings are captured and ready for planning.
