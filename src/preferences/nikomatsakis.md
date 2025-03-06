# nikomatsakis

I am pretty keen on the [quadrant `(const)`](../quadrant-paren-const.md) syntax and semantics, which seems to cover all the bases. The rationale below is mostly accurate but not fully updated with latest thoughts.

## outdated:

I haven't seen a proposal I truly *like* yet, but I do have some thoughts. The first is that I believe

* [`const` and `async` should "feel the same"](../axioms.md#const-and-async-should-feel-the-same)

Today we have the ability to declare functions with given effects in traits (notably async):

```rust
trait Service {
    type Response;
    async fn request(&self) -> Self::Response;
}
```

This implies to me that we should permit `const` functions as well. This seems like it would clearly be useful and is analogous to supporting `const` items:

```rust
trait Service {
    type Response;

    /// Given the hash of the inputs, create a hash
    /// that uniquely identifies this service.
    const fn request_hash(input_hash: u64) -> u64;

    async fn request(&self) -> Self::Response;
}
```

I also believe

* [Traits in libstd that becomes a "const trait" will want to be an "async trait" too](../axioms.md#traits-in-libstd-that-becomes-a-const-trait-will-want-to-be-an-async-trait-too)

I would be very interested in counter examples, but from what I can tell, a const fn is just a "very simple" function, which means that it can be used in virtually any environment. I think we have tons of combinators and methods that fit this description, as well as a number of traits, all of which seem to me like they would make sense in an async context:

```rust
trait async Default {
    async fn default() -> Self;
}

trait async Debug {
    async fn debug(
        &self, 
        fmt: &mut std::fmt::Formatter<'_>,
    ) -> Self;
}

trait async Iterator {
    type Item;

    async fn next(&mut self) -> Option<Self::Item>;
}
```

Not only does it make sense to me to have these traits be potentially async, it makes sense for them to be potentially "try" as well. (Relevant to this, I think it is a key axiom for async that you can take sync code, add "async" and "await" everywhere, and it should do the same--including dropping things at the same time, etc. This implies to me that it would be very wrong to have e.g. an async iterator that, when you execute collect, actually executed concurrently. That is a distinct trait.)

It also seems true that

* [We will want to have more effects than `const`](../axioms.md#we-will-want-to-have-more-effects-than-const) and
* [We will want a "lower floor" than `const`](../axioms#we-will-want-a-lower-floor-than-const).

In particular, I think that *marker effects* are going to be huge. When Esteban was exploring redpen, one thing that he found was that the vast majority of lints boiled down to "make sure that you don't call X from Y", which is basically saying "please give me effects"[^linear]

[^linear]: The second most common category of lints was "make sure that you DO call X eventually", which is linear types, but never mind. 

I am also very cognizant that effects, if done poorly, are going to push Rust over the edge. To that end, I believe that

* [You should be able to declare a "maybe const" function without understanding effects](../axioms.md#you-should-be-able-to-declare-a-maybe-const-function-without-understanding-effects)

would be a great goal, although I'm not sure I'd call it a requirement. I also think that

* [Twiddle const is where Rust jumps the shark](../axioms.md#twiddle-const-is-where-rust-jumps-the-shark)

## Things I do not believe or do believe but don't care

I do believe this is true:

* [Most traits want to be "all or nothing" (all fns const or no fns const).](../axioms.md#most-traits-want-to-be-all-or-nothing-all-fns-const-or-no-fns-const) 

but I also think that most traits that are going to be generic over effects have relatively few method. To me the consistency of actively annotating methods that diverge from "default" effects wins over the overhead of having to add annotations to a bunch of functions.
