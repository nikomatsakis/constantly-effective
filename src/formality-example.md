Here are examples written in the [formality](./formality.md) dialect. We'll reference these when discussion the options.

## Default trait and impl

Here the model is having the `Default` trait that is usable in different contexts, e.g., `const Default` and `async Default`. There is also an impl of `Wrapper<T>` and then a function that calls it

```rust
trait Default {
    do DefaultEffect;

    fn default() -> Self
    do
        Self::DefaultEffect;
}

struct Wrapper<T> {
    value: T
}

impl<T> Default for Wrapper<T>
where
    T: Default,
{
    do DefaultEffect = T::DefaultEffect;

    fn default() -> Self
    do
        Self::DefaultEffect
    {
        Wrapper {
            value: T::default()
        }
    }
}

fn get_me<T>(x: Option<Wrapper<T>>) -> Wrapper<T>
where
    T: Default,
do
    T::DefaultEffect,
{
    match x {
        None => <Wrapper<T>>::default(),
        Some(v) => v,
    }
}
```