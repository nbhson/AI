---
name: code-review
description: Peer review checklist covering Angular 15, RxJS subscriptions, SCSS imports, restricted imports, security, and performance in the Horizon 2 UI project.
---

# Workflow: Code Review

Instructions for reviewing code changes, pull requests, and validating coding conventions in the Horizon 2 UI application.

---

## Review Checklist

1. **Architecture & Structure**:
   - Ensure components are module-based and correctly registered in their NgModules (not standalone components).
   - Verify container vs. presenter architecture (container handles services/state; presenter accepts `@Input()`, emits `@Output()`, and implements `changeDetection: ChangeDetectionStrategy.OnPush`).

2. **TypeScript & Angular 15 Rules**:
   - Check that **NO** Angular Signals or `resource()` APIs are used.
   - Enforce RxJS subscription management (always use `takeUntil(this.destroy$)` or the `async` pipe in templates to prevent memory leaks).
   - Enforce standard decorators (`@Input()`, `@Output()`, `@ViewChild()`).
   - Prohibit `@if` / `@for` syntax; ensure `*ngIf` / `*ngFor` (with `trackBy`) are used.
   - **Restricted Service Imports**:
     - Ensure service files are imported via the `@services/*` alias.
     - Prohibit root `@services` imports (use precise paths like `@services/state/custom-view/custom-view.service`).
     - Prohibit relative service imports (like `../services/...` or `./services/...`).

3. **Styling & Layout (SCSS)**:
   - Ensure `@import` is used for partial styles (not `@use`).
   - Validate that variables from `scss/helpers/variable` or CSS variables are used rather than hardcoded hex colors or fonts.
   - Prohibit inline CSS styling.
   - Verify layout is responsive on smaller screens.

4. **Security & Secrets**:
   - Confirm no passwords, client secrets, API tokens, or credentials are hardcoded.

5. **Performance & Cleanliness**:
   - Ensure feature modules are lazy loaded in `app-routing.module.ts` via `loadChildren`.
   - Check for and clean up unused imports, variables, console logs, or commented out blocks of code.
   - Confirm that types are explicit (avoid `any`) and formatting/linting expectations are met.

---

## Review Deliverables & Summary

When preparing the review or the Pull Request, summarize:
- **Risks & Impact**: Potential side-effects on other modules or layout rendering.
- **Required Follow-ups**: Any post-merge actions or technical debt items identified.
- **Validation Evidence**: Proof that unit tests pass, compilation succeeds, and local verification was successful.
