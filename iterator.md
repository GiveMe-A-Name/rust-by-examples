# 迭代器

在 Rust 中，迭代器是惰性的，意味着如果你不使用它，那么它将不会发生任何事

```rust

let v1 = vec![1, 2, 3];

// 惰性迭代器
let v1_iter = v1.iter(); // 创建一个迭代器，他并不会发生任何事情，所以也不会消耗额外的性能

// 数组实现了 IntoIterator 特征，Rust 通过 for 语法糖，自动把实现了该特征的数组类型转换为迭代器（你也可以为自己的集合类型实现此特征），最终让我们可以直接对一个数组进行迭代，
// 只有在使用迭代器时，才会消耗性能
for val in v1_iter {
    println!("{}", val);
}

```

我们来查看下迭代器具体实现了什么 trait

```rust
// 所有迭代器都实现了Iterator特征，他内部有一个next函数来迭代取出元素
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // 省略其余有默认实现的方法
}
```

所以我们也可以使用 next 方法来手写出 for 的效果,实际上你可以把 `for..in..`当成语法糖

## into_iter, iter, iter_mut 的区别

这三种方法都可以将数组等类型转换为迭代器，但有一定的区别

```rust
let v1 = vec![1,2,3];

v1.into_iter() //会夺走所有权
v1.iter() //是借用
v1.iter_mut() // 是可变借用

/*
  他们的方法签名分别是：
  1. into_iter(self)
  2. iter(&self)
  3. iter_mut(&mut self)
*/

```

## Iterator 和 IntoIterator 的区别

这两个其实还蛮容易搞混的，但我们只需要记住，Iterator 就是迭代器特征，只有实现了它才能称为迭代器，才能调用 next。

而 IntoIterator 强调的是某一个类型如果实现了该特征，它可以通过 into_iter，iter 等方法变成一个迭代器。

## 消费者与适配器

消费器会使用 next 方法来消费掉迭代器的，所以一个迭代器只能被消费一次。

迭代器是惰性的，也是一次性的。

```rust
fn main() {
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();

    assert_eq!(total, 6);

    // v1_iter 是借用了 v1，因此 v1 可以照常使用
    println!("{:?}",v1);

    // 以下代码会报错，因为sum已经把v1_iter消耗掉了
    // println!("{:?}",v1_iter);
}

```

适配器则会返回一个新的迭代器，他负责转换迭代器里的元素

```rust
let v1: Vec<i32> = vec![1, 2, 3];

// .map()是一个适配器，传入一个闭包函数，负责映射迭代器里的元素，
// 但这种转换是惰性的，使用当他消费的时候才会真正显现出来
let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```

## 迭代器是 Rust 的 零成本抽象，意味着抽象并不会引入运行时开销。
