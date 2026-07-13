---
name: unit-test
description: Dedicated workflow for planning Jest test cases, mocking Angular 15 dependencies, and executing/validating tests in the Horizon 2 UI project.
---

# Workflow: Unit Test

This document outlines the systematic flow for writing, updating, and executing unit tests in the Horizon 2 UI application.

---

## Required Steps & Phases

1. **Understanding & Scope**:
   - Identify the source file (`*.ts`) that needs test coverage and identify the behavior to verify at the lowest relevant level of the stack.
   - Locate its corresponding spec file (`*.spec.ts`). If the spec file does not exist, create a new one next to the source file.
   - Analyze the target code's dependencies (e.g., HTTP clients, custom services, activated routes, or NgRx Store) to determine what must be mocked.

2. **Planning & Mocking**:
   - Plan the testing scenarios, covering:
     - **Happy paths**: Successful responses, standard renders, expected UI actions.
     - **Edge cases**: Empty lists, null or undefined inputs, extreme pagination values.
     - **Error flows**: HTTP error handling, failed actions, toast reminders.
   - **Assertion Strategy**: Prefer real component/service behavior over mock-only assertions where possible. Keep Angular 15 and RxJS patterns consistent with the existing test setup.
   - Determine setup dependencies, importing test modules such as `HttpClientTestingModule` or using `jasmine.spyOn` / `jest.spyOn` as appropriate.

3. **Execution**:
   - Write the tests inside the `*.spec.ts` using `TestBed.configureTestingModule`.
   - Setup mocks for services and state providers (like `provideMockStore` for NgRx store mocks).
   - Use standard Angular test practices:
     ```typescript
     fixture = TestBed.createComponent(MyComponent);
     component = fixture.componentInstance;
     fixture.detectChanges();
     ```

4. **Formatting & Quality**:
   - Follow the [Post Code Change Skill](../post-code-change/SKILL.md) for canonical formatting, linting, and formatting checks.

5. **Validation**:
   - Run the relevant Jest suite and capture the result:
     ```bash
     npm run test
     ```
   - Ensure the tests execute cleanly without memory leaks or uncaught async processes.

6. **Report**:
   - Generate and complete report artifacts using the [Report Generation Skill](../report-generation/SKILL.md) and finalize in Phase 4 (`.github/hooks/phase-4-validation-pr.hook.md`).
