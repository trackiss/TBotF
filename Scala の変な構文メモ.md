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

### クラス内クラスを別インスタンスでも型解決できるようにしたいとき (\# シャープ)

```scala
class Foo {
  class Bar
  val bar = new Bar

  def hoge(bar: Bar): Unit = ???
}
```

こういうクラスがあるとする。ここで、次のコードはエラーになる:

```scala
val foo1 = new Foo
val foo2 = new Foo

foo1 hoge foo2.bar  // エラー！
```

型の不一致である。  
なぜなら、`foo1.hoge()` が求めているのはあくまで `foo1.Bar` であり、`foo2.Bar` ではないからである。  
もちろん逆も同じで、`foo2.hoge()` は `foo2.Bar` を求めている。

要するに、クラス内クラスの型同一性はあくまでそのインスタンスのスコープ内に限られているということである。  
これをどうにかするために、`#` という記号が Scala にはある:

```scala
class Foo {
  class Bar
  val bar = new Bar

  def hoge(bar: Foo#Bar): Unit = ???
}

val foo1 = new Foo
val foo2 = new Foo

foo1 hoge foo2.bar
```

こうすることで、クラス内クラスの同一性はインスタンスではなくクラスに依存するようになる。
