# Cell & RefCell (提高变量可变形)

该智能指针实际上是为了提高了变量的可变性。

具体是什么意思？

我们来看下面的例子

```rust
use std::cell::Cell;
fn main() {
  let string = "asdf";
  let c = Cell::new(string);
  let one = c.get();
  c.set("qwer");
  let two = c.get();
  // 你可以看到string变量本身是不可变的，
  // 但我们使用Cell进行包裹，Cell保证了内部可变性
  // 也就保证了被包裹的变量是可变的。
  println!("{},{}", one, two);
}
```

但是`Cell<T>` 适用于 T 实现 Copy 的情况.

即只能适用于 i32, float32, bool 等类型，但不适用于`Vec<T>`, `String`等类型

所以 Rust 加入了 RefCell 来提高这些变量的可变性

```rust
use std::cell::RefCell;

fn main() {
    let s = RefCell::new(String::from("hello, world"));
    let s1 = s.borrow();
    let s2 = s.borrow_mut();

    println!("{},{}", s1, s2);
}
```
