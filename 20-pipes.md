# Angular Pipes - Interview Quick Revision Notes

---

# Pipes

## What are Pipes?

Pipes are used to **transform or format data before displaying it in the view**.

They improve the readability of data without modifying the original value.

Pipes are applied using the (`|`) operator.

```html
{{ value | pipeName }}
```

---

## Why do we use Pipes?

- Format dates
- Format currency
- Convert text to uppercase/lowercase
- Display percentages
- Slice strings or arrays
- Handle asynchronous data
- Create reusable data transformations

---

## Built-in Pipes

Angular provides several built-in pipes.

### UpperCase Pipe

Converts text to uppercase.

```html
{{ 'angular interview' | uppercase }}
```

**Output**

```
ANGULAR INTERVIEW
```

---

### LowerCase Pipe

Converts text to lowercase.

```html
{{ 'ANGULAR' | lowercase }}
```

**Output**

```
angular
```

---

### TitleCase Pipe

Capitalizes the first letter of every word.

```html
{{ 'angular interview preparation' | titlecase }}
```

**Output**

```
Angular Interview Preparation
```

---

### Currency Pipe

Formats numbers as currency.

```html
{{ 25000 | currency }}
```

**Output**

```
$25,000.00
```

Specify currency.

```html
{{ 25000 | currency:'INR' }}
```

**Output**

```
₹25,000.00
```

---

### Date Pipe

Formats dates.

```html
{{ today | date }}
```

```html
{{ today | date:'short' }}
```

```html
{{ today | date:'dd/MM/yyyy' }}
```

---

### Decimal Pipe

Formats decimal numbers.

```html
{{ 1234.5678 | number:'1.2-2' }}
```

**Output**

```
1234.57
```

---

### Percent Pipe

```html
{{ 0.75 | percent }}
```

**Output**

```
75%
```

---

### Slice Pipe

Works on strings and arrays.

```html
{{ 'Angular Interview' | slice:0:7 }}
```

Output

```
Angular
```

---

## Chaining Pipes

Multiple pipes can be applied to the same value.

Pipes execute **from left to right**.

```html
{{ today | date:'fullDate' | uppercase }}
```

Angular first formats the date and then converts the result to uppercase.

---

## Interview Tip

> Pipes never modify the original data.
>
> They only transform the value before displaying it in the UI.

---

# Custom Pipes

Custom pipes are used when the built-in pipes cannot satisfy the application's requirements.

---

## Steps to Create a Custom Pipe

### 1. Add the `@Pipe` decorator

```ts
@Pipe({
    name: 'percentage'
})
```

---

### 2. Implement `PipeTransform`

```ts
export class PercentagePipe implements PipeTransform
```

---

### 3. Implement the `transform()` method

The first parameter is always the value on which the pipe is applied.

Additional parameters are passed using `:`.

---

## Example

### HTML

```html
<span>
    {{ 86 | percentage:100:2 }}
</span>
```

---

### Pipe

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'percentage'
})
export class PercentagePipe implements PipeTransform {

  transform(
      value: number,
      total: number,
      decimal: number = 0
  ): string {

    if (total === 0) {
      return '0%';
    }

    const percentage = (value / total) * 100;

    return percentage.toFixed(decimal) + '%';
  }
}
```

**Output**

```
86.00%
```

---

## Passing Multiple Parameters

Parameters are separated using `:`.

```html
{{ marks | percentage:totalMarks:2 }}
```

Inside `transform()`

```ts
transform(value, totalMarks, decimalPlaces)
```

---

## Interview Tip

The first parameter of `transform()` is always the value before the pipe.

All remaining parameters come from the HTML after `:`.

---

# Pure vs Impure Pipes

## Pure Pipe (Default)

All Angular pipes are **pure by default**.

A pure pipe executes only when Angular detects a **pure change**.

---

## What is a Pure Change?

A pure change occurs when:

### Primitive values change

- string
- number
- boolean

Example

```ts
studentName = "John";

studentName = "David";
```

Angular executes the pipe.

---

### Reference changes

For objects and arrays, Angular checks the **reference**, not the contents.

```ts
students = [...students, newStudent];
```

Since the array reference changed, the pipe executes.

---

## What is NOT a Pure Change?

Changing the contents of an existing object or array without changing its reference.

Example

```ts
students.push(newStudent);
```

The array reference remains the same.

A pure pipe **will not execute**.

---

## Impure Pipe

To make a pipe impure,

```ts
@Pipe({
    name: 'genderFilter',
    pure: false
})
```

Angular executes the pipe during **every change detection cycle**.

---

## Real-Time Example

Suppose a Gender filter is applied.

Current list

```
John
David
Alex
```

User adds

```
Peter
```

using

```ts
students.push(newStudent);
```

### Using a Pure Pipe

The filtered list **does not update** because the array reference remains unchanged.

---

### Using an Impure Pipe

The new student immediately appears because the pipe executes during every change detection cycle.

---

## Why are Impure Pipes discouraged?

Impure pipes execute during **every change detection cycle**.

This can significantly reduce application performance, especially for:

- Filtering
- Sorting
- Searching
- Large collections

Therefore Angular recommends **avoiding impure pipes** whenever possible.

---

# Why Filtering and Sorting Should Not Be Done Using Pipes

Although it is possible, Angular **does not recommend** using pipes for filtering or sorting.

Reasons

- Poor performance
- Executes repeatedly
- Difficult to debug
- Recalculates frequently

---

## Recommended Approach

Move the filtering and sorting logic into:

- Component
- Service

Update the source array whenever the filter changes.

---

## Example

Instead of

```html
<select [(ngModel)]="gender">
```

Use

```html
<select
    [value]="gender"
    (change)="getFilteredData($event)">
```

Component

```ts
getFilteredData(event: Event) {

    const gender =
        (event.target as HTMLSelectElement).value;

    this.students =
        this.studentService.filterStudents(gender);
}
```

This is more efficient than filtering through a pipe.

---

# Async Pipe

`async` is a built-in Angular pipe.

It automatically

- subscribes to an Observable or Promise
- displays the latest emitted value
- unsubscribes automatically when the component is destroyed

---

## Why use Async Pipe?

Without Async Pipe

```ts
this.studentService.getStudentsCount()
.subscribe(count => {
    this.totalStudents = count;
});
```

You must manually

- subscribe
- unsubscribe
- avoid memory leaks

---

## Using Async Pipe

Component

```ts
totalStudents$ =
this.studentService.getStudentsCount();
```

HTML

```html
{{ totalStudents$ | async }}
```

Angular automatically subscribes.

When a new value is emitted, the UI updates automatically.

---

## Benefits

- Automatic subscription
- Automatic unsubscription
- Prevents memory leaks
- Cleaner code
- Recommended over manual subscription for template binding

---

# Common Interview Questions

### What are Pipes?

Pipes transform or format data before displaying it in the UI.

---

### Do Pipes modify the original value?

No.

Pipes only transform the displayed value.

---

### Difference between Built-in and Custom Pipes?

| Built-in Pipe | Custom Pipe |
|---------------|-------------|
| Provided by Angular | Created by developers |
| General formatting | Business-specific transformations |

---

### Difference between Pure and Impure Pipes?

| Pure Pipe | Impure Pipe |
|------------|-------------|
| Default | `pure:false` |
| Runs only for pure changes | Runs every change detection cycle |
| Better performance | Slower |
| Recommended | Avoid unless absolutely necessary |

---

### Why shouldn't we use Pipes for filtering and sorting?

Because filtering and sorting are computationally expensive.

Impure pipes execute during every change detection cycle and can negatively affect application performance.

Instead, perform filtering and sorting inside the component or a service.

---

### Why is Async Pipe preferred?

- Automatically subscribes
- Automatically unsubscribes
- Prevents memory leaks
- Keeps templates clean
- Updates UI automatically when new values are emitted

---

# Common Mistakes

❌ Subscribing inside a custom pipe

❌ Using impure pipes for large collections

❌ Using pipes for filtering and sorting

❌ Forgetting that pure pipes check object/array references, not their contents

❌ Manually subscribing in the component when the value is only displayed in the template (use `async` instead)

---

# Quick Revision

- Pipes transform data before displaying it.
- Built-in pipes: `uppercase`, `lowercase`, `titlecase`, `currency`, `date`, `number`, `percent`, `slice`, `async`.
- Multiple pipes execute from **left to right**.
- Custom pipes implement `PipeTransform`.
- `transform()` first parameter is the input value.
- Additional parameters are passed using `:`.
- Pipes are **pure by default**.
- Pure pipes execute only when primitive values or object references change.
- Impure pipes (`pure:false`) execute during every change detection cycle.
- Avoid filtering and sorting using pipes.
- Use services/components for filtering and sorting.
- `async` pipe automatically subscribes and unsubscribes from Observables and Promises.