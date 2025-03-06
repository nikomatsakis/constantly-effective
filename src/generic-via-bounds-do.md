# do bounds

## TL;DR

## Design axioms

* [Twiddle const is where Rust jumps the shark](./axioms.md#twiddle-const-is-where-rust-jumps-the-shark)
    * Therefore we use `do`

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
    T: const Default,
{
    const fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: do Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

