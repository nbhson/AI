---
name: styling-standards
description: Guidelines for modular SCSS structures, order of imports, CSS variables, and responsive grid layouts in the Horizon 2 UI project.
---

# Styling Standards & SCSS Guidelines

This skill enforces strict Sass styling practices to keep styles small, clean, and DRY.

---

## 1. Modular SCSS Structure & Variables

All styling should reuse configurations and partials:

- **Variables Sheet**: Reference variables defined in `scss/helpers/variable` (such as colors, fonts, margins) rather than inlining values.
- **Mixins**: Reuse core mixins or animations. Do not write duplicate styling rules for cards, shimmers, buttons, or scrollbars.
- **Material Theme**: Leverage Angular Material component overrides rather than writing ad-hoc overrides.
- **Gradients & Custom Colors**: If styling specific items, verify if a theme variable exists (e.g. `$primary-color`, `$secondary-color`).

---

## 2. Order of Imports

- **At the Top**: All `@import` lines must be at the absolute top of the stylesheet.
- **Paths**: Keep imports relative or point to standard locations:
  ```scss
  @import 'scss/helpers/variable';
  @import 'scss/helpers/common';
  ```

---

## 3. Responsive Layouts

Use CSS grids, flexbox, and media queries consistently:

```scss
.tracker-main-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1.5rem;

  @media (max-width: 992px) {
    grid-template-columns: 1fr;
  }
}
```

Keep sidebars, dialog headers, or lists responsive:

```scss
.sidebar-panel {
  position: sticky;
  top: 1rem;

  @media (max-width: 992px) {
    position: static;
  }
}
```
