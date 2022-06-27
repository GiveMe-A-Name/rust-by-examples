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

这看来是违反了 Rust 的规则，Rust 一段范围内只有一个可变变量的规则是为了保证多线程情况下，只能有一个线程能对数据进行修改。

使用 RefCell 可以逃过编译器的检测，Rust 选择把控制数据安全的权利交给你，让你自己去保证多线程情况下数据安全

## 总结

Cell 和 RefCell 都为我们带来了内部可变性这个重要特性，同时还将借用规则的检查从编译期推迟到运行期，但是这个检查并不能被绕过，该来早晚还是会来，RefCell 在运行期的报错会造成 panic。

从性能上看，RefCell 由于是非线程安全的，因此无需保证原子性，性能虽然有一点损耗，但是依然非常好，而 Cell 则完全不存在任何额外的性能损耗。
