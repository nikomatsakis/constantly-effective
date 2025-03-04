# do bounds

This is roughly what is described in the RFC.

## TL;DR

## Design axioms

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
do trait Default {
    do fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> do Default for Wrapper<T>
where
    T: do Default,
{
    fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

do fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: do Default,
{
    match x {
        None => <Wrapper<T>>::default(),
        Some(v) => v,
    }
}
```

