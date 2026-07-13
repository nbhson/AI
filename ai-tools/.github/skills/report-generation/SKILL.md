---
name: report-generation
description: Guide for selecting, completing, and saving structured reports to the .github/reports directory in the Horizon 2 UI project.
---

# Hook: Report Generation Guide

After completing any development workflow (Bug Fix, Feature Delivery, Refactor, Unit Test), generate a structured report based on the workflow type and save it to the `.github/reports` directory.

---

## Report Generation Steps

### 1. Select the Appropriate Template

Based on your workflow type, select the corresponding template from `/.github/report-templates/`:

| Workflow Type | Template File | Report File Name |
| :--- | :--- | :--- |
| **Bug Fix** | `bug-report.md` | `BUG-REPORT-[TICKET-ID]-[DATE].md` |
| **Feature Delivery** | `engineering-report.md` | `ENGINEERING-REPORT-[FEATURE-NAME]-[DATE].md` |
| **Refactor** | `technical-design.md` (planning phase) + `engineering-report.md` (completion) | `REFACTOR-REPORT-[COMPONENT-NAME]-[DATE].md` |
| **Unit Test** | `engineering-report.md` | `TEST-REPORT-[COMPONENT-NAME]-[DATE].md` |
| **Code Review** | `pull-request.md` | `CODE-REVIEW-[PR-ID]-[DATE].md` |

### 2. Fill in the Template

Complete all sections of the selected template with:
- Specific details from your work
- File paths and line numbers referencing modified code
- Test results and validation evidence
- Screenshots or execution logs where relevant
- Clear summary of changes and their impact

### 3. Save the Report

Save the completed report to `.github/reports/` with the naming convention above. Include:
- A descriptive timestamp (YYYYMMDD-HHMM format)
- The ticket ID or component name
- The workflow type

Example file names:
- `BUG-REPORT-HZ2-1234-20260630-1430.md`
- `ENGINEERING-REPORT-CustomViewFiltering-20260630-1415.md`
- `REFACTOR-REPORT-SharedComponentModule-20260630-1400.md`

### 4. Link to Pull Request (if applicable)

If creating a Pull Request, update the report template with the PR link for easy reference.

---

## Integration with Workflows

Reports are generated **after** each workflow phase is complete:
- **Phase 3 Completion** (Code Formatting & Linting): Generate draft report with current implementation status.
- **Phase 4 Completion** (Validation & PR): Finalize report with test results and PR link.

---

## Report Format

All reports should follow this structure:
1. **Header**: Workflow type, ticket/component ID, and date
2. **Summary Section**: High-level overview of work completed
3. **Details Section**: In-depth information about files, changes, and logic
4. **Verification Section**: Test results, build verification, manual checks
5. **References**: Links to PR, tickets, related documentation

---

## Archiving Old Reports

Periodically clean up old reports (older than 30 days) to maintain repo cleanliness. Keep reports as reference artifacts in git history.
