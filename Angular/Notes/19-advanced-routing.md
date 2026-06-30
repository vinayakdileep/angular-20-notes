# Angular Routing - Part 4 (Advanced Routing)

---

# Router Navigation Events

## What are Router Navigation Events?

Whenever Angular navigates from one route to another, it emits a sequence of **Router Events**.

These events help us determine the current stage of navigation.

They are commonly used for:

- Displaying a loading spinner
- Analytics
- Logging
- Route tracking
- Performance monitoring

---

## Common Navigation Events

| Event | Description |
|--------|-------------|
| `NavigationStart` | Navigation has started. |
| `RoutesRecognized` | Angular has recognized the matching route. |
| `GuardsCheckStart` | Route guards start executing. |
| `GuardsCheckEnd` | Route guards finish executing. |
| `ResolveStart` | Route resolvers start loading data. |
| `ResolveEnd` | Route resolvers finish loading data. |
| `NavigationEnd` | Navigation completed successfully. |
| `NavigationCancel` | Navigation cancelled (usually by a route guard). |
| `NavigationError` | Navigation failed due to an error. |

---

## Real-time Use Case

Suppose a page takes 4 seconds to load because of an API call.

Instead of showing a blank page, we can display a loading spinner while Angular is navigating.

```
User Clicks Menu

↓

NavigationStart

↓

Show Loader

↓

Component Loads

↓

NavigationEnd

↓

Hide Loader
```

---

## Example

The loader should be placed in the component that contains the main `<router-outlet>` (usually `AppComponent`).

```ts
import {
    NavigationStart,
    NavigationEnd,
    NavigationCancel,
    NavigationError,
    Router
} from '@angular/router';

export class AppComponent {

    isLoading = false;

    router = inject(Router);

    constructor() {

        this.router.events.subscribe(event => {

            if (event instanceof NavigationStart) {

                this.isLoading = true;

            }

            if (
                event instanceof NavigationEnd ||
                event instanceof NavigationCancel ||
                event instanceof NavigationError
            ) {

                this.isLoading = false;

            }

        });

    }

}
```

> **Best Practice**
>
> Use `instanceof` instead of checking `event.constructor.name`. It is type-safe, easier to refactor, and recommended by Angular.

---

## Template

```html
<div *ngIf="isLoading">
    Loading...
</div>

<router-outlet></router-outlet>
```

---

## Interview Tip

`NavigationCancel` usually occurs when a route guard blocks navigation.

---

# Passing Data Between Routes

Angular provides multiple ways to pass data between routes.

| Method | Use Case |
|---------|----------|
| Route Parameters | Mandatory values (ID, Code, etc.) |
| Query Parameters | Filtering, Searching, Sorting |
| Route Data | Static configuration data |
| Navigation State | Dynamic objects |

---

# Static Route Data

Static data is defined in the route configuration.

## Route Configuration

```ts
{
    path: 'Courses',
    component: CoursesComponent,

    data: {
        title: 'Courses',
        showFilter: true
    }
}
```

---

## Reading Static Data

### Using Snapshot

```ts
const title =
    this.activatedRoute.snapshot.data['title'];
```

### Using Observable

```ts
this.activatedRoute.data.subscribe(data => {

    console.log(data['title']);

});
```

---

## Real-time Uses

- Page Titles
- Breadcrumbs
- Feature Flags
- User Roles
- Layout Configuration

---

# Passing Dynamic Data Using Navigation State

Sometimes we don't want to expose data in the URL.

Angular allows passing data using **Navigation State**.

---

## HTML Example

```html
<button
    class="btn"
    [routerLink]="['/Courses', 'checkout']"
    [state]="course">

    BUY NOW

</button>
```

---

## Programmatically

```ts
this.router.navigate(
    ['/Courses', 'checkout'],
    {
        state: course
    }
);
```

---

## Reading Navigation State

```ts
course!: Course;

ngOnInit() {

    this.course = history.state as Course;

    console.log(this.course);

}
```

---

## Real-time Example

Suppose the user selects a course.

```
Course List

↓

Click BUY NOW

↓

Navigate to Checkout

↓

Pass complete Course object

↓

Checkout page receives Course object
```

No additional API call may be needed if all required information is already available.

---

## Important Notes

- Navigation state is **not visible in the URL**.
- It exists only for that navigation.
- Refreshing the page clears the navigation state.
- Do not use it for persistent data.

---

## Route Parameters vs Navigation State

| Route Parameters | Navigation State |
|-----------------|------------------|
| Visible in URL | Hidden from URL |
| Good for IDs | Good for objects |
| Survives page refresh | Lost after refresh |
| Bookmarkable | Not bookmarkable |

---

# Passing Data - Which Method Should I Use?

| Requirement | Recommended Method |
|-------------|--------------------|
| View Employee by ID | Route Parameter |
| Search Employees | Query Parameter |
| Configure Page | Route Data |
| Pass Selected Object | Navigation State |

---

# Common Interview Questions

### What are Router Navigation Events?

Router events are emitted during every navigation and help track different stages of the routing lifecycle.

---

### Which Router Event is used to display a loader?

`NavigationStart`

Hide the loader when:

- `NavigationEnd`
- `NavigationCancel`
- `NavigationError`

---

### Why should we use `instanceof` instead of `constructor.name`?

`instanceof` is type-safe, more reliable, and recommended by Angular.

---

### Difference between Route Data and Navigation State?

**Route Data**

- Static
- Defined in route configuration
- Available whenever the route is active

**Navigation State**

- Dynamic
- Passed during navigation
- Lost after browser refresh

---

### Can Navigation State survive a page refresh?

No.

After refreshing the browser, the navigation state is lost.

---

### When should Navigation State be used?

When passing complex objects between routes without exposing them in the URL.

---

# Best Practices

- Show a loader during long-running navigations.
- Use `instanceof` for checking router events.
- Use Route Parameters for resource identifiers.
- Use Query Parameters for optional filters.
- Use Route Data for static configuration.
- Use Navigation State for temporary objects.
- Avoid passing large objects through Navigation State if they can be fetched efficiently from the server.

---

# Quick Revision

- Router emits multiple events during navigation.
- `NavigationStart` → Show Loader.
- `NavigationEnd` → Hide Loader.
- `NavigationCancel` → Navigation blocked or cancelled.
- `NavigationError` → Navigation failed.
- Use `instanceof` to identify router events.
- Route Data → Static information.
- Navigation State → Dynamic object.
- Navigation State is not visible in the URL.
- Navigation State is lost after a page refresh.
- Use Route Parameters for IDs.
- Use Query Parameters for searching and filtering.