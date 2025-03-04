# RFC proposal

This is roughly what is described in the RFC.

## TL;DR

* Traits can be declared as `const`, in which case all methods are "maybe-`const`" (depending on the impl)
* Impl can decide whether it is implementing `const Trait` (const or maybe-const methods)

## Design axioms

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
const trait Default {
    fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> const Default for Wrapper<T>
where
    T: ~const Default,
{
    fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: ~const Default,
{
    match x {
        None => <Wrapper<T>>::default(),
        Some(v) => v,
    }
}
```

