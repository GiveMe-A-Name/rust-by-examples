# 闭包

## 闭包的特征类型有三种

```rust
// 1. FnOnce() -> ()
#[warn(dead_code)]
fn fn_once<F>(func: F)
where
    F: FnOnce(usize) -> bool,
{
    println!("{}", func(3));
    // FnOnce(usize) -> bool 该类型的闭包会拿走被捕获变量的所有权
    // 只能使用一次,无法使用第二次
    // println!("{}", func(4));
}
// 2. FnMut() -> ()
#[warn(dead_code)]
fn exec_FnMut<'a, F>(mut f: F)
where
    F: FnMut(&'a str),
{
    // 它以可变借用的方式捕获了环境中的值
    f("hello")
}
// 3. Fn() -> ()
// 它以不可变借用的方式捕获环境中的值
fn exec_Fn<'a, F: Fn(&'a str)>(mut f: F) {
    f("hello")
}
```

## move 和 FnOnce 的关系

move 可以强制闭包取得捕获变量的所有权

move 的用法通常用于闭包的生命周期大于捕获变量的生命周期时，例如将闭包返回或移入其他线程。

```rust
fn factory(x: i32) -> Box<dyn FnOnce(i32) -> i32> {
    let num = 5;

    // num是函数的局部变量, 因为要返回出去， 所以需要使用move进行强制获取
    Box::new(move |x| x + num)
}
```

## 三种闭包特征的关系

```rust
fn main() {
    let s = String::new();

    // 实际上这里取决于update_string闭包对捕捉变量s的使用方式来
    // 检测他使用于哪一种闭包特征
    // update_string仅仅是使用了他的借用就可以满足闭包
    // 所以他满足了FnOnce, FnMut, Fn三种都满足
    let update_string =  || println!("{}",s);

    exec(update_string);
    exec1(update_string);
    exec2(update_string);
}

fn exec<F: FnOnce()>(f: F)  {
    f()
}

fn exec1<F: FnMut()>(mut f: F)  {
    f()
}

fn exec2<F: Fn()>(f: F)  {
    f()
}

```

实际上实现了 Fn 特征的闭包，一定实现了 FnOnce 闭包，这个源代码代码中能体现出来。

```rust
// 满足Fn一定实现FnMut
pub trait Fn<Args> : FnMut<Args> {
    extern "rust-call" fn call(&self, args: Args) -> Self::Output;
}

// 满足FnMut一定实现FnOnce
pub trait FnMut<Args> : FnOnce<Args> {
    extern "rust-call" fn call_mut(&mut self, args: Args) -> Self::Output;
}

pub trait FnOnce<Args> {
    type Output;

    extern "rust-call" fn call_once(self, args: Args) -> Self::Output;
}
```

**我们记住一个原则即可： 一个闭包实现了哪种 Fn 特征取决于该闭包如何使用被捕获的变量，而不是取决于闭包如何捕获它们**
