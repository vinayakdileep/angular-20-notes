# Angular Routing - Part 3 (Route Guards)

---

# Route Guards

## What are Route Guards?

Route Guards are used to **control navigation** between routes.

They allow us to decide whether a user:

- Can enter a route.
- Can leave a route.
- Can access child routes.
- Can load a lazy-loaded module.
- Should wait until data is loaded before navigating.

Route Guards improve:

- Security
- User Experience
- Navigation Control

---

# Types of Route Guards

| Guard | Purpose |
|--------|---------|
| `canActivate` | Controls whether a route can be activated. |
| `canActivateChild` | Controls access to child routes. |
| `canDeactivate` | Controls whether the user can leave the current route. |
| `resolve` | Loads required data before the component is created. |
| `canMatch` | Determines whether a route should match the current URL (replaces most `canLoad` use cases in newer Angular versions). |

> **Interview Tip**
>
> In Angular 15+, **functional guards** are commonly used instead of class-based guards.

---

# canActivate

## What is canActivate?

`canActivate` determines whether Angular should allow navigation to a route.

It is commonly used for:

- Authentication
- Authorization
- Subscription validation
- Role-based access

If the guard returns:

- `true` → Navigation continues.
- `false` → Navigation is cancelled.
- `UrlTree` → Angular redirects to another route.

---

## Route Configuration

```ts
export const routes: Routes = [

  {
    path: 'Courses',
    children: [

      {
        path: 'checkout',
        component: CheckoutComponent,
        canActivate: [AuthCheckout]
      }

    ]
  }

];
```

---

## Functional Guard

```ts
import { inject } from '@angular/core';
import { Router } from '@angular/router';

export const AuthCheckout = () => {

    const authService = inject(AuthService);
    const router = inject(Router);

    if (authService.isLoggedIn) {
        return true;
    }

    return router.createUrlTree(['/login']);

};
```

> **Best Practice**
>
> Prefer returning a `UrlTree` using `router.createUrlTree()` instead of calling `router.navigate()` inside a guard. This lets the router handle the redirect cleanly.

---

## Real-time Example

```
Checkout Page

↓

User clicks Checkout

↓

Logged In ?

↓

Yes → Checkout

No → Login Page
```

---

## Interview Tip

`canActivate` protects **only the route on which it is applied**.

Child routes require either:

- Their own `canActivate`
- Or a parent `canActivateChild`

---

# canActivateChild

## What is canActivateChild?

`canActivateChild` protects **only the child routes** of a parent route.

The parent route itself remains accessible.

---

## Route Configuration

```ts
export const routes: Routes = [

  {
    path: 'Courses',
    component: CoursesComponent,

    canActivateChild: [authCoursesChildren],

    children: [

      {
        path: 'popular',
        component: PopularComponent
      },

      {
        path: 'checkout',
        component: CheckoutComponent
      },

      {
        path: ':id',
        component: CourseDetailComponent
      }

    ]

  }

];
```

---

## Real-time Example

```
Courses

↓

Accessible

↓

Popular

↓

Guard Executes

↓

Checkout

↓

Guard Executes
```

---

## Interview Tip

Suppose

```
/Courses
```

is accessible to everyone.

But

```
/Courses/checkout
/Courses/popular
```

should be accessible only after login.

In that case use **canActivateChild**.

---

## canActivate vs canActivateChild

| canActivate | canActivateChild |
|-------------|------------------|
| Protects one route | Protects all child routes |
| Executes before activating the current route | Executes before activating child routes |
| Does not automatically protect child routes | Protects every child route |

---

# canDeactivate

## What is canDeactivate?

`canDeactivate` decides whether a user can leave the current page.

It is commonly used when there are unsaved changes.

---

## Real-time Examples

- Registration Form
- Employee Edit Screen
- Profile Update
- Product Edit
- Payment Page

---

## Route Configuration

```ts
{
    path: 'Contact',
    component: ContactComponent,
    canDeactivate: [deactivateCheckout]
}
```

---

## Guard

```ts
export const deactivateCheckout = (
    component: ContactComponent
): boolean => {

    if (
        component.canExit &&
        !component.canExit()
    ) {

        return confirm(
            'You have unsaved changes. Leave anyway?'
        );

    }

    return true;

};
```

---

## Component

