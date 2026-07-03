---
name: angular-architecture
description: Guidelines for Container vs. Presentation architecture, component splitting, and RxJS/NgRx asynchronous data loading in Angular 15.
---

# Angular Architecture & Component Blueprint

This skill guides on how to structure, decompose, and manage Angular components and data flows in this repository.

---

## 1. Container vs. Presentation Component Blueprint

To avoid bloated templates and hard-to-maintain files, decompose components when they grow large or handle complex layouts:

### A. Parent (Container / Orchestrator)

- **Role**: Fetch data, integrate with NgRx store, inject services, manage routing, coordinate submissions, handle dialog boxes, and hold states.
- **Example Template**:
  ```html
  <div class="tracker-main-container">
    <app-tracker-overview-table [data]="trackerData" (rowClicked)="onRowSelect($event)"></app-tracker-overview-table>
    <app-pagination [total]="totalCount" (pageChange)="onPageChange($event)"></app-pagination>
  </div>
  ```

### B. Children (Presenters / Stateless components)

- **Folder Location**: Nested inside a `components/` subdirectory under the parent component directory or inside `src/shared/components/`.
- **Role**: Strictly render UI.
- **Rules**:
  - Accept state via classic `@Input()` decorators.
  - Emit actions via `@Output() outputEvent = new EventEmitter<T>()`.
  - Do NOT inject services directly inside presenters. Let the parent component handle operations.
  - Always implement `changeDetection: ChangeDetectionStrategy.OnPush`.

---

## 2. Reactivity & Async Data Blueprint

Do NOT use Angular Signals or `resource()`. Always write data loading and reactivity using RxJS Observables and classical Angular patterns:

- **Fetching Data**:

  ```typescript
  // In Service:
  getData(params: PaginationParams): Observable<PaginationResultModel<DataDTO>> {
    return this.apiService.get(params);
  }

  // In Component:
  loadData() {
    this.isLoading = true;
    this.dataService.getData(this.params)
      .pipe(
        takeUntil(this.destroy$),
        finalize(() => this.isLoading = false)
      )
      .subscribe({
        next: (res) => this.items = res.content,
        error: (err) => this.toastr.error('Failed to load data')
      });
  }
  ```

- **Derived State**: Use RxJS `.pipe(map(...))` rather than manual recalculation inside templates to ensure efficiency and clean derived data.

---

## 3. Template Code Blueprint

Use classical Angular 15 template directives:

- Always use `*ngIf`, `*ngFor`, and `*ngSwitch` (imported via `CommonModule` or `SharedModule`).
- Always specify `trackBy` for loops to prevent redundant DOM re-rendering:
  ```html
  <div *ngIf="!isLoading; else loadingTpl">
    <div *ngFor="let item of items; trackBy: trackById" class="item-card">
      <h4>{{ item.title }}</h4>
    </div>
  </div>
  <ng-template #loadingTpl>
    <div class="shimmer-loader">Loading...</div>
  </ng-template>
  ```
