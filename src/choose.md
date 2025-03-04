# Choose your own adventure

I'm going to lay out the design space as I see it. I think the first question we have to answer is this one:

## Do we want to focus on const, or do we want to look more generally?

**Why it matters:** If we want to be more general, than we should think about how the system will scale when we consider other kinds of effects. This might mean marker effects (like panic-freedom) but it might also mean codegen effects (like async).

**Possible answers (and arguments):**

* Yes, we want const and async to feel the same.
    * If these don't work in analogous ways, it's going to be a source of surprise and confusion for users. Even if they are conceptually different, users will expect const/async/unsafe/etc to work and feel the same.
        * *Counterargument:* But const is differet, it is subtractive.
    * We already have `Fn` and `AsyncFn` and it's obvious that we are going to want `const` too. We already see the desire for iterators.
        * *Counterargument:* In practice you wind up with a lot less generic code than you think. In practice, there are always differences.
    * If you buy it, turn to [so-you-want-const-and-async-to-feel-the-same](./so-you-want-const-and-async-to-feel-the-same.md).
* No, let's just focus on const.
    * Each effect is just too different from the others. *Especially* `const`, which is a substractive effect.
        * *Counterargument:* That is a subtle distinction and people will still expect them to feel the same.
    * Our experiment with integrating effects like `async` led to horrific compilation time implications. It might be nice, but we don't know if it's possible yet.
    * Plus, if we open up too many questions, we'll never reach a decision.
        * *Niko's counterargument:* That is what leads us to scenario solving and "blunt" designs. I want sharp designs. If the process is the problem, let's not use this process.
    * If you buy it, turn to [so-you-want-to-focus-on-const-only](./so-you-want-to-focus-on-const-only.md).
