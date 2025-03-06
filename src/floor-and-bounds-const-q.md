# const and const?

## Summary

* Declare the "floor" on the item
    * `const`
    * `do` -- declares default effects
* For bounds, declare "maybe" bounds with a `?`, e.g., `const?` or `do?`
* In a trait method, declare "maybe" items with a `?` to tie them to the trait mode

## Design axioms

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
const trait Default {
    const? fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> const Default for Wrapper<T>
where
    T: const? Default,
{
    const? fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: const? Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

## Observations

* Pretty confusing that you write `const?` fn in the trait definition but `const fn` outside of it
