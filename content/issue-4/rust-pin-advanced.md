
# Dive into Rust Pin API

To deeply understand `Pin`, being familiar with all of `Pin`'s methods is indispensable. Excluding the nightly APIs, `Pin` has a total of 13 methods:

```rust
// Pin<P> where P: Deref
impl Pin<P> where P: Deref {
  unsafe fn new_unchecked(pointer: P) -> Pin<P> {}

  fn as_ref(&self) -> Pin<&P::Target> {}

  unsafe fn into_inner_unchecked(pin: Pin<P>) -> P {}
}

impl<P: Deref<Target: Unpin>> Pin<P> {
  fn new(pointer: P) -> Pin<P> {}

  fn into_inner(pin: Pin<P>) -> P {}
}

impl<'a, T: ?Sized> Pin<&'a T> {
  unsafe fn map_unchecked<U, F>(self, func: F) -> Pin<&'a U>
    where
        U: ?Sized,
        F: FnOnce(&T) -> &U {}

  fn get_ref(self) -> &'a T {}
}


// Pin<P> where P: DerefMut
impl<P: DerefMut> Pin<P> {
  fn as_mut(&mut self) -> Pin<&mut P::Target> {}

  fn set(&mut self, value: P::Target) where P::Target: Sized {}
}

impl<'a, T: ?Sized> Pin<&'a mut T> {
  fn into_ref(self) -> Pin<&'a T> {}

  fn get_mut(self) -> &'a mut T where T: Unpin {}

  unsafe fn get_unchecked_mut(self) -> &'a mut T {}

  unsafe fn map_unchecked_mut<U, F>(self, func: F) -> Pin<&'a mut U>
    where
        U: ?Sized,
        F: FnOnce(&mut T) -> &mut U {}
}
```

These methods can be divided into two major categories:

- `Pin<P> where P: Deref`

- `Pin<P> where P: DerefMut`

`Pin` is usually represented as `Pin<P<T>>` (`P` stands for **Pointer**, `T` stands for **Type**), so the content wrapped by `Pin` can only be smart pointers (types that implement the `Deref` trait can be called smart pointers), and makes no sense for other ordinary types. Because `&T` and `&mut T` implement `Deref` and `DerefMut` respectively, `Pin<&'a T>` and `Pin<&'a mut T>` are considered specializations of these two major categories.

At first glance, these 13 methods seem a bit chaotic, but their design is actually very deliberate, and even exhibit symmetry. Categorizing by functionality, these methods can be divided into 5 major classes, each subdivided into 2~3 kinds by mutability or conformance to the `T: Unpin` bound. Mutable versions all end in `_mut`, and unsafe versions that don't conform to `T: Unpin` all contain `_unchecked`.

| Functionality                        | Methods                                           | Notes                                                         |
| ------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------ |
| **Constructing `Pin`**                        | `new()` / `new_unchecked()`                  | The safe and unsafe versions are distinguished by whether they satisfy the `T: Unpin` bound. |
| **Converting `Pin`**                     | `as_ref()` / `as_mut()`                           | Convert `&/&mut Pin<P<T>>` into `Pin<&/&mut T>`。               |
| **Get borrow of `T` from `Pin<P<T>>`**  | `get_ref()` / `get_mut()` / `get_unchecked_mut()` | Consume ownership and get the borrow of `T`. There are two versions according to mutability. Because `&mut T` is danger (easy be moved), `get_mut()` distinguishes between safe and unsafe versions by whether they satisfy the `T: Unpin` bound.|
| **Consuming `Pin` to get the inner `P`** | `into_inner()` / `into_inner_unchecked()`         | The safe and unsafe versions are distinguished by whether they satisfy the `T: Unpin` bound. In addition, to avoid conflicts with `P`'s own `into` methods, these apis are designed to be static methods that must be called in the form `Pin::into inner()`, not `pin.into_inner()`. |
| **Pin projection**                    | `map_unchecked()` / `map_unchecked_mut()`         | Usually used for Pin projection. |


# Pin Additional attributes

## `#[fundamental]`

## `#[repr(transparent)]`

# Pin implementation traits

## `Unpin`

## `Deref` and `DerefMut`

## `Future`

# `Unpin` and `Future`

## `Unpin` a safe trait

## Why `Future` can be `Unpin`

## `Pin`'s `Future` implementation

## Why need `Unpin` trait bound

# Other scenarios requiring `Pin`

## Intrusive collections


## Others

# Conclusion





