# Angular: `@ViewChild` vs `@ViewChildren` vs `@ContentChild` vs `@ContentChildren`

## `@ViewChild`
- Used to get a **single child component, directive, or DOM element** from the component's own template.
- Returns the **first matching element**.
- Accessed after the view is initialized (`ngAfterViewInit`).
- Commonly used to call methods on a child component or access an element.

### Example

```ts
@ViewChild(ProductListComponent)
productList!: ProductListComponent;

ngAfterViewInit() {
  this.productList.loadProducts();
}
```

---

## `@ViewChildren`
- Used to get **multiple child components, directives, or DOM elements** from the component's own template.
- Returns a `QueryList<T>`.
- Accessed after the view is initialized (`ngAfterViewInit`).
- Useful when multiple child elements/components exist.

### Example

```ts
@ViewChildren(ProductComponent)
products!: QueryList<ProductComponent>;

ngAfterViewInit() {
  console.log(this.products.length);
}
```

---

## `@ContentChild`
- Used to get a **single projected element or component** passed from the **parent** using `<ng-content>`.
- Returns the **first matching projected element**.
- Accessed after the content is projected (`ngAfterContentInit`).
- Commonly used in reusable components that support content projection.

### Child Component

```html
<ng-content></ng-content>
```

```ts
@ContentChild('header')
header!: ElementRef;

ngAfterContentInit() {
  console.log(this.header);
}
```

### Parent Component

```html
<app-card>
  <h2 #header>Employee Details</h2>
</app-card>
```

---

## `@ContentChildren`
- Used to get **multiple projected elements or components** passed from the parent using `<ng-content>`.
- Returns a `QueryList<T>`.
- Accessed after the content is projected (`ngAfterContentInit`).
- Useful when multiple projected items need to be processed.

### Example

```ts
@ContentChildren(ItemDirective)
items!: QueryList<ItemDirective>;

ngAfterContentInit() {
  console.log(this.items.length);
}
```

---

# Quick Interview Revision

| Decorator | Searches In | Single / Multiple | Returns | Lifecycle Hook |
|-----------|-------------|-------------------|----------|----------------|
| `@ViewChild` | Component's own view (template) | Single | Component, Directive, or ElementRef | `ngAfterViewInit` |
| `@ViewChildren` | Component's own view (template) | Multiple | `QueryList<T>` | `ngAfterViewInit` |
| `@ContentChild` | Projected content (`<ng-content>`) | Single | Component, Directive, or ElementRef | `ngAfterContentInit` |
| `@ContentChildren` | Projected content (`<ng-content>`) | Multiple | `QueryList<T>` | `ngAfterContentInit` |

---

# Easy way to remember

- **`@ViewChild`** → Get **one** child from **my own template**.
- **`@ViewChildren`** → Get **all** matching children from **my own template**.
- **`@ContentChild`** → Get **one** item **projected by the parent** through `<ng-content>`.
- **`@ContentChildren`** → Get **all** projected items **provided by the parent** through `<ng-content>`.

## Memory Trick

- **View** → Look **inside my own component's HTML**.
- **Content** → Look at **what the parent passed into me** via `<ng-content>`.
- **Child** → **One** match.
- **Children** → **Multiple** matches.