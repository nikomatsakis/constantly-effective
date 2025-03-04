# "Everywhere"-style proposals

## Summary

Everywhere-style proposals have a keyword that represents "some set of effects". This keyword is used consistently...

* on the trait, to indicate that it may contain effect-generic methods
* on functions that are generic over effects
* in an impl and in trait bounds, to indicate the effects of the methods defined in the trait

Question mark:

* When you call a function that is effect-generic, do you need to acknowledge that?

## Design axioms

* **"const and async should feel the same"**. They are more similar than they are different -- const lets you work in const blocks, async in async blocks. Users will expect to be able to sprinkle const/async/await in roughly the right places and have things work.
    * Just as we support `trait Foo { async fn foo() }` we therefore support `trait Foo { const fn foo() }`.
* const-is-const
    * when you say that something is const it means you can use it in a const block
* but we want a syntax that COULD be just about const
    * just in case we never go further
* you should be able to declare a maybe-const function without understanding effects
* the most common pattern will be `X Trait` where all fns become `X`
* every trait in libstd that becomes a "const trait" will want to be an "async trait" too
    * and for the rest, it won't hurt them





