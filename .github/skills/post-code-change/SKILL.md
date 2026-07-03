---
name: post-code-change
description: Automated post-code modification rules for cleaning imports, formatting files, running ESLint and checking for credentials in the Horizon 2 UI project.
---

# Hook: Post Code Change

After making code changes, execute the following quality and verification steps:

---

## Required Steps

1. **Format & Lint**: Run formatting and linting for the modified files or package scripts:
   - `npm run lint`
   - `npm run format`

2. **Clean Code**: Remove unused imports, variables, class properties, or dead code.

3. **Security Check**: Verify that no credentials, passwords, client secrets, API tokens, or sensitive values were introduced.

4. **Alignment Verification**: Ensure all changes remain aligned with the [Core Engineering Guidelines](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/knowledge/core-engineering-guidelines.md).

5. **Generate Draft Report**: After all quality checks pass, generate a structured draft report based on your workflow type:
   - Follow the [Report Generation Skill](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/skills/report-generation/SKILL.md).
   - Use the appropriate template from `/.github/report-templates/`.
   - Save the report to `.github/reports/` with the naming convention: `[WORKFLOW-TYPE]-REPORT-[ID]-[DATE].md`.

---

## Related Templates

- **Bug Report**: `/.github/report-templates/bug-report.md`
- **Engineering Report**: `/.github/report-templates/engineering-report.md`
- **Technical Design**: `/.github/report-templates/technical-design.md`
