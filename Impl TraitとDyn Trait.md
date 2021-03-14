---
marp: true
---

# Impl TraitとDyn Trait

---

## Impl Trait

- [RFC 1951](https://github.com/rust-lang/rfcs/blob/master/text/1951-expand-impl-trait.md) （2017/03/12）で提案
- [Rust 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html)（2018/05/10）で安定化された機能
- この`impl`は静的ディスパッチであることを意味するキーワード
- 戻り値位置で使用された場合は存在型（∃）
  - トレイトを満たす「ある」型
- 引数位置で使用された場合は全称的（∀）
  - トレイトを満たす「任意の」型

---

## 戻り値位置で使用された場合は存在型（∃）

```rust
fn foo() -> impl Trait {
    // ...
}
```

- この関数`foo`の戻り値は`Trait`を満たす「ある型」
- 戻り値型は静的（コンパイル時）に決定している必要がある
- `Box<Trait>`と異なりヒープに確保されることがない

---

## 具体例１（クロージャを戻す関数）

Rust 1.26以前はクロージャを戻すときはヒープに確保しなければならなかったが、`impl`を使うことで回避できる。

```rust
// before
fn foo() -> Box<Fn(i32) -> i32> {
    Box::new(|x| x + 1)
}

// after
fn foo() -> impl Fn(i32) -> i32 {
    |x| x + 1
}
```

コンパイル時に型が確定しているのであれば、動的ディスパッチの必要がない。

---

## 具体例２（イテレータを戻す関数）

Rust 1.26以前はイテレータを戻す関数の戻り値は長い型名を書く必要があったが、書かなくてよくなる。

```rust
fn foo() -> impl Iterator<Item = i32> {
    vec![1, 2, 3]
        .into_iter()
        .map(|x| x + 1)
        .filter(|x| x % 2 == 0)
}
```

コンパイル時に型が確定しているのであれば、動的ディスパッチの必要がない。

---

## C++14の戻り値型推論に似ている？

次のように書くとヒープに確保してしまう。

```C++
std::function<int(int)> makeFunc()
{
  return [](int x){ return x + 1; };
}
```

静的に型が決まるのならば次のように書いたほうが良い。

```C++
decltype(auto) makeFunc()
{
  return [](int x){ return x + 1; };
}
```

この戻り値型についてコンセプト（C++20）で縛ることができるかどうかは演者は勉強不足で分からない（勉強しろ）。

---

# 動的ディスパッチの必要がある場合は従来通り

実行時にならないと型が決まらない場合は`impl`は使えない（当たり前）。

```rust
fn foo(x: i32) -> Box<Iterator<Item = i32>> {
    let iter = vec![1, 2, 3]
        .into_iter()
        .map(|x| x + 1);

    if x % 2 == 0 {
        Box::new(iter.filter(|x| x % 2 == 0))
    } else {
        Box::new(iter)
    }
}
```


---


## 引数位置で使用された場合は全称的（∀）

構文上は引数としても`impl`を使うことができるが、あまり利益はない（たぶん）。

```rust
// before
fn foo<T: Trait>(x: T) {

// after
fn foo(x: impl Trait) {
```

- 上の２つはほぼ同じ
  - `impl`版は実装で`T`を使えないが
- `impl`キーワードの意味は「型が静的に決まる」ということ
- 全称的（∀）であって存在型（∃）ではないことに注意
  - `foo`の引数の型は`Trait`を満たす「任意の」型

---

## dyn trait導入

- [RFC 2113](https://github.com/rust-lang/rfcs/blob/master/text/2113-dyn-trait-syntax.md)（2017/03/12）で提案
- [Rust 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html)（2018/05/10）で安定化
- `impl`（静的ディスパッチ）と平仄をとるために以下の変更
  - `Box<Trait>` → `Box<dyn Trait>`
  - `&Trait` -> `&dyn Trait`
  - `&mut Trait` -> `&mut dyn Trait`
- bare traitは非推奨

---

## dyn trait導入のメリット

- 教育的・人間工学的に優れている
  - impl traitは静的ディスパッチ
  - dyn trait（旧bare trait）は動的ディスパッチ
- トレイト（`Trait`）とトレイトオブジェクト（`dyn Trait`）の区別を明示
- 「dynがデフォルトでimplがニッチな機能」という誤解を避ける
  - implを使えるところではimplを使うべし
- エラーメッセージが改善される
  - bare traitは本来の意図がわかりにくい
    - implの書き忘れかもしれない
    - `fn`と`Fn`の書き間違えなどもある

---

# 参考

[Announcing Rust 1.26](https://blog.rust-lang.org/2018/05/10/Rust-1.26.html)

[Rust Edition Guide 3.5.1 impl Trait for returning complex types with ease](https://doc.rust-lang.org/edition-guide/rust-2018/trait-system/impl-trait-for-returning-complex-types-with-ease.html)

[RFC 1951](https://github.com/rust-lang/rfcs/blob/master/text/1951-expand-impl-trait.md) - impl Traitの話

[RFC 2113](https://github.com/rust-lang/rfcs/blob/master/text/2113-dyn-trait-syntax.md) - dyn Traitの話





