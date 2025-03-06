# quadrant `(const)` 

## TL;DR

To declare a functon with a "conditionally const" bound, you write `(const) Default`

```rust
const fn get<T: (const) Default> {...}
```

To declare a functon with an "always const" bound, you write `const Default`

```rust
const fn get<T: const Default> {
    const { T::default() }
}
```

To declare a trait with a method that is "conditionally const" depending on the impl:

```rust
trait Default {
    (const) fn default() -> Self;
}
```

To implement the trait with an impl that is always const:

```rust
impl Default for i32 {
    const fn default() -> i32 {
        0
    }
}
```

To implement the trait with an impl that is never const:

```rust
impl<T> Default for Box<T>
where
    T: Default,
{
    fn default() -> Self {
        Box::new(T::default())
    }
}
```

To implement the trait with a "conditionally const" impl:

```rust
impl<T> Default for [T; 1]
where
    T: (const) Default,
{
    (const) fn default() -> Self { // (*)
        [T::default()]
    }

    // (*) it is possible this should be `const`, we can decide,
    // I'm not sure yet which I like better.
}
```

The model also supports

* "always-const" methods
* "always-maybe-const" methods

see below.

## Design axioms

* [The step from a monomorphic `const fn` to one with a generic bound should be minimal](./axioms.md#the-step-from-a-monomorphic-const-fn-to-one-with-a-generic-bound-should-be-minimal)
    * In this model, you "just add something like `T: (const) Default`"


## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
trait Default {
    // Declare the item as conditionally const (depending on the impl):
    (const) fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> Default for Wrapper<T>
where
    T: (const) Default,
{
    // When implementing a conditionally const method, you write `(const)`
    // to indicate that it is "
    //
    // Variation: We could also have you write `const fn default() -> Self`
    // here. See discussion below.
    (const) fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: (const) Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

### Desugaring walkthrough

```rust
trait Default {
    // Declare the item as conditionally const (depending on the impl):
    (const) fn default() -> Self;
}
```

This is desugared to

```rust
trait Default {
    do DefaultEffect; // <-- because of the `(const)`

    fn default() -> Self
    do
        <Self as Default>::DefaultEffect; // <-- because of the `(const)`
}
```

The function `get_me` would be desugared as:

```rust
fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: Default,
do
    <T as Default>::Effect, // <-- because of the `T: (const) Default`
{
    match x {
        None => <Wrapper<T>>::default(),
        Some(v) => v,
    }
}
```

The `impl` is desugared as

```rust
impl<T> Default for Wrapper<T>
where
    T: Default,
{
    do DefaultEffect = (
        const, // <-- included because `default` was declared as `(const)`
        <T as Default>::Effect, // <-- because of `T: (const) Default` from the impl header
    );

    fn default() -> Self
    do
        <Self as Default>::DefaultEffect, // <-- because `default` was declared as `(const)`
    {
        Wrapper {
            value: T::default()
        }
    }
}
```

## [All the things](./formality-example.md#all-the-things)

```rust
trait ATT {
    // const in all impls:
    const fn always_const(&self) -> u32;

    // const if declared in impl as const≈g
    (const) fn maybe_const(&self) -> u32;

    // const if (a) declared in impl as const and (b) methods in `T: ATT` impl are const
    (const) fn maybe_maybe_const<T: (const) ATT>(&self) -> u32;

    // includes effects from `T`
    const fn always_maybe_const<T: (const) ATT>(&self) -> u32;
}
```≈g