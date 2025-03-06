# Generic via bounds

In the "generic via bounds" approach, you declare 

* direct effects on the function or item (e.g., `const fn`);
* but you have have special bounds that potentially have other effects.

## Design axioms

As in the [RFC proposal](./rfc-proposal.md), we wish to ensure

* [The step from a monomorphic `const fn` to one with a generic bound should be minimal](./axioms.md#the-step-from-a-monomorphic-const-fn-to-one-with-a-generic-bound-should-be-minimal)

but we also believe

* [We will want to have more effects than `const`](./axioms.md#we-will-want-to-have-more-effects-than-const)

However, we are not convinced that

* [Traits in libstd that becomes a "const trait" will want to be an "async trait" too](./axioms.md#traits-in-libstd-that-becomes-a-const-trait-will-want-to-be-an-async-trait-too)

and therefore we prefer for people to write their trait and impl definitions purely in terms of `const`. If we do wind up with some more general form we can do that later.

## Examples

* [Using the `do` keyword](./generic-via-bounds-do.md)

## What if...?

### You believe that [Traits in libstd that becomes a "const trait" will want to be an "async trait" too](./axioms.md#traits-in-libstd-that-becomes-a-const-trait-will-want-to-be-an-async-trait-too)...?

...then this design area is unappealing. You would in that case want to have the trait declaration for `Default` not be `const trait Default` but something more like `do trait Default`. At that point, if you push it through, you wind up writing `do` (or whatever...) everywhere *except* on the `const fn`, and that is pretty clearly going to be more confusing in the end:

```rust
const trait Default {
    fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> do Default for Wrapper<T>
where
    T: do Default,
{
    do fn default() -> Self {
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

As you can see in the [gvb-do](./generic-via-bounds-do.md#default-trait-and-impl) example, it is kind of hard to make this coherent. You wind up with a lot of use of `do` *except* for on the `fn get_me`.
