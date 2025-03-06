# do bounds

This is roughly what is described in the RFC.

## TL;DR

* You can think of the `do` keyword as saying "plus some other effects"
* If you write `do fn`, then, it is "default + some more"
* If you write `const do`

## Design axioms

* [We will want a "lower floor" than `const`](./axioms.md#we-will-want-a-lower-floor-than-const)
    * ...and therefore we have people write `const do fn`

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
const do trait Default {
    const do fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> const do Default for Wrapper<T>
where
    T: const do Default,
{
    const do fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

const do fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: const do Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

