# Angular Routing - Part 2 (Route Parameters, Query Parameters, Fragments & Child Routes)

---

# Route Parameters

## What are Route Parameters?

Route parameters are **dynamic values** that are part of the URL path. They are commonly used to identify a specific resource such as a course, employee, product, or order.

### Real-time Examples

```
/Courses/101
/Employees/25
/Products/15
```

Here, `101`, `25`, and `15` are route parameters.

---

## When should we use Route Parameters?

Use route parameters when the value is **mandatory** to display the requested resource.

**Examples**

- View Course Details
- View Employee Details
- Edit Product
- View Order

---

## Registering Route Parameters

```ts
export const routes: Routes = [
  {
    path: 'Courses/:id',
    component: CourseDetailComponent
  }
];
```

Here, `:id` represents the route parameter.

---

## Navigating with Route Parameters

### Using `routerLink`

```html
<button
    class="btn"
    [routerLink]="['/Courses', course.id]">
    DETAILS
</button>
```

If

```ts
course.id = 10;
```

Angular navigates to

```
/Courses/10
```

---

### Programmatically

```ts
this.router.navigate(['/Courses', course.id]);
```

---

# Reading Route Parameters

Angular provides two ways to read route parameters.

## 1. Using Snapshot

```ts
courseDetail!: Course | undefined;
courseId!: number;

courseService = inject(CourseService);
activatedRoute = inject(ActivatedRoute);

ngOnInit(): void {

  this.courseId =
      +(this.activatedRoute.snapshot.paramMap.get('id') ?? 0);

  this.courseDetail =
      this.courseService.courses.find(
          c => c.id === this.courseId
      );

}
```

### When to use Snapshot?

Use `snapshot` when you only need the parameter once while the component is created.

### Limitation

Snapshot **does not detect future route parameter changes** if Angular reuses the same component instance.

Example

```
/Courses/1

↓

/Courses/2
```

The component is reused, but `snapshot` still contains the old value.

---

## 2. Using `paramMap` (Recommended)

```ts
courseDetail!: Course | undefined;
courseId!: number;

courseService = inject(CourseService);
activatedRoute = inject(ActivatedRoute);

ngOnInit(): void {

    this.activatedRoute.paramMap.subscribe(params => {

        this.courseId = +(params.get('id') ?? 0);

        this.courseDetail =
            this.courseService.courses.find(
                c => c.id === this.courseId
            );

    });

}
```

### Why use `paramMap`?

`paramMap` is an Observable.

Whenever the route parameter changes, Angular emits the updated value.

Example

```
/Courses/1

↓

/Courses/2

↓

/Courses/3
```

The subscription executes for every navigation.

---

## Snapshot vs paramMap

| Snapshot | paramMap |
|-----------|----------|
| Reads once | Emits whenever parameter changes |
| Good for one-time read | Recommended for dynamic parameters |
| Synchronous | Observable |

---

## Interview Tip

If Angular reuses the same component for different route parameters, always prefer **paramMap**.

---

# Query Parameters

## What are Query Parameters?

Query parameters are **optional key-value pairs** appended after `?` in the URL.

Example

```
/Courses?search=Angular
```

or

```
/Employees?page=2&sort=name
```

---

## Route Parameters vs Query Parameters

| Route Parameters | Query Parameters |
|-----------------|------------------|
| Mandatory | Optional |
| Part of the route path | Appended after `?` |
| Must be defined in Routes | No route registration required |
| Used to identify a resource | Used for filtering, sorting, searching, pagination |

---

## Passing Query Parameters

### Programmatically

```ts
this.router.navigate(
    ['/Courses'],
    {
        queryParams: {
            search: 'javascript'
        }
    }
);
```

Result

```
/Courses?search=javascript
```

---

### Using `routerLink`

```html
<button
    class="btn"
    [routerLink]="['/Courses']"
    [queryParams]="{
        search: 'javascript'
    }">

    JavaScript Courses

</button>
```

---

## Reading Query Parameters

```ts
ngOnInit(): void {

    this.activatedRoute.queryParamMap.subscribe(params => {

        const searchText = params.get('search');

        if (searchText) {

            this.AllCourses =
                this.courseService.courses.filter(course =>
                    course.title
                        .toLowerCase()
                        .includes(searchText.toLowerCase())
                );

        } else {

            this.AllCourses =
                this.courseService.courses;

        }

    });

}
```

