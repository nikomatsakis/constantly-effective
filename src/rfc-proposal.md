# rfc-proposal

This is roughly what is described in the RFC.

## TL;DR

* Traits can be declared as `const`, in which case all methods are "maybe-`const`" (depending on the impl)
* Impl can decide whether it is implementing `const Trait` (const or maybe-const methods)

## Design axioms

* [The step from a monomorphic `const fn` to one with a generic bound should be minimal](./axioms.md#the-step-from-a-monomorphic-const-fn-to-one-with-a-generic-bound-should-be-minimal)
    * In this proposal, you just add `T: ~const Default`, which is easy to suggest via compiler error.
* [Most traits want to be "all or nothing" (all fns const or no fns const).](./axioms.md#most-traits-want-to-be-all-or-nothing-all-fns-const-or-no-fns-const)
    * Therefore you write `const trait Default { fn default() }` with no indication on `fn default`.
* [Adding `const` to an item should not be a breaking change](./axioms.md#adding-const-to-an-item-should-not-be-a-breaking-change)
    * In this proposal we extend this rule to include changing to `const trait Foo` and `impl const Foo`.

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

## Potential user confusion

If I write this...

```rust
const fn foo() {}
```

I have a function that can be used in or out of a const block. But if I put that `const` keyword on a generic bound...

```rust
const fn foo<T: const Default>() {

}
```

...I have a function that requires a `const` impl, which can be surprising (although it makes sense when you think about it, since trait bounds are declaring things that can be *used* by `foo`, not things that it *does*). However, it *is* backwards compatible to remove the `const` here. (It's not backwards compatible to *add* it.)

