# So you want const and async to feel the same...

Despite being very different in the details, `const` and `async` feel very similar at a high-level. They both indicate a function that can only be executed in a particular context. For `const`, it is a `const` block. For `async`, it is an `async` block. (Note that an `async` function can be *called* from outside an `async` block, but it doesn't actually *execute* until you `await` the future.)

Let's look at how async works. You can declare both top-level fns and trait fns as `async`:

```rust
async fn do_something();

trait Service {
    async fn fetch(&self);
}
```

An async fn is, of course, one that returns a future, but put a bit more generally, it's a function that is "fully usable only in an async block". `const` is similar: it indicates a function that can only be called ("fully used") from a const block. By analogy with `async` fn
By analogy, then, we should expect the ability to create `const` fns in traits:

```rust
trait Process {
    const fn id() -> &'static str;
}
```

What would const mean then? I think it means "the ability to be used in a const block" (or other const context). So you ought to be able to call `id`:

```rust
fn foo<T: Service>() {
    let x: &'static str = const { T::id() }
}
```

This all feels right, because it works the same way as `async` and `const` fn items:
