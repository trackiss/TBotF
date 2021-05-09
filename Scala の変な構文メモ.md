## Scala の変なルールやシンタックスシュガーまとめ

<br>

---

<br>

---

<br>

### タプルのコレクションをイテレートしたいとき (match の省略記法)

```scala
val ts = List((0, 1, 2), (3, 4, 5), (6, 7, 8))
```

この中身すべてをインクリメントしたいとき、次のように書きたくなるものだが...

```scala
val tsplus = ts map { (a, b, c) => (a + 1, b + 1, c + 1) }
```

当然、不可能である。map が気を利かせて自動でタプルを分解してくれたりはしない。  
したがって、次のように書く必要がある (宗教上の理由により、タプルには添字アクセスをすることはできないものとする):

```scala
val tsplus = ts map {
  _ match {
    case (a, b, c) => (a + 1, b + 1, c + 1)
  }
}
```

うーん、冗長である。  
でも Scala には、こんな状況に対応できるシンタックスシュガーがある:

```scala
val tsplus = ts map {
  case (a, b, c) => (a + 1, b + 1, c + 1)
}
```

なんと、case が書けるのは match 式の中だけではなかったのである！ これは便利だ。

<br>

---

<br>

### クラス内クラスをインスタンス依存ではなくクラス依存としたいとき (\# シャープ)

```scala
class Foo {
  class Bar

  def hoge(bar: Bar): Unit = ???
}
```

こういうクラスがあるとする。ここで、次のコードはエラーになる:

```scala
val foo1 = new Foo
val foo2 = new Foo

val bar1 = new foo1.Bar
val bar2 = new foo2.Bar

foo1 hoge bar1  // OK!
foo1 hoge bar2  // Error!
```

型の不一致だ。  
なぜなら、`foo1.hoge()` が求めているのはあくまで `foo1.Bar` であり、`foo2.Bar` ではないからである。  
もちろん逆も同じで、`foo2.hoge()` は `foo2.Bar` を求めている。

要するに、クラス内クラスの型というのは、その外側のクラスの型ではなくインスタンスに依存しているのである (だから、たとえば `val bar = new Foo.Bar` みたいなことはできない)。たとえ宣言は同じでも、別のインスタンスであればそれは別の型として扱われる。  
これをどうにかするために、`#` という記号が Scala にはある:

```scala
class Foo {
  class Bar

  def hoge(bar: Foo#Bar): Unit = ???
}

val foo1 = new Foo
val foo2 = new Foo

val bar1 = new foo1.Bar
val bar2 = new foo2.Bar

foo1 hoge bar2  // OK!
foo2 hoge bar1  // OK!
```

こうすることで、インスタンスではなくクラスに依存したクラス内クラスの型を表現することができる。
