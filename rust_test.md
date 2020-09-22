---
marp: true
---

# 脱線

Haskellのundefinedの型は`forall t. t`である。
多相型がある型システムにおいては、これで十分なことが多いが、Rustには多相型がない（Rustのクロージャは単相）。

```Haskell
Prelude> :set -fprint-explicit-foralls
Prelude> :t undefined
undefined :: forall t. t
```

---

# Section 6.8 関数呼び出しとメソッド呼び出し

---

# staticメソッド呼び出し

```rust
let mut numbers = Vec::new(); // static method call
```

---

# ターボフィッシュ

Rustの関数呼び出しやメソッド呼び出しで面白いのは（演者注：全く面白くないが）、`Vec<T>`のような通常のジェネリック型の構文が使えないことだ。

```rust
return Vec<i32>::with_capacity(1000); // error: something about chained comparisons
let ramp = (0 .. n).collect<Vec<i32>>(); // same error
```

この式の問題は、`<`が「小なり」比較演算子であることだ。

```rust
return Vec::<i32>::with_capacity(1000); // ok, using ::<
let ramp = (0 .. n).collect::<Vec<i32>>(); // ok, using ::<
```

シンボル`::<...>`は、Rustのコミュニティでは、愛情を込めてターボフィッシュと呼ばれている。

演者注：新しい言語なのだから丸括弧とかにすればよかったのに。。。

---

# 型推論させる方法

型パラメータを省略してコンパイラに推論させることができる場合もある。
型をコンパイラを推論できる場合は、型を省略したほうが良いと考えられている。

```rust
return Vec::with_capacity(1000); // ok, if the fn return type is Vec<i32>
let ramp: Vec<i32> = (0 .. n).collect(); // ok, variable's type is given
```



---

# Section 6.9 フィールドと要素

- 構造体のフィールドには、なじみ深い構文でアクセスできる。
- タプルのフィールドには番号が付いている。
- `.`演算子や`[]`演算子の左側が参照やスマートポインタの場合は自動的に参照解決が行われる。
- これらは代入分の左辺に現れることができるので左辺値（lvalue）と呼ばれる。


```rust
game.black_pawns // struct field
coords.1 // tuple element
pieces[i] // array element
```

---

# `..`演算子とスライス

配列やベクタからスライスを取り出す。

```rust
let second_half = &game_moves[midpoint .. end]:
```

`..`演算子はどちらのオペランドも省略できる。

```rust
.. // RangeFull
a .. // RangeFrom { start: a}
.. b // RangeTo { end:b }
a .. b // Range { start: a, end; b }
```

---

# クイックソート

```rust
fn quicksort<T: Ord><slice: &mut [T]> {
    if slice.len() <= 1 {
        return; // Nothing to sort
    }

    // Partition the slice into two parts, front and back.
    let pivot_index = partition(slice);

    // Recursively sort the front half of `slice`.
    quicksort(&mut slice[.. pivot_index]):

    // And the back half.
    quicksort(&mut slice[pivot_index + 1 ..]);
}
```

演者注：スライスの例というよりは再帰の例な気もしますが。

---

# Section 6.10 参照演算子

参照演算子`*`が必要になるのは、参照が指す値全体を読んだり書いたりしたい場合に限られる（`.`演算子には自動参照があるので）。
例えば、イテレータが参照を生成するが、参照の先にある値が必要な場合などだ。

```rust
let padovan: Vec<u64> = compute_padovan_sequence(n);
for elem in &padovan {
    draw_triangle(turtle, *elem);
}
```

この例では`elem`の型は`&u64`なので、`*elem`の型は`u64`になる。

---

# Section 6.11 （１） 算術演算子

C++やJavaなどと違う点のみを述べる。

- デバッグビルドでは、整数のオーバーフローはパニック（3章のp47）
  - チェックしたくないときは`a.wrapping_add(b)`を使う。
- 整数のゼロ割はリリースビルドでもパニック
  - `a.checked_div(b)`は`Option`を返す。
- 単項の`+`演算子はない
- 単項の`-`演算子は符号付き整数型をサポートしない
  - `-100u32`はエラー。
---

# Section 6.11 （２） ビット演算子、比較演算子

- ビットの反転（NOT）は`~`ではなく`!`
- ビット演算は比較演算子より高い優先順位（C言語と違う）
  - `x & BIT != 0`と書くと`(x & BIT) != 0`の意味
- Javaの`>>>`演算子はない
  - Rustには符号なし整数があるので不要

---

# Section 6.12 代入

- Rustでは他の言語ほどには代入は用いられない。
  - 変数はデフォルトで変更不能だからだ。
- 4章で説明した通り、デフォルトはムーブ（Copy型は例外）
- 複合代入`+=`などもサポート（詳細はp139の表）
- 代入の連鎖はサポートされていない（`a = b = 3`のように書けない）
- インクリメント演算子`++`やデクリメント演算子`--`はない

---

# Section 6.13 型キャスト

- キャストには`as`キーワードを用いる。
- 整数型から他の整数型へのキャストの挙動は完全に定義されている。
- 浮動小数点値から整数値へのキャストを行うと小数点以下が切り捨てられるが、整数型で表現できないときに未定義動作。
  - これはコンパイルのバグだ（`f64`から`f32`へのキャストも問題あり）。
- bool, char, C言語のようなenumは整数型にキャストできる
  - 逆方向へのキャストは許されない（実行時チェックが必要）
  - `std::char::from_u32()`のように`Option`型を戻す
  - 例外として`u8`は`char`にキャストできる
- 安全でないポイント型に関連するキャストも許される（21.7節）

---

# Deref自動型変換

Rustは通常は明示的キャストが必要だが、参照型に関するいくつかの型変換は自動的に（暗黙的に）行われる。

- 型`&String`の値は型`&str`に自動変換
- 型`&Vec<i32>`の値は`&[i32]`に自動変換
- 型`Box<Chessboard>`の値は&`Chessboard`に自動変換

これらはDeref自動型変換と呼ばれる。
組み込みトレイトの`Deref`を実装している型に適用されるからだ。
目的はスマートポインタ型を参照先の値と可能な限り同じように振舞わせることだ。
ユーザー定義型でも`Deref`トレイトを実装できる。
独自のスマートポイント型を書く必要があるなら13.5節。

---

# Section 6.14 クロージャ

Rustには、軽量の関数のような値、クロージャがある。
通常クロージャは、「`|`」で囲まれた引数リストと、式で構成される。

```rust
let is_even = |x| x % 2 == 0;
```

コンパイラが引数の型と返り値の型を推論してくれるが、関数の場合と同様に明示的に書くこともできる。返り値の型を書いた場合には、構文上の問題でボディ部をブロックにしなければならない。

```rust
let is_even = |x: u64| -> bool { x % 2 == 0 }; // ok
```

詳しくは14章で！（遠い）

---

# 演者注：関数の型とクロージャの型（14.2節の予習）

- 関数には`fn(&City) -> i64`のような型が付く
  - C言語の関数ポインタと同じ
- クロージャは固有の型を持つ（C++と同じ）
- 但しクロージャは`Fn`トレイトを実装している
- クロージャを引数に取る高階関数を作りたいときは、型パラメータが`Fn`トレイトで制約されたジェネリック関数を定義する

---

# Section 6.15 優先順位と結合性

省略（p138の表6-1を参照）
ビット演算が比較演算子より優先順位が高いことはこの表を見てもわかる。

---

# Section 6.16 その先へ

式は言語全体から見ると、ごく一部でしかない。

- 8章でプログラムに構造を与えるモジュールとクレートを説明
- 9章でデータに構造を与える構造体と列挙型を説明する

