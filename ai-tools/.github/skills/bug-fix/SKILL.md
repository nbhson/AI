---
name: bug-fix
description: Step-by-step workflow for reproducing, patching, formatting, testing, and creating bug reports in the Horizon 2 UI project.
---

# Workflow: Bug Fix

This document outlines the systematic flow for reproducing, identifying, fixing, and validating bugs in the Horizon 2 UI application.

---

## Required Steps & Phases

1. **Replicate & Document**: Replicate the bug on the local development server. Document the details using the Bug Report template (`.github/report-templates/bug-report.md`).
2. **Investigation & Scope**: Identify the root cause. For complex bug fixes, prepare a design in `implementation_plan.md` using the Technical Design template (`.github/report-templates/technical-design.md`). For simple/isolated fixes, proceed directly.
3. **Draft Plan**: Before implementation, provide a concise draft plan and wait for review/approval when the task is non-trivial.
4. **Implement Fix**: Make targeted changes following the [Core Engineering Guidelines](../../knowledge/core-engineering-guidelines.md). Search existing constants, models, shared components, and services before introducing new logic. Avoid unrelated code modifications.
5. **Write/Update Unit Tests**: Add or update Jest unit tests in the corresponding `*.spec.ts` file to verify the bug fix.
6. **Formatting & Quality Checks**: Follow the [Post Code Change Skill](../post-code-change/SKILL.md) for canonical formatting, linting, and cleanup.
7. **Validation**:
   - Run unit tests to verify changes: `npm run test` (or run the specific spec file).
   - Run production compilation check: `npm run build`.
   - Verify layout and functionality manually on the local development server: `npm run startdev`.
8. **Report**: Generate and complete report artifacts using the [Report Generation Skill](../report-generation/SKILL.md) and finalize in Phase 4 (`.github/hooks/phase-4-validation-pr.hook.md`).

---

## Deliverables

- **Clear Summary of the Root Cause**: Explanation of what caused the bug and how it was diagnosed.
- **Minimal Code Change**: Targeted, clean fix with no duplicate or redundant logic.
- **Validation Evidence**: Output/evidence from running lint, build, and test commands.
