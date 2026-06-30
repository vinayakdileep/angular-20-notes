# Angular Routing - Part 1 (Routing Fundamentals)

---

# Routing

## What is Routing?

Routing is the mechanism that allows navigation between different components (views) in an Angular application without reloading the entire page.

Angular is a **Single Page Application (SPA)**, so only the required component is loaded while the application state remains intact.

---

## Creating Routes

Routes are defined using the `Routes` array.

Each route object usually contains:

- `path`
- `component`
- `redirectTo`
- `children`
- `canActivate`
- `canDeactivate`
- `resolve`
- `data`
- etc.

### Example

```ts
export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'contact', component: ContactComponent }
];
```

---

## Registering Routes

### Standalone Applications (Angular 15+)

```ts
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes)
  ]
});
```

### NgModule Applications (Older Projects)

```ts
@NgModule({
  imports: [
    RouterModule.forRoot(routes)
  ]
})
export class AppModule {}
```

> **Interview Note**
>
> - Angular 15+ applications generally use **provideRouter()**.
> - `RouterModule.forRoot()` is used in module-based applications.

---

## router-outlet

`<router-outlet>` is a placeholder where Angular loads the component associated with the current route.

```html
<router-outlet></router-outlet>
```

Whenever the URL changes, Angular replaces the component inside this outlet.

---

# routerLink

## Why not use href?

Although `href` can navigate to another page, it performs a **full page refresh**.

Problems with `href`

- Reloads the entire application
- Reloads JavaScript, CSS and other resources
- Loses application state
- Slower navigation

Since Angular is a **Single Page Application**, we use **routerLink**.

Benefits of `routerLink`

- No page refresh
- Preserves application state
- Faster navigation
- Uses Angular Router

---

## What is routerLink?

`routerLink` is an **attribute directive** used to navigate to another route.

It binds an HTML element to an Angular route.

---

## Syntax

### Static Route

```html
<a routerLink="/Home">Home</a>
```

---

### Property Binding

```html
<a [routerLink]="['/Home']">Home</a>
```

---

### Dynamic Route

```html
<button [routerLink]="['/Courses', course.id]">
    View Details
</button>
```

Navigates to

```
/Courses/10
```

if

```ts
course.id = 10;
```

---

## Interview Explanation

> routerLink is an attribute directive that performs client-side navigation using Angular Router without refreshing the browser. It improves performance by preserving the application state and loading only the required component.

---

# routerLinkActive

## What is it?

`routerLinkActive` is an attribute directive used to apply one or more CSS classes when the associated route is active.

Most commonly used for navigation menus.

---

## Example

```html
<a
    routerLink="/Home"
    routerLinkActive="active-menu">
    Home
</a>
```

When `/Home` is active,

```css
.active-menu{
    color: blue;
    font-weight: bold;
}
```

is automatically applied.

---

## Child Route Issue

Suppose the routes are

```
/Courses
/Courses/Popular
/Courses/Checkout
```

If the current URL is

```
/Courses/Popular
```

Angular considers **Courses** as active because it is a parent route.

So both

```
Courses
Popular
```

may receive the active class.

---

## Exact Route Matching

To activate the class only when the exact route matches,

```html
<a
    routerLink="/Courses"
    routerLinkActive="active-menu"
    [routerLinkActiveOptions]="{ exact: true }">
    Courses
</a>
```

---

## Interview Tip

Without

```html
[routerLinkActiveOptions]="{ exact: true }"
```

parent routes also become active whenever one of their child routes is active.

---

## Important

If you're using standalone components, import the directives.

```ts
imports: [
    RouterLink,
    RouterLinkActive
]
```

For NgModule applications,

```ts
RouterModule
```

must be imported.

---

# Absolute vs Relative Navigation

## Absolute Path

Starts with `/`

Navigation always starts from the application's root.

```html
<a routerLink="/Courses">
```

Current URL

```
/Home/About
```

Result

```
/Courses
```

---

## Relative Path

Does **not** start with `/`.

Navigation starts relative to the currently active route.

Current URL

```
/Courses
```

```html
<a routerLink="Popular">
```

Result

```
/Courses/Popular
```

---

## ../ (Move One Level Up)

Current URL

```
/Home/Products
```

```html
<a routerLink="../About">
```

Result

```
/Home/About
```

Each

```
../
```

moves one level up.

---

## Memory Trick

```
/        -> Root

No /     -> Current Route

../      -> Parent Route
```

---

# Programmatic Navigation

Instead of using `routerLink`, navigation can also be performed from TypeScript.

Inject Router

```ts
router = inject(Router);
```

---

## navigate()

Accepts URL segments as an array.

```ts
this.router.navigate(['/Courses']);
```

---

### Passing Route Parameters

```ts
this.router.navigate(['/Courses', 10]);
```

Navigates to

```
/Courses/10
```

---

### Passing Query Parameters

```ts
this.router.navigate(
    ['/Courses'],
    {
        queryParams: {
            search: 'Angular'
        }
    }
);
```

Result

```
/Courses?search=Angular
```

---

## navigateByUrl()

Accepts the complete URL as a string.

```ts
this.router.navigateByUrl('/Courses');
```

---

## Relative Navigation

To navigate relative to the current route, use `relativeTo`.

```ts
this.router.navigate(
    ['Popular'],
    {
        relativeTo: this.activatedRoute
    }
);
```

Current URL

```
/Courses
```

Result

```
/Courses/Popular
```

---

## navigate() vs navigateByUrl()

| navigate() | navigateByUrl() |
|------------|-----------------|
| Accepts URL segments as an array | Accepts complete URL string |
| Easier for route parameters | Good when full URL is already available |
| Supports `relativeTo` | Does not support URL segments |

---

# Common Interview Questions

### Why should we use routerLink instead of href?

Because `routerLink` performs client-side navigation without refreshing the page, preserving the application state.

---

### Difference between navigate() and navigateByUrl()?

- `navigate()` accepts URL segments as an array and supports relative navigation.
- `navigateByUrl()` accepts a complete URL string.

---

### Difference between Absolute and Relative Path?

Absolute path starts from the application's root (`/`).

Relative path starts from the currently active route.

---

### What is router-outlet?

A placeholder where Angular dynamically loads the component associated with the current route.

---

### What is routerLinkActive?

A directive used to add or remove CSS classes depending on whether a route is currently active.

---

# Common Mistakes

❌ Using `href` instead of `routerLink`

❌ Forgetting to import `RouterLink` and `RouterLinkActive` in standalone components

❌ Forgetting `exact: true` causing parent links to remain active

❌ Assuming `navigate()` is always absolute (it becomes relative when `relativeTo` is used)

---

# Quick Revision

- Routing allows navigation between components without page reload.
- `Routes` array defines application routes.
- `provideRouter()` → Standalone apps.
- `RouterModule.forRoot()` → NgModule apps.
- `router-outlet` loads the routed component.
- `routerLink` performs SPA navigation.
- `routerLinkActive` highlights the active route.
- `exact: true` prevents parent links from being highlighted.
- `/` → Root route.
- No `/` → Relative route.
- `../` → Parent route.
- `navigate()` → URL segments (array).
- `navigateByUrl()` → Complete URL (string).
- `relativeTo` enables relative programmatic navigation.