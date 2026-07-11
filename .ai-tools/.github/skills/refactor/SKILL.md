---
name: refactor
description: Step-by-step workflow for smell assessment, technical planning, incremental edits, unit test updates, and validation of refactored code in the Horizon 2 UI project.
---

# Workflow: Refactor

This document outlines the workflow for improving the structure, style, or performance of the codebase without breaking features in the Horizon 2 UI application.

---

## Required Steps & Phases

1. **Smell Assessment**: Identify candidates for refactoring (such as large modules that need decomposition into container/presenter roles, duplicate `@import` rules, hardcoded variables in SCSS, direct DOM manipulation, or duplicate interfaces).
2. **Plan & Get Approval**: Detail the refactoring changes in `implementation_plan.md` using the Technical Design template (`.github/report-templates/technical-design.md`). Obtain explicit developer confirmation before executing code changes if the task is non-trivial.
3. **Incremental Changes**: Make changes incrementally while preserving existing behavior. Keep refactoring sets focused, reuse shared assets, and strictly follow the project naming, module, and styling conventions.
4. **Update Unit Tests**: Update corresponding unit tests in spec files (`*.spec.ts`) to match the refactored code and ensure proper mock configurations.
5. **Formatting & Quality Checks**: Follow the [Post Code Change Skill](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/skills/post-code-change/SKILL.md) for canonical lint, format, and cleanup requirements.
6. **Validation & Report**:
   - Run unit tests to verify no regressions: `npm run test`.
   - Run production compilation check: `npm run build`.
   - Manually test and inspect the layouts on local dev: `npm run startdev`.
   - Generate/finalize report artifacts through the [Report Generation Skill](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/skills/report-generation/SKILL.md) and Phase 4 (`.github/hooks/phase-4-validation-pr.hook.md`).
