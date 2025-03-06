# Generic via bounds

In the "generic via bounds" approach, you declare 

* direct effects on the function or item (e.g., `const fn`);
* but you have have special bounds that potentially have other effects.

## Design axioms

As in the [RFC proposal](./rfc-proposal.md), we wish to ensure

* The step from a monomorphivc `const fn` to one with a generic bound should be minimal

but we also wish 

## Notes

It's hard to make this coherent, though. You have 

There is a point of tension here. When you wish to declare a "potentially generic" trait, you hit a challenge. Is it...

```rust
const trait Foo {
    fn foo();
}
```