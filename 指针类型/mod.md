# 智能指针

## 智能指针是啥？

通常情况下，指针是一个包含了内存地址的变量，该内存地址引用或者指向了另外的数据。

而智能指针则不然，它虽然也号称指针，但是它是一个复杂的家伙：通过比引用更复杂的数据结构，包含比引用更多的信息，例如元数据，当前长度，最大可用长度等。

**我们常用的智能指针有 Box, RC, Arc, Cell, RefCell 等**，实际上我们的智能指针往往是基于结构体实现，它与我们自定义的结构体最大的区别在于它实现了 Deref 和 Drop 特征：

- **Deref** 当给智能指针解引用时，实际上会默默调用 Deref 特征中的 deref 方法

```rust
// 当我们对智能指针 Box 进行解引用时，实际上 Rust 为我们调用了以下方法：

use std::ops::Deref;

struct MyBox<T>(T);

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0
    }
}

let mybox = &MyBox(13i32);

let number = *mybox // 实际上会默默的转换为*(mybox.deref())
// 所以这里解用的是 &i32, 即返回的是i32类型
```

实际上除了在解引用会默默调用 deref 方法，在函数或方法传递引用时会根据参数签名来智能调用 deref 方法。

```rust
fn main() {
    let s = String::from("hello world");
    // String类型实现了Deref特征，可以转换为&str
    display(&s)
}

// 在传递时自动调用了 (&s).deref() 返回的 &str 类型
fn display(s: &str) {
    println!("{}",s);
}
```

再看下面的例子

```rust
fn main() {
    let s = MyBox::new(String::from("hello world"));
    // Deref 可以支持连续的隐式转换，直到找到适合的形式为止：
    // MyBox持续的隐式转换，直到转换到&str为止
    display(&s)
}

fn display(s: &str) {
    println!("{}",s);
}
```

- **Drop** 智能指针实现了 Drop 特征，在一个变量超出作用域时将会自动调用 drop 方法进行收尾工作和资源释放。
