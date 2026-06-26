# Angular Lifecycle Hooks – Short Notes

- **Lifecycle hooks** are special methods that Angular calls at different stages of a component or directive's lifecycle.
- Hooks with the **`ngAfter...` prefix** (`ngAfterContentInit`, `ngAfterContentChecked`, `ngAfterViewInit`, `ngAfterViewChecked`) are **available only for components** and **not for directives**.
- **All `Init` hooks** (`ngOnInit`, `ngAfterContentInit`, `ngAfterViewInit`) are executed **only once** during component initialization.
- **All `Checked` hooks** (`ngDoCheck`, `ngAfterContentChecked`, `ngAfterViewChecked`) are executed during **every change detection cycle**, including component creation, updates, and checks.
- **`ngOnChanges`** is invoked **only for `@Input()` property changes**. It compares the **previous value** and the **current value** and provides the changes through the `SimpleChanges` object.
- **`ngAfterContentInit`** is called **once after content projection (`<ng-content>`) is completed**.
- **`ngAfterContentChecked`** is called **after every check of the projected content**.
- **`ngAfterViewInit`** is called **once after the component's view and all child component views have been fully initialized**. It executes **after all child component lifecycle methods have finished initialization**.
- **`ngAfterViewChecked`** is called **after every change detection cycle once the component's view has been checked**.
- **`ngOnDestroy`** is called **just before Angular destroys a component or directive**. It is used to perform cleanup tasks such as unsubscribing from Observables, removing event listeners, and clearing timers to avoid memory leaks.