# Rc & Arc

Rc 简单来说就是引用计数，让一个值可以有多个所有者

```rust
use std::rc::Rc;
fn main() {
    let a = Rc::new(String::from("hello, world"));
    let b = Rc::clone(&a);
    // a,b指向同一块内存，
    // 简单来说Rc引用计数这有两个变量引用内存
    // 只有当引用计数为0时，Rc才会被真正清除。
    assert_eq!(2, Rc::strong_count(&a));
    assert_eq!(Rc::strong_count(&a), Rc::strong_count(&b))
}
```

## Arc

简单来说 Arc 就是并发版本的 Rc，因为在多线程中我们的代码执行流时不固定的，所以在计数的过程中需要时原子并发的。
所以引入的 `Arc(Atomic Rc)`这个智能指针