---

## Why use `queryParamMap`?

Since it is an Observable, Angular automatically notifies the component whenever query parameters change.

---

## Real-time Uses

- Search
- Filtering
- Sorting
- Pagination
- Language selection
- Theme selection

---

# Fragment

## What is a Fragment?

A fragment is the part of a URL after the `#` symbol.

Example

```
http://localhost:4200/Home#search
```

Here,

```
search
```

is the fragment.

---

## Purpose

A fragment allows navigation to a specific section of the page.

The fragment value should match the **id** of an HTML element.

Example

```html
<section id="search">
```

---

## Setting Fragment

```html
<a
    routerLink="/Home"
    fragment="search">

    SEARCH

</a>
```

Angular navigates to

```
/Home#search
```

---

## Reading Fragment

```ts
activatedRoute = inject(ActivatedRoute);

ngOnInit(): void {

    this.activatedRoute.fragment.subscribe(fragment => {

        this.loadFragment(fragment);

    });

}
```

---

## Scroll to Fragment

```ts
loadFragment(fragment: string | null) {

    if (fragment) {

        document
            .getElementById(fragment)
            ?.scrollIntoView({
                behavior: 'smooth'
            });

    }

}
```

---

## Important Notes

- A URL can contain only **one fragment**.
- Fragment is always written after `#`.
- Fragment does not trigger a server request.
- Mainly used for long pages and documentation websites.

---

## Real-time Uses

- Documentation websites
- FAQ pages
- Table of Contents
- Jump to Search section
- Jump to Comments

---

# Child Routes

## What are Child Routes?

Child routes allow a parent route to contain multiple sub-routes.

Example

```
Courses
│
├── Popular
├── Checkout
└── Course Details
```

---

## Route Configuration

```ts
export const routes: Routes = [

  {
    path: 'Courses',
    component: CoursesComponent,

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

## Parent Component

The parent component **must contain** another `<router-outlet>`.

```html
<h2>Courses</h2>

<nav>
    ...
</nav>

<router-outlet></router-outlet>
```

Without the child `router-outlet`, Angular cannot render child components.

---

## Child URLs

```
/Courses

/Courses/popular

/Courses/checkout

/Courses/15
```

---

## Why use Child Routes?

They allow related pages to share the same layout and navigation.

**Examples**

- Dashboard
- Admin Panel
- User Profile
- Employee Module
- Course Module

---

## Advantages

- Better route organization
- Shared layout
- Reusable navigation
- Easier route protection
- Cleaner routing configuration

---

# Common Interview Questions

### Difference between Route Parameters and Query Parameters?

**Route Parameters**

- Mandatory
- Part of the route path
- Used to identify a resource

Example

```
/Courses/10
```

**Query Parameters**

- Optional
- Key-value pairs
- Used for filtering, sorting, searching, and pagination

Example

```
/Courses?search=Angular
```

---

### Difference between Snapshot and paramMap?

| Snapshot | paramMap |
|-----------|----------|
| Reads value once | Emits updated values |
| Synchronous | Observable |
| Suitable for one-time read | Suitable for dynamic routes |

---

### Why should we use `paramMap`?

Because Angular may reuse the same component while changing route parameters. `paramMap` automatically receives the latest values.

---

### Why are Query Parameters useful?

They allow optional data such as search text, page number, filters, and sorting options without changing the route definition.

---

### Why do Child Routes need another `router-outlet`?

The parent component acts as a container. Angular needs another `router-outlet` to know where to render the child component.

---

# Common Mistakes

❌ Using `snapshot` when route parameters change dynamically.

❌ Forgetting to subscribe to `paramMap`.

❌ Using Route Parameters for optional filters.

❌ Forgetting to add `<router-outlet>` inside the parent component.

❌ Expecting fragments to work without matching HTML element IDs.

---

# Quick Revision

- Route Parameters → Mandatory (`/Courses/10`)
- Query Parameters → Optional (`?search=Angular`)
- Fragment → Navigate to a section (`#search`)
- `snapshot` → Reads once
- `paramMap` → Updates automatically
- `queryParamMap` → Updates automatically
- Child Routes → Organize related pages
- Parent component must contain a child `<router-outlet>`
- Fragment value must match an HTML element `id`