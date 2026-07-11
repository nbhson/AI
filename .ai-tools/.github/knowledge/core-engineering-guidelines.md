# Core Engineering Knowledge

This document contains the repository-wide technical guidance previously embedded in `.github/copilot-instructions.md` (Sections 1-6).

---

## 1. Tech Stack & Angular 15 Architecture

We use **Angular 15.2.1** for development. Copilot must adhere to classical Angular 15 patterns:

- **Module-Based Architecture**:
  - Do NOT assume Standalone Components. Components must be declared in an `NgModule` (typically `SharedModule` or feature modules like `EmbeddedModule`, `TrackerModule`, `AdminModule`).
  - To add a new component, also register it in its corresponding module's `declarations` and `exports` arrays (if reusable).
- **Change Detection**:
  - We use standard Zone.js change detection.
  - Implement `changeDetection: ChangeDetectionStrategy.OnPush` where performance is critical and inputs are immutable.
- **Reactivity & State Management**:
  - Do NOT use Angular Signals, computed signals, or the `resource()` API (these are not available in Angular 15).
  - Use RxJS `Observable`, `Subject`, `BehaviorSubject`, and standard NgRx Store/Effects for state management.
  - Correctly clean up subscriptions (using the `takeUntil` pattern with a destroyer subject or the `async` pipe in templates) to prevent memory leaks.
- **Lazy Loading**:
  - Lazy-load routes using classic module loading (e.g., `loadChildren: () => import('./tracker/tracker.module').then((m) => m.TrackerModule)`).
- **Control Flow**:
  - Do NOT use `@if`, `@for`, or `@switch` (they are not supported in Angular 15).
  - Use `*ngIf`, `*ngFor` (always specify `trackBy` for collections), and `*ngSwitch` directives instead. Make sure `CommonModule` is imported in modules using these directives.

---

## 2. Directory Structure & Naming Conventions

Maintain consistency with our directory structure:

- **Suffixes**:
  - Components: Class names must end with `Component` and files must use the `.component.ts` suffix (e.g., `home-page.component.ts` and `HomePageComponent`).
  - Services: Class names must end with `Service` and files must use the `.service.ts` suffix (e.g., `custom-view.service.ts` and `CustomViewService`).
  - Modules: Class names must end with `Module` and files must use the `.module.ts` suffix (e.g., `share-module.module.ts` and `SharedModule`).
- **Core Folder Organization**:
  - **Shared Assets**: `src/shared/` contains global components, decorators, directives, DTOs, guards, material configurations, and pipes.
  - **Constants**: `src/constants/` contains global constants (`constants.ts`, `enum.ts`, `messages.ts`, etc.).
  - **Models**: `src/models/` contains global interface types (e.g., `table.model.ts`).
  - **SCSS**: `src/scss/` contains global stylesheets split into `base/`, `components/`, `helpers/`, and `pages/`.
- **Path Aliases**:
  - Use `@services/*` to refer to service files (mapped to `src/app/services/*`).
  - **ESLint Restricted Imports**:
    - Do NOT import directly from the root `@services` barrel. Always use specific category subfolders (e.g., `@services/state/custom-view/custom-view.service`).
    - Do NOT use relative paths (like `../services/...` or `./services/...`) to import services; you must use the `@services/*` alias.
  - Use relative pathing or direct src paths (like `src/shared/components/...`) for other shared elements.

---

## 3. Styling & SCSS Rules

- **Import Methods**:
  - We use classic `@import` syntax instead of the newer Dart Sass `@use` syntax for importing stylesheets and partials in this project.
  - All `@import` statements must appear at the absolute top of the SCSS stylesheet before any CSS rules, selectors, or variables are declared.
  - Component-specific styles are linked via `styleUrls` in component decorators.
- **Style Component Scoping**:
  - Component-specific styling must be kept in the component's SCSS file (e.g., `my-component.component.scss`).
  - Use component selectors or sub-selectors to avoid style pollution. Avoid styling global elements directly from component-level stylesheets.
- **Reuse Style Variables**:
  - Always use design system variables (e.g., variables defined in `src/scss/helpers/variable`) instead of hardcoding hex values.
  - Do not duplicate common layout properties; leverage existing Material styles or Bootstrap utility classes.

---

## 4. Safety & Authorization Interceptions

- **Authentication & MSAL**:
  - We use `@azure/msal-angular` for authentication.
  - Protect routes with the `canActivate: MAIN_ROLE_GUARDS` guard configuration.
- **Global Constants**:
  - Maintain all constant values (APIs, formats, regexes) in their designated files in `src/constants/` rather than inlining values.

---

## 5. Workflows & Commands

Follow these terminal commands when building, testing, or formatting:

- **Local Dev Server**: `npm run startdev` (runs `ng serve` with increased memory limit)
- **Format Code**: `npm run format` (uses Prettier)
- **Code Linting**: `npm run lint` (uses ESLint with fix option)
- **Run Unit Tests**: `npm run test` (uses Jest)
- **Production Build**: `npm run build` or `npm run build-prod`

---

## 6. Code Style & Quality Checklist

Before completing changes, ensure Copilot fulfills this quality checklist:

1. **Types**: Strictly avoid using `any` types. Provide interface declarations or models in `src/models/`.
2. **Reusability**: Search the project first. If a service, utility, or constant already exists in `src/constants/` or `src/shared/`, reuse it.
3. **No Unused Code**: Clean up unused imports, variables, and commented out sections before compiling.
4. **Validation**: Run `npm run lint` and `npm run format` on modified files.
