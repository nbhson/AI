# Phase 4 Hook - Validation and PR Preparation

## Goal

Validate final quality and prepare for pull request.

## Trigger

Run this hook after Phase 3 outputs are available.

## Required Steps

1. Run compile/build validation:
   - `npm run build` (or `npm run build-prod` when needed)
2. Run unit tests:
   - `npm run test`
3. Prepare PR details:
   - branch context
   - summary of changes
   - risk/impact notes
   - confirm all commits in the PR follow format: `SP0168-<ticket number>: <short summary>`
4. Review diffs for unintended changes before PR submission.

## Related Detailed Hooks

- `.github/skills/pre-pull-request/SKILL.md`
- `.github/hooks/phase-5-report-generation.hook.md` (for report generation)

## Exit Criteria

- Build and test validations are completed.
- PR branch and description are ready.
- PR commit messages follow `SP0168-<ticket number>: <short summary>`.
- All code diffs reviewed.

## Failure Handling

- If any Phase 4 validation step fails (build, test, or diff review), return to Phase 3 (Execution and Formatting) to apply fixes, then re-run Phase 4 until all exit criteria pass.
