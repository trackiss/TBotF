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
val tsplus = ts map { (a, b, c) =>
  (a + 1, b + 1, c + 1)
}
```

当然、不可能である。ラムダ式が気を利かせて自動でタプルを分解してくれたりはしない。  
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

なんと、case が書けるのは match 式の中だけではなかったのだ。先ほどのコードに case を付けるだけでいい。便利。  
もちろん、あくまで match 式の省略記法なので、ほかのパターンも並べてパターンマッチングができる。

<br>

---

<br>

### 内部クラスをインスタンス依存ではなくクラス依存としたいとき (\# シャープ)

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

内部クラスの型は、そのクラスがどういった経路でインスタンス化されたかどうかに依存する。つまり、外側のクラスにそのものに所属するのではなく、各インスタンスに固有のものとなるのである (だから、たとえば `val bar = new Foo.Bar` みたいなことはできない)。  
これを **パス依存型 (path-dependent type)** という。

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

foo1 hoge bar1  // OK!
foo1 hoge bar2  // OK!
```

こうすることで、内部クラスの型をパス依存でなくクラスに属するものとして扱うことができる。

<br>

---

<br>

### final case class について

```scala
class Foo {
  final case class Bar()
}

val foo1 = new Foo
val foo2 = new Foo

val bar1 = foo1.Bar()
val bar2 = foo2.Bar()

println(bar1 == bar2)  // true
```

上記のコードを Scala 2.x で実行すると、1つの警告とともに true が表示される。これは本来ありえない挙動である。  
内部クラスの型は通常 パス依存型であるから、異なるインスタンスに所属する内部クラスは別の型である (`foo1.Bar` と `foo2.Bar` は別の型だ)。

警告の内容は、`The outer reference in this type test cannot be checked at run time.` だ。ともかく、実行時に型検査ができないということを言いたいらしい。  
この問題は10年以上も前から既知の不具合として知られているもので、Scala 3 にてようやく改善されている。ちゃんと false になるし、警告も出ない。

そもそも Scala の内部クラスは、外側のクラスインスタンスへのポインター (外部参照) を持っている。Java にも内部クラスは存在するが、こちらはパス依存型ではない。したがって、外部参照を比較することで疑似的にパス依存型を再現しているのだ。  
これが、(GitHub の Issue を読んでみてもあまり意味がわからなかったが) final がつくとどういうわけか外部参照を持たないようになっていたらしい。関連したバグもあるにはあったのだが、メモリ節約がどうとかで半ば仕様っぽくもあった... のかな...？ 英語力がないのでわからない。

ともかく、Scala 3 に乗り換えるか、final を外して sealed にするなりしよう。
