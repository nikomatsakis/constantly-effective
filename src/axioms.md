# Design axioms

This document outlines the various design axioms that come into play. These are not all mutually satisfiable and not everyone will even agree they are all desirable. That's ok.

## The step from a monomorphic `const fn` to one with a generic bound should be minimal

It will be common for people to write something like

```rust
const fn default_wrapper<T: Default>() -> Wrapper<T> {
    Wrapper { value: T::default() }
}
```

and then realize they need `T: Default` to be a const-enabled bound. We should make the step from what they have to what they need be minimal.

## Adding `const` to an item should not be a breaking change

It is very common for people to start with a regular `fn` and then go to a `const fn`. It's nice that this is not a breaking change since it is hard to know what should be const up front and easy to overlook. In general, we should strive to have a rule that says: you can make something that was not const into something const and it should not be a breaking change.

## `const` and `async` should "feel the same"

They are more similar than they are different -- `const` lets you work in `const` blocks, `async` in `async` blocks. Users will expect to be able to sprinkle `const`/`async`/`await` in roughly the right places and have things work.

## `const` is `const`

When you see `const fn` (or any other const item), it should mean that you can use the thing in a `const` block. Not that you can "maybe" use the thing in a const block.

## We will want to have more effects than `const`

We already see the need to have (e.g.) `async Fn` and `const Fn` -- and most any trait that could be `const` could as well be `async`.

## Most very trait in libstd that becomes a "const trait" will want to be an "async trait" too

 We know we want `const Default`, but why not `async Default`? And for those where you don't want it (`async Sized`, for example), it doesn't have to have it be possible to write.
 
## We might never want more complex effects than `const`

It's interesting to think about generalizations of `const` but it's unclear that we will ever adopt those things. When we experimented with codegen effects we encountered 

## We will want a "lower floor" than `const`

We already see people requesting panic freedom. It'd be cool if allocation worked in `const` and yet we know people want to verify allocation freedom. We should be prepared for some way to have functions that do "even less" than const.

## You should be able to declare a "maybe const" function without understanding effects

Effects are kind of complex to think about. Regardless of how things work it'd be nice if you declare a "maybe const" function and think about it *just* in terms of const.

## Most traits want to be "all or nothing" (all fns const or no fns const). 

There isn't much need to have some functions that are always or never const. If you really need it, you can break up the trait into supertraits.