```ts
export class ContactComponent {

    hasUnsavedChanges = true;

    canExit(): boolean {
        return !this.hasUnsavedChanges;
    }

}
```

---

## Generic Solution (Recommended)

Instead of depending on a specific component, create an interface.

```ts
export interface CanDeactivateComponent {

    canExit(): boolean;

}
```

Component

```ts
export class ContactComponent
implements CanDeactivateComponent {

    canExit(): boolean {

        return true;

    }

}
```

Guard

```ts
export const canDeactivateGuard = (
    component: CanDeactivateComponent
) => {

    return component.canExit();

};
```

Now the same guard can be reused for multiple components.

---

## Important Notes

- Executes **only when leaving a route**.
- Does **not** execute while entering the route.
- Angular passes the current component instance to the guard.

---

## Common Mistakes

❌ Expecting `canDeactivate` to execute when entering a page.

❌ Using `router.navigate()` instead of returning a `UrlTree` (when redirecting).

❌ Forgetting to implement the interface for reusable guards.

---

# Resolve

## What is Resolve?

A Resolver loads the required data **before** Angular creates the component.

The component is displayed only after the resolver finishes.

---

## Why use Resolve?

Suppose

```
Employee List
```

takes 5 seconds to load.

Without Resolve

```
Component Opens

↓

Loader Appears

↓

API Executes

↓

Data Appears
```

With Resolve

```
API Executes

↓

Data Loaded

↓

Component Opens with Data
```

The user sees a fully populated page.

---

## Route Configuration

```ts
{
    path: 'Courses',
    component: CoursesComponent,

    resolve: {
        courses: resolveCourses
    }
}
```

Here,

```
courses
```

is the key used to access the resolved data.

---

## Resolver

```ts
import { ResolveFn } from '@angular/router';

export const resolveCourses: ResolveFn<Course[]> = () => {

    const courseService = inject(CourseService);

    return courseService.getAllcourses();

};
```

> **Important**
>
> Do **not** call `subscribe()` inside a resolver. Return the `Observable` (or `Promise`) directly. Angular subscribes automatically.

---

## Reading Resolved Data

### Using Snapshot

```ts
ngOnInit() {

    const allCourses =
        this.activatedRoute.snapshot.data['courses'];

}
```

---

### Using data Observable (Recommended)

```ts
ngOnInit() {

    this.activatedRoute.data.subscribe(data => {

        this.AllCourses = data['courses'];

    });

}
```

If you're also filtering with query parameters, you can combine the resolved data with `queryParamMap` as needed.

---

## Real-time Uses

- Employee List
- Product List
- Dashboard
- User Profile
- Course Catalog

---

# Summary of Route Guards

| Guard | Executes When | Typical Use |
|--------|---------------|-------------|
| `canActivate` | Before entering a route | Authentication |
| `canActivateChild` | Before entering child routes | Protect child routes |
| `canDeactivate` | Before leaving a route | Unsaved form changes |
| `resolve` | Before creating a component | Load required data |
| `canMatch` | Before route matching | Feature flags, permissions, lazy loading decisions |

---

# Common Interview Questions

### Difference between `canActivate` and `canActivateChild`?

`canActivate` protects a specific route.

`canActivateChild` protects all child routes of a parent.

---

### When is `canDeactivate` executed?

Only when leaving a route.

---

### Why use Resolve instead of loading data inside `ngOnInit()`?

Because the component is displayed only after the required data is loaded, avoiding empty or partially loaded screens.

---

### Why should we return the Observable from a Resolver?

Angular automatically subscribes to it. Calling `subscribe()` inside the resolver prevents Angular from waiting for the data correctly.

---

### Why prefer `router.createUrlTree()` over `router.navigate()` inside a guard?

Returning a `UrlTree` allows the router to manage the redirect as part of the same navigation, resulting in cleaner and more predictable behavior.

---

# Quick Revision

- Route Guards control navigation.
- `canActivate` → Enter a route.
- `canActivateChild` → Enter child routes.
- `canDeactivate` → Leave a route.
- `resolve` → Load data before component creation.
- `canMatch` → Decide whether a route should match.
- Functional guards use `inject()`.
- Prefer returning `UrlTree` instead of calling `router.navigate()` inside guards.
- `canDeactivate` receives the component instance.
- Return the Observable directly from a Resolver—do not subscribe.
- Access resolved data using `ActivatedRoute.data` or `snapshot.data`.