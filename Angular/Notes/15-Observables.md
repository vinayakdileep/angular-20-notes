# RxJS Interview Revision Notes

## Promise vs Observable

| Promise                        | Observable                               |
| ------------------------------ | ---------------------------------------- |
| Native JavaScript feature      | Part of RxJS library                     |
| Emits only one value           | Emits multiple values (stream of data)   |
| Eager (starts immediately)     | Lazy (starts only after subscription)    |
| Handles asynchronous data only | Handles synchronous or asynchronous data |
| Cannot be cancelled            | Can be cancelled using `unsubscribe()`   |
| Uses `then()`, `catch()`       | Uses `subscribe()`                       |

### Interview Definition

* **Promise** represents a single future value.
* **Observable** represents a stream of values over time.

---

# Observable Pattern

Observable pattern consists of three parts:

## Observable

* Produces/emits data.

## Observer

* Consumes the data.
* Starts receiving data only after calling `subscribe()`.

```typescript
observable.subscribe(...)
```

## Handler

Handles emitted events.

```typescript
observable.subscribe({
    next: value => {},
    error: err => {},
    complete: () => {}
});
```

---

# Observable Events

## next()

Used to emit data.

```typescript
observer.next(value);
```

---

## error()

Terminates the Observable with an error.

```typescript
observer.error("Something went wrong");
```

**Important**

* After `error()`, Observable cannot emit any more values.

---

## complete()

Indicates successful completion.

```typescript
observer.complete();
```

**Important**

* After `complete()`, Observable cannot emit any more values.

Observable lifecycle:

```text
next()
next()
next()
complete()

OR

next()
next()
error()
```

---

# of() Operator

Creates an Observable from multiple values.

```typescript
const values = [1,2,3];

of(values, 6, "Angular");
```

Output:

```text
[1,2,3]
6
Angular
complete
```

> The array is treated as **one value**.

---

# from() Operator

Creates an Observable from:

* Array
* String
* Set
* Map
* Promise

Example:

```typescript
from([1,2,3]);
```

Output

```text
1
2
3
complete
```

Unlike `of()`, every array element is emitted separately.

### Promise to Observable

```typescript
const promise = Promise.resolve(100);

from(promise);
```

---

# fromEvent()

Converts browser events into Observables.

```typescript
fromEvent(button, 'click')
    .subscribe(() => console.log("Clicked"));
```

Common uses:

* Click
* Keyup
* Mousemove
* Scroll
* Resize

---

# Pipe()

Used to chain multiple RxJS operators.

```typescript
observable.pipe(
    map(...),
    filter(...),
    take(...)
);
```

**Important**

* Operators execute from left to right.
* Every operator returns a new Observable.
* Output of one operator becomes input of the next operator.

---

# map()

Transforms emitted values.

```typescript
of(1,2,3)
.pipe(
    map(x => x * 10)
);
```

Output

```text
10
20
30
```

Remember:

* Used inside `pipe()`
* Must return transformed value

---

# filter()

Filters values based on a condition.

```typescript
of(1,2,3,4,5)
.pipe(
    filter(x => x % 2 === 0)
);
```

Output

```text
2
4
```

Remember:

* Used inside `pipe()`
* Returns only matching values

---

# Observable vs Subject

## Observable

* Unicast
* Each subscriber gets its own execution
* Only produces data
* Cannot manually emit values

```typescript
const obs = interval(1000);

obs.subscribe();
obs.subscribe();
```

Each subscription runs independently.

---

## Subject

* Multicast
* Same emitted value reaches all subscribers
* Acts as both Observable and Observer
* Can manually emit values using `next()`

```typescript
const subject = new Subject<number>();

subject.subscribe(x => console.log("A", x));
subject.subscribe(x => console.log("B", x));

subject.next(10);
```

Output

```text
A 10
B 10
```

---

# Subject vs BehaviorSubject vs ReplaySubject vs AsyncSubject

## Subject

* No initial value
* New subscribers receive only future values

```typescript
const subject = new Subject<number>();

subject.next(1);

subject.subscribe(console.log);

subject.next(2);
```

Output

```text
2
```

---

## BehaviorSubject

* Requires an initial value
* Stores latest value
* New subscribers immediately receive the latest value

```typescript
const subject = new BehaviorSubject<number>(0);

subject.next(1);

subject.subscribe(console.log);
```

Output

```text
1
```

Common use:

* Login user
* Theme
* Shared application state

---

## ReplaySubject

* Stores previous values
* New subscribers receive old values

```typescript
const subject = new ReplaySubject<number>(2);

subject.next(1);
subject.next(2);
subject.next(3);

subject.subscribe(console.log);
```

Output

```text
2
3
```

---

## AsyncSubject

* Emits only the last value
* Emits only after `complete()` is called

```typescript
const subject = new AsyncSubject<number>();

subject.next(1);
subject.next(2);
subject.next(3);

subject.complete();
```

Output

```text
3
```

---

# Quick Comparison

| Type            | Initial Value | New Subscriber Receives   | Common Use          |
| --------------- | ------------- | ------------------------- | ------------------- |
| Subject         | ❌ No          | Future values only        | Event communication |
| BehaviorSubject | ✅ Yes         | Latest value              | Shared state        |
| ReplaySubject   | Optional      | Previous values           | History, Cache      |
| AsyncSubject    | ❌ No          | Last value after complete | Final result        |

---

# Hot vs Cold Observable

## Cold Observable

* Starts execution separately for every subscriber.
* Each subscriber gets its own data stream.

Examples:

* `HttpClient`
* `of()`
* `from()`

---

## Hot Observable

* Shares one execution among all subscribers.
* Subscribers receive the same emitted values.

Examples:

* Subject
* DOM Events (`fromEvent`)
* WebSocket

---

# Why Angular HttpClient Returns Observable?

Because Observables support:

* Cancellation (`unsubscribe()`)
* Multiple RxJS operators
* Better async composition
* Streaming data
* Retry and error handling

---

# When Should We Unsubscribe?

Unsubscribe from long-running Observables like:

* `interval()`
* `fromEvent()`
* WebSocket
* Subject

No manual unsubscribe is usually required for:

* `HttpClient`

because HTTP Observables complete automatically after one response.

---

# Interview One-Liners

* Promise is **eager**, Observable is **lazy**.
* Promise emits **one value**, Observable emits **multiple values**.
* Observable can be **cancelled** using `unsubscribe()`.
* `pipe()` is used to chain RxJS operators.
* `map()` transforms values.
* `filter()` removes unwanted values.
* Observable is **unicast**.
* Subject is **multicast**.
* BehaviorSubject always stores the latest value.
* ReplaySubject replays previous values to new subscribers.
* AsyncSubject emits only the final value after completion.
* After `error()` or `complete()`, an Observable cannot emit more values.
