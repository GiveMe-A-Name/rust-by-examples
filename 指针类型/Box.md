## Box

使用 `Box<T>` 将数据存储在堆上

```rust
fn main() {
    // 这里数字3是被分配到堆上的数据
    let a = Box::new(3);
    println!("a = {}", a); // a = 3

    // 下面一行代码将报错
    // let b = a + 1; // a是Box类型，1是i32类型。不能直接进行相加
}
```

我们来看看 Box 的内存布局。

```rust
// vec的内存布局
(stack)    (heap)
┌──────┐   ┌───┐
│ vec1 │──→│ 1 │
└──────┘   ├───┤
           │ 2 │
           ├───┤
           │ 3 │
           ├───┤
           │ 4 │
           └───┘

// Vec<Box<i32>> 的内存布局
                    (heap)
(stack)    (heap)   ┌───┐
┌──────┐   ┌───┐ ┌─→│ 1 │
│ vec2 │──→│B1 │─┘  └───┘
└──────┘   ├───┤    ┌───┐
           │B2 │───→│ 2 │
           ├───┤    └───┘
           │B3 │─┐  ┌───┐
           ├───┤ └─→│ 3 │
           │B4 │─┐  └───┘
           └───┘ │  ┌───┐
                 └─→│ 4 │
                    └───┘

// 可以看到引用包裹

```

### Box::leak 函数

将数据从 Box 中解体出来

```rust
fn main() {
   let s = gen_static_str();
   println!("{}", s);
}

fn gen_static_str() -> &'static str{
    // 这里是将数据从Box<&str>中解体出来
    let boxed =  Box::new("hello, world");
    Box::leak(boxed) // 返回的是&str类型
}
```
