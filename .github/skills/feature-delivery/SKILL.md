---
name: feature-delivery
description: Step-by-step workflow for scoping, designing, building component logic, writing unit tests, and delivering new features in the Horizon 2 UI project.
---

# Workflow: Feature Delivery

This document outlines the workflow for developing and delivering new features in the Horizon 2 UI application, keeping them aligned with architecture and code standards.

---

## Required Steps & Phases

1. **Discovery & Setup**: Clarify scope, acceptance criteria, and affected modules. Map out the layout, modules involved, and identify any core dependencies, shared services, constants, models, and components that can be reused before adding new implementation.
2. **Plan & Get Approval**: Write an implementation plan in `implementation_plan.md` using the Technical Design template (`.github/report-templates/technical-design.md`). Wait for developer approval before coding if the task is non-trivial.
3. **Develop Components & Logic**:
   - Write NgModule-based components following Angular 15 guidelines (no standalone components).
   - Separate container/orchestrator responsibilities from UI presenter components.
   - Use standard styles, variables, and common helper layouts.
4. **Data Flows**: Integrate state logic using RxJS (observables, behavior subjects) and NgRx where application-wide state is needed.
5. **Write Unit Tests**: Write or update unit tests inside the corresponding `*.spec.ts` files to test component renderings, data logic, and event flows.
6. **Formatting & Quality Checks**: Follow the [Post Code Change Skill](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/skills/post-code-change/SKILL.md) for canonical formatting, lint/format, and cleanup steps.
7. **Validation**:
   - Run unit tests to verify correctness (`npm run test`).
   - Run production compilation check (`npm run build`).
   - Verify layout and logs in the local browser (`npm run startdev`).
8. **Report & Submit**:
   - Create a draft report via the [Report Generation Skill](file:///d:/bitbucket/horizon2/horizon2-ui/ClientApp/.github/skills/report-generation/SKILL.md).
   - Finalize validation and PR readiness via Phase 4 (`.github/hooks/phase-4-validation-pr.hook.md`).

---

## Deliverables

- **Technical Design Summary**: Outlining architecture, modules, and component split.
- **Implementation**: Aligned with the project structure and styling rules.
- **Validation Evidence**: Proof of passing tests and production build checks.
