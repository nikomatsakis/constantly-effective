# From const to generic

You start with a const fn and a `T: Default`:

```rust
const fn compute_something<T: Default>() {
    let t = T::default();
}
```

and you get an error...what does it suggest you to do and how confusing is it?