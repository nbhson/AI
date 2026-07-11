# Phase 5 Hook - Report Generation

## Goal

Generate comprehensive structured reports documenting all work completed, validation results, and evidence for archival and audit purposes.

## Trigger

Run this hook after Phase 4 (Validation & PR Preparation) is complete.

## Required Steps

1. **Select Report Template**: Choose the appropriate template based on workflow type:
   - **Bug Fix** → `bug-report.md`
   - **Feature Delivery** → `engineering-report.md`
   - **Refactor** → `engineering-report.md` (with planning context)
   - **Unit Test** → `engineering-report.md`
   - **Code Review** → `pull-request.md`
   - **Jira Ticket Review** → `jira-ticket-review.md`

2. **Complete Report Content**:
   - High-level summary of completed work
   - List of all files modified with line references
   - Test results and validation outputs
   - Build verification evidence (hash, time, size)
   - Type checking and linting results
   - Manual verification steps (if applicable)
   - Risk assessment and backward compatibility notes
   - PR link (once created)

3. **Generate Report File**:
   - Save to `.github/reports/` directory
   - Use naming convention: `[WORKFLOW-TYPE]-REPORT-[COMPONENT/TICKET]-[DATE]-[TIME].md`
   - Examples:
     - `REFACTOR-REPORT-ValidatorSupportUtil-20260701-1531.md`
     - `BUG-REPORT-HZ2-1234-20260630-1430.md`
     - `ENGINEERING-REPORT-CustomViewFiltering-20260630-1415.md`

4. **Include Evidence**:
   - Build log excerpts with hash and duration
   - Test output summaries
   - ESLint/TypeScript validation results
   - Screenshots or execution logs (if relevant)
   - Performance metrics (if applicable)

5. **Add PR Reference**:
   - Once PR is created, update report with PR link
   - Include PR URL for traceability

## Report Template Structure

All reports should follow:
1. **Header**: Workflow type, component/ticket ID, and timestamp
2. **Summary Section**: High-level overview of changes
3. **Files Modified**: Workspace-relative paths with line numbers
4. **Verification Section**: Test results, build verification, type checking
5. **Quality Metrics**: Code changes impact analysis
6. **Backward Compatibility**: Breaking changes assessment
7. **Risks & Mitigation**: Potential issues and how addressed
8. **Evidence & References**: Links to files and PR

## Related Skills

- [Report Generation Skill](../../skills/report-generation/SKILL.md)

## Exit Criteria

- Report file generated and saved to `.github/reports/`
- All required sections completed with specific details
- Evidence links and references included
- Report is accessible and archive-ready
- (Optional) PR link added once PR is created
