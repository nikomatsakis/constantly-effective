# do bounds

This is roughly what is described in the RFC.

## TL;DR

* Write `const` to mean "can only do const things"
* Write `const+` to mean "can only directly do const things, but may do non-const things via trait bounds"

## Design axioms

* We will want a lower floor than `const` eventually
    * What we've learned from the `?Sized` experience is that it's best to have people "ask for the things they need".
* You should be able to declare a maybe-const function without understanding effects.
    * Therefore, we prefer a `const`-keyword-based syntax.

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
const+ trait Default {
    const+ fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> const+ Default for Wrapper<T>
where
    T: const+ Default,
{
    const+ fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const+ fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: const+ Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

