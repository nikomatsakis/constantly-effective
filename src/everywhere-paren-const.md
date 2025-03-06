# `(const)` everywhere

## TL;DR

* Write `const` to mean "can only do const things"
* Write `(const)` to mean "can only directly do const things, but may do non-const things via trait bounds"

## Design axioms

* We will want a lower floor than `const` eventually
    * What we've learned from the `?Sized` experience is that it's best to have people "ask for the things they need".
* You should be able to declare a maybe-const function without understanding effects.
    * Therefore, we prefer a `const`-keyword-based syntax.

## Default trait and impl

Equivalent of [default trait and impl](./formality-example.md#default-trait-and-impl):

```rust
(const) trait Default {
    (const) fn default() -> Self;
}

struct Wrapper<T> {
    value: T
}

impl<T> Default for Wrapper<T>
where
    T: (const) Default,
{
    (const) fn default() -> Self {
        Wrapper {
            value: T::default()
        }
    }
}

(const) fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: (const) Default,
{
    match x {
        None => <Wrapper<T>>::default(), // <-- do we need something here?
        Some(v) => v,
    }
}
```

## Variations

I'm not persuaded by putting the `(const)` before the keyword `trait`:

```rust
(const) trait Default {
    (const) fn default() -> Self;
}
```

It seems inconsistent with how the `impl` works (`impl (const) Default`) and how bounds work ()`T: const Default`). I would therefore expect the following:

```rust
trait (const) Default {
    (const) fn default() -> Self;
}
```

Question though, what does it mean to write `(const)` anyway? It indicates the "floor" for default functions? But that is indicated on a per-function basis.

Another option is to just say that the trait is "generic over const" if it has methods that are; that has a downside in that it rules out the ability to have a trait that has "just" a `(const) fn` method:

```rust
trait Default {
    (const) fn foo<T: (const) Debug>() -> Self;
}
```

All of this makes me wonder if we can come up with a syntax for more explicitly "tying" things together, but I'm not sure what it would be. For example `(trait)` or `(fn)` or even `const(if trait)`...? Or `T: (fn) Debug`, which would mean "add this to the effects of the function"...?
