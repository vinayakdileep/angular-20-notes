# Custom Attribute Directives in Angular

## What is a Custom Attribute Directive?

- A **custom attribute directive** is used to **change the appearance or behavior of an existing DOM element**.
- Unlike structural directives, it **does not add or remove elements from the DOM**. Instead, it modifies the element on which it is applied.
- A directive is applied by adding its **selector** as an attribute to an HTML element.

Example:

```html
<p appHighlight>Highlighted Text</p>
```

Angular creates an instance of the directive and associates it with the `<p>` element.

## Accessing the Host Element

Angular can inject the following objects into the directive constructor:

### `ElementRef`

- `ElementRef` provides a reference to the **host DOM element**.
- It allows direct access to the native DOM element.

```typescript
constructor(private elementRef: ElementRef) {}
```

> **Note:** Direct DOM manipulation using `ElementRef.nativeElement` is generally discouraged because it can cause security issues and reduces platform independence.

### `Renderer2`

- `Renderer2` is the recommended way to modify the DOM.
- It provides methods to safely change styles, classes, attributes, and properties without directly accessing the native element.

```typescript
constructor(
  private elementRef: ElementRef,
  private renderer: Renderer2
) {}
```

Example:

```typescript
this.renderer.setStyle(
  this.elementRef.nativeElement,
  'backgroundColor',
  'yellow'
);
```

## `@HostListener`

- `@HostListener` is used to listen for events on the **host element**.
- It is similar to event binding (`(click)`, `(mouseenter)`, etc.) used in components.

Example:

```typescript
@HostListener('mouseenter')
onMouseEnter() {
  // Handle mouse enter
}

@HostListener('mouseleave')
onMouseLeave() {
  // Handle mouse leave
}
```

## `@HostBinding`

- `@HostBinding` binds a property of the directive to a property or attribute of the **host element**.
- It is similar to property binding (`[style]`, `[class]`, etc.) in templates.

Example:

```typescript
@HostBinding('style.backgroundColor')
backgroundColor = 'yellow';
```

Whenever `backgroundColor` changes, the host element's background color is updated automatically.

## Passing Values from HTML to a Directive

Values can be passed from the template using an `@Input()` property.

Example:

```html
<p [appHighlight]="'yellow'">Text</p>
```

```typescript
@Input()
set appHighlight(color: string) {
  // Use the passed value
}
```

Using a **setter** allows custom logic to run whenever the input value changes.

### Using the Directive Selector as the Input Name

To avoid adding an extra input property name, the `@Input()` property is often given the **same name as the directive selector**.

Example:

```typescript
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {

  @Input()
  set appHighlight(color: string) {
    // Handle value
  }
}
```

Usage:

```html
<p [appHighlight]="'red'">Hello</p>
```

This is cleaner than having separate selector and input names.

---

# Structural (Conditional) Directives

## What are Structural Directives?

- Structural directives **change the structure of the DOM** by adding or removing elements.
- They are commonly written with the `*` (asterisk) syntax.

Examples:

```html
<div *ngIf="isVisible">Content</div>
<li *ngFor="let item of items">{{ item }}</li>
```

## How `*` Works Internally

The `*` syntax is syntactic sugar.

For example:

```html
<div *ngIf="isVisible">
  Hello
</div>
```

Angular internally transforms it into:

```html
<ng-template [ngIf]="isVisible">
  <div>Hello</div>
</ng-template>
```

- `ng-template` itself **does not render any visible DOM**.
- Angular creates or removes the embedded view based on the directive logic.

## `TemplateRef` and `ViewContainerRef`

Custom structural directives typically inject:

```typescript
constructor(
  private templateRef: TemplateRef<any>,
  private viewContainer: ViewContainerRef
) {}
```

### `TemplateRef`

- Represents the **template inside the `<ng-template>`**.
- In the `*` syntax, it contains the host element and its children after Angular rewrites the template.

### `ViewContainerRef`

- Represents the **container where embedded views are inserted or removed**.
- It provides methods such as:
  - `createEmbeddedView()`
  - `clear()`
  - `remove()`

Example:

```typescript
if (condition) {
  this.viewContainer.createEmbeddedView(this.templateRef);
} else {
  this.viewContainer.clear();
}
```

## Creating a Custom `*ngIf`-like Directive

A structural directive can show or hide content by controlling the view container:

```typescript
@Directive({
  selector: '[appIf]'
})
export class AppIfDirective {

  @Input()
  set appIf(condition: boolean) {
    this.viewContainer.clear();

    if (condition) {
      this.viewContainer.createEmbeddedView(this.templateRef);
    }
  }

  constructor(
    private templateRef: TemplateRef<any>,
    private viewContainer: ViewContainerRef
  ) {}
}
```

Usage:

```html
<div *appIf="isLoggedIn">
  Welcome!
</div>
```

## Important Notes

- **Attribute directives** modify the behavior or appearance of existing elements but **do not change the DOM structure**.
- **Structural directives** add or remove elements from the DOM and are typically used with the `*` syntax.
- `ElementRef` provides direct access to the native element, but **`Renderer2` is the preferred way** to manipulate the DOM safely.
- `@HostListener` is used to listen to events on the host element.
- `@HostBinding` binds directive properties to host element properties, styles, classes, or attributes.
- `@Input()` (or an `@Input()` setter) allows values to be passed from the template into the directive.
- `TemplateRef` represents the template to be rendered, while `ViewContainerRef` controls where and when that template is inserted into the DOM.