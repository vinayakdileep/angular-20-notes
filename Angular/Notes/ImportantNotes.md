# Angular: `ng-template` vs `ng-container` vs `ng-content`

## `ng-template`
- Used to define a **template that is not rendered immediately**.
- The content inside `<ng-template>` is rendered **only when Angular is instructed to do so** (e.g., using `*ngIf`, `*ngTemplateOutlet`).
- Commonly used with `ng-container` or template references (`#templateRef`).
- Does **not** create an element in the DOM.

### Example
```html
<ng-template #loading>
  <p>Loading...</p>
</ng-template>

<div *ngIf="isLoaded; else loading">
  Data loaded successfully.
</div>
```

---

## `ng-container`
- A **logical container** used to group elements without adding an extra element to the DOM.
- Helps apply structural directives like `*ngIf`, `*ngFor`, etc., without introducing unnecessary `<div>` or `<span>` tags.
- Useful when you want to avoid cluttering the DOM.

### Example
```html
<ng-container *ngIf="isLoggedIn">
  <h2>Welcome</h2>
  <p>User Dashboard</p>
</ng-container>
```

---

## `ng-content`
- Used for **content projection** (passing HTML from a parent component to a child component).
- The parent places content between the child component's tags.
- The child component uses `<ng-content>` to display that content.
- Supports **multiple projection slots** using the `select` attribute.

### Child Component
```html
<div class="card">
  <ng-content select=".header"></ng-content>
  <ng-content></ng-content>
</div>
```

### Parent Component
```html
<app-card>
  <h2 class="header">Employee Details</h2>
  <p>This content is projected into the child component.</p>
</app-card>
```

---

# Quick Interview Revision

| Feature | `ng-template` | `ng-container` | `ng-content` |
|---------|---------------|----------------|--------------|
| Adds element to DOM? | ❌ No | ❌ No | ✅ Renders projected content |
| Purpose | Define reusable or conditional template | Group elements without creating a DOM node | Project content from parent to child |
| Common Use | `*ngIf`, `*ngTemplateOutlet` | `*ngIf`, `*ngFor` without extra `<div>` | Component content projection |
| Parent → Child Content | ❌ No | ❌ No | ✅ Yes |

## Easy way to remember

- **`ng-template`** → **Store** HTML for later use.
- **`ng-container`** → **Group** elements without creating a DOM element.
- **`ng-content`** → **Project** content from the parent component into the child component.