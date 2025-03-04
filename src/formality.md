# Formality model

This section defines a formal model. This is not a proposal for syntax to be added to the Rust language. It allows us to talk uniformly about what is happening. It is intended to cover a superset of functionality we might someday want.

We define an effect `E` as a set of `runtime` and associated effects `<T as Trait>::Effect`:

```
E = panic
  | loop
  | runtime
  | await
  | throw<T>
  | yield<T>
  | <P0 as Trait<P1...>>::Effect
  | union(E0, ...., En)
```

Rust "keywords" and funtion declarations can be translated to sets of these primitive effects:

* a `const fn` can have the effects `union(panic, loop)`
* a `fn` can have the `union(panic, loop, runtime)` (we also call this set of effects "default")
* an `async fn` can have the effects `union(await, default)`
* a `gen fn`, if we had that, would have the effect `union(yield<T>, default)`

Traits are extended with the ability to define associated effects, declared with `do Effect`:

```rust
trait Foo {
    do Effect;
}
```

Trait references can *bound* an associated effect by writing

```rust
T: Foo<Effect: E>
```

which means "the associated effect `Effect` is a subset of `E`".

Functions and methods can be declared with a set of effects, written (for now) after the `do` keyword:

```rust
fn foo<T: Default>()
do
    runtime,
{
    ...
}
```

The effect checker enforces:

* if the function body does something that is known not to be const compatible, it must have `runtime` effect;
* when the function calls functions etc., it must have a known superset of their effects declared.

## "Marker" effects vs "codegen" effects

In the space of effects we can separate out two categories.

* *Marker* effects do not require changes to the compiler codegen. They indicate actions that are or are not allowed. Examples include `runtime`, `panic`, and `loop`.
* *Codegen* effects require changes to the compiler. An example would be `await` or `throw<T>`.

### Codegen impacts

When a function is compiled that has the `await` or `yield` effects, it is compiled using the coroutine transform. The actual function call returns the coroutine.

When a function is compiled that has the `throw<T>` effect, it is compiled to return a `Result`, with the result being `Ok`-wrapped (we can generalize this with the `Try` trait if we wish).

When a function is called that has the `await` or `yield` effects and the containing function has those effects, the result is either "awaited" or "yield*"'d.

