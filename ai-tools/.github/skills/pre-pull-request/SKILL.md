---
name: pre-pull-request
description: Quality checklist, git branch verification, commits, and PR descriptions to run before pulling and pushing code in the Horizon 2 UI project.
---

# Hook: Pre Pull Request (Pre-PR)

Before creating a commit, pushing code, or submitting a Pull Request, execute the following verification steps:

---

## Required Steps

1. **Build Check**: Run the relevant build command to ensure no compilation errors:
   - `npm run build` (for production build)
   - Or verify that your local development build completes without errors.

2. **Test Verification**: Run unit tests to confirm all changes are covered and pass:
   - `npm run test`

3. **Branch & Commit Clarity**:
   - Checkout a clean branch from the main target (e.g., `feat/feature-name` or `fix/bug-id`).
   - Use this mandatory commit message format for all PR commits: `SP0168-<ticket number>: <short summary>`.
   - Example: `SP0168-3417: fix loading issue in create IPG budget request`.

4. **Diff Review**:
   - Review all file changes for redundant edits, accidental modifications, or missing documentation.
   - Verify that no unrelated or temporary code/logs are included.

5. **Alignment Check**:
   - Ensure the implementation follows repository guidance in [Core Engineering Guidelines](../../knowledge/core-engineering-guidelines.md).

6. **Finalize Report**:
   - Complete and review the generated report file in `.github/reports/`.
   - Update with final test results and build validation.
   - Add the PR link once created.
   - Refer to the [Report Generation Skill](../report-generation/SKILL.md).

7. **Submit PR**:
   - Push the branch to the remote git repository.
   - Propose opening a Pull Request. Fill out the description using the Pull Request template (`.github/report-templates/pull-request.md`).

---

## Templates Reference

- **Pull Request Template**: `/.github/report-templates/pull-request.md`
- **Engineering Report**: `/.github/report-templates/engineering-report.md` (for non-bug workflows)
