## Kotlin Tutorial

### IntelliJ IDEA のインストール

[こちら](https://www.jetbrains.com/ja-jp/idea/) から、IntelliJ IDEA Community をダウンロードしてインストール。

初回起動時はいろいろ訊かれるので、自力で進む。

日本語化したいなら、Japanese Language Pack EAP プラグインをインストールする。まだアーリーアクセスなので完全ではないが、公式の日本語訳である。  
その他のプラグインは、Rainbow Brackets や Material Theme などがおすすめ。Rainbow Brackets は、ペアとなっている丸括弧や波括弧を色分けしてくれる。Material Theme は、IDE の見た目をかっこよくしてくれる。別になくてもいい。

### 新規プロジェクトの作成

"新規プロジェクト" をクリックしたら、左のペインから "Gradle" を選択。  
"Java" のチェックを外し、"Kotlin/JVM" にチェックを入れ "次" へ。

任意のプロジェクト名を入力して "完了"。

### Hello, World!

次のコードで、おなじみの Hello World を実行できる。

```kotlin
fun main(args: Array<String>): Unit {
    println("Hello, World!")
}
```

Kotlin は、関数などをクラスに含めず記述することができる。気持ち悪いがそういう仕様なのでしかたない。

関数は `fun 関数名(引数名: 引数の型名): 関数の型名 { 処理 }` という構文になっている。  
返り値がない場合は、`void` ではなく `Unit` 型で示す。

### 変数

Kotlin では、基本的に `val` キーワードを使って変数を宣言する。  
`val` キーワードを使って宣言された変数は不変 (immutable) であり、再代入することができない。

Kotlin は静的型付け言語ではあるが、右値から型を推測してくれる (型推論) ため、型アノテーション (`: 型名`)は書かなくともよい。

```kotlin
val a: Int = 10
val b = "Hello"

a = 20  // Compile Error!
```

可変 (mutable) な変数を宣言する場合は、`var` キーワードを使って宣言する。  
もっとも、必要でないかぎりはイミュータブルな変数を使うべきである。

```kotlin
var a = 10

a = 20  // OK!
```

変数名は、`myVariable`、`helloWorld` のように、camelCase で記述する (先頭の単語は小文字、以降の単語は大文字で始める)。

### 定数

コンパイル時に解決される定数を使う場合は、`const val` キーワードを使って宣言する。

```kotlin
const val MY_CONSTANTS = "Hello, World!"
```

定数名は、`MY_CONSTANTS`、`HELLO_WORLD` のように、CONSTANT_CASE で記述する (すべて大文字、単語はアンダースコアで区切る)。

### Nullable

Kotlin は、デフォルトではすべての型において null の代入を禁じている。

null を許容する (nulleble) 型は、`?` 接尾辞を付与する。

```kotlin
val a: String = null  // Compile Error!
val b: String? = null // OK!
```

Nullable な型は、同じ Nullable でない型と互換性がない。たとえば、String 型と String? 型はまったくの別物として扱われる。

次のように、Nullable なオブジェクトのプロパティやメソッドを呼び出すことはできない。

```kotlin
val a: String = "Hello"
val b: String? = "Hello"

println(a.length) // OK!
println(b.length) // Compile Error!
```

Nullable なオブジェクトのプロパティやメソッドを呼び出すには、セーフタイプ演算子 `?.` を使用する。  
`?.` を使って呼び出されたプロパティやメソッドは、もし値が null だった場合は、評価されずにそのまま null を返す。ほかの言語における `map()` に相当する。

```kotlin
val a: String = "Hello"
val b: String? = "Hello"

println(a.length)   // "5"
println(b?.length)  // "null"
```

Nullable なオブジェクトを Non-null なオブジェクトに変換するには、エルビス演算子 `?:` または `!!` 演算子を使用する。

`?:` 演算子は、`Nullableオブジェクト ?: Non-nullオブジェクト` にように使用する。すなわち、もし Nullable なオブジェクトが null でない場合はそのままオブジェクトを、null である場合はかわりに右値を返す。ほかの言語における `getOrElse()` に相当する。

```kotlin
val a: String? = "Hello"
val b: String? = null

println(a.length ?: -1) // "5"
println(b.length ?: -1) // "-1"
```

`!!` 演算子は、`Nullableオブジェクト!!` のようにして使用する。これは積極的に使うべきでない手法で、強制的に Nullable を Non-null へ変換する。  
もし null が入っていた場合、NullPointerException がスローされる。他の言語における `get()` に相当する。

```kotlin
val a: String? = "Hello"
val b: String? = null

println(a!!.length) // OK!
println(b!!.length) // Runtime Error!
```

### List

配列を使う場合は、List コレクションを使用する。  
不変な List は `listOf()` メソッドを、可変な List は `mutableListOf()` メソッドを使って初期化できる。

```kotlin
val list = listOf("Apple", "Orange", "Grape")
```

### 関数

関数は、`fun 関数名(引数名: 引数の型名): 関数の型名 { 処理; return 値 }` と記述する。  
関数が複数行におよばない場合は、`fun 関数名(引数名: 引数の型名): 関数の型名 = 値` といったふうに、波括弧と return を省略できる。

```kotlin
fun sample(a: Int, b: String): String {
    val buf = a.toString() + b
    return buf
}

fun sample2(a: Int, b: String): String =
    a.toString() + b
```

`(引数名: 引数の型名 = 初期値)` とすることで、引数が省略されたときの初期値を指定できる。

```kotlin
fun greet(message: String = "Hello!"): Unit =
    println(message)

greet("GoodNight!") // "GoodNight!"
greet()             // "Hello!"
```

関数名は、`myFunction()`、`helloWorld()` のように、camelCase で記述する (先頭の単語は小文字、以降の単語は大文字で始める)。

### if 式

Kotlin では、ほとんどの制御構文が文 (statement) ではなく式 (expression) であり、返り値を持つ。

if 式は、返り値を持ち、三項演算子のようにして使用できる。

```kotlin
val value = (readLine() ?: "").toInt()

println(
    if (value > 0)
        "正数だよ"
    else if (value < 0)
        "負数だよ"
    else
        "0だよ"
)
```

### for

Kotlin の for は、式ではない。  
また、C言語風の `for (初期化; 条件; カウント)` 記法は使えない。

for は、`for (変数 in イテレーター)` というような、いわゆる for-each 記法を使用する。  
ある回数繰り返したい場合は、`0..9` のように Range オブジェクトを生成するのが一般的。

```kotlin
// "0123456789"
for (i in 0..9)
    print(i)
```

### foreach と map

iterable (for で回せる) なオブジェクトの各要素を順番に処理したい場合は、for よりも適切な方法がある。

foreach は、iterable の各要素を利用して、任意の処理を行うためのメソッドである。  
通常のラムダ式のようにローカル変数を命名することもできるし、現在の要素を `it` キーワードによって取得することもできる。

```kotlin
val items = listOf(4, 7, 2, 8)

// "4728"
items.foreach { item ->
    print(item)
}

// "4728"
items.foreach { print(it) }
```

map は、iterable の各要素へ任意の関数を適用し、変換するためのメソッドである。

```kotlin
val items = listOf(4, 7, 2, 8)

// "5839"
items
    .map { it + 1 }
    .foreach { print(it) }
```

このように、ラムダ式を渡す関数を高階関数 (hyper-kind function) という。ほかにも、fold などの便利な高階関数が多数用意されている。

### パターン マッチング

Kotlin には、switch 式に相当する when 式が存在する。

```kotlin
val a = readLine()!!.toInt()

// "3～5"
println(
    when (a) {
        is 1 -> "1"
        is 2 -> "2"
        is in 3..5 -> "3～5"
        is in 6..10 -> "6～10"
        else -> "それ以外"
    }
)
```

`when ()` の中でのみ使用できるローカル変数を宣言することもできる。

```kotlin
println(
    when (val a = readLine()!!.toInt()) {
        is 1 -> "1"
        is 2 -> "2"
        is in 3..5 -> "3～5"
        is in 6..10 -> "6～10"
        else -> "それ以外"
    }
)
```

型を判定したりすることも可能。

```kotlin
// "浮動小数点数！"
println(
    when (val a: Double = 3.14) {
        is Int -> "整数！"
        is Double -> "浮動小数点数！"
        is Double? -> "Nullable な浮動小数点数！"
        is String -> "文字列！"
        else -> "それ以外！"
    }
)
```

ほかにもいろいろな分岐条件を指定できる。

### クラス

Kotlin の場合、関数の引数のようにしてコンストラクターとそのパラメーターを定義できる。  
プロパティの初期化以外にやりたいことがある場合、`init` ブロック内に処理を記述できる。

プロパティやメソッドは、`インスタンス名.プロパティ`、`インスタンス名.メソッド()` のようにしてアクセスする。  
アクセス修飾子を指定しなかった場合、すべてのプロパティやメソッドは public 扱いとなる。

インスタンスの初期化時、Java などのように `new` キーワードは書かない。

```kotlin
class Sample(val a: String, private val b: String, val c: String = "C") {
    init {
      println("インスタンスが生成されたよ！")
    }

    fun printAll(): Unit =
        println(a + b + c)
}

val sample = Sample("A", "B") // "インスタンスが生成されたよ！"
println(sample.a)             // "A"
println(sample.b)             // Compile Error!
println(sample.c)             // "C"
println(sample.printAll())    // "ABC"
```

コンストラクターそのものを private にすることもできる。この場合、コンストラクターを通じて初期化することはできなくなる。

```kotlin
class Sample private constructor(val a: String, val b: String)
```

Kotlin には static キーワードがない。かわりに、コンパニオン オブジェクトを使用する。

`companion object` ブロック内に宣言されたプロパティやメソッドは、`クラス名.プロパティ`、`クラス名.メソッド()` のようにアクセスでき、すべてのインスタンスで共有される。

```kotlin
class Sample(val a: String) {
    companion object {
      val b: String = "World!"
    }
}

val sample = Sample("Hello, ")
println(sample.a + Sample.b)  // "Hello, World!"
```

コンパニオン オブジェクトからであれば private なプロパティおよびメソッドにもアクセスできるため、先述した private なコンストラクターと組み合わせることで、たとえば受け取った値が正常なものであるかどうかの検証など、インスタンス生成前に任意の処理をはさむことができる。これをファクトリー パターンという。

```kotlin
class PositiveNum private constructor(val value: Int) {
    companion object {
        // 受け取った値が0より大きければ PositiveNumber のインスタンスを、
        // 0より小さければエラーを投げるファクトリー メソッド
        fun genInstance(value: Int) =
            if (value > 0)
                PositiveNum(value)
            else
                throw RuntimeException()
    }
}
// Compile Error! コンストラクターは private だ
val posNum1 = PositiveNum(10)

// Runtime Error! 値が0以下だ
val posNum2 = PositiveNum.genInstance(-10)

// OK!
val posNum3 = PositiveNum.genInstance(10)
```

Kotlin では、`operator` キーワードを使用してメソッドを定義することで、+ や -、& などの演算子をオーバーロードすることができる。  
なかでも `invoke()` 演算子は特殊で、`インスタンス名()` のようにして呼び出せるメソッドを定義できる。

この invoke() をコンパニオン オブジェクト内に定義してやれば、ファクトリー メソッドをまるでコンストラクターのように見せかけることができる。

```kotlin
class PositiveNum private constructor(val value: Int) {
    companion object {
        operator fun invoke(value: Int) =
            if (value > 0)
                PositiveNum(value)
            else
                throw RuntimeException()
    }
}

// Runtime Error! 値が0以下だ
val posNum1 = PositiveNum(-10)

// OK!
val posNum2 = PositiveNum(10)
```

クラス名は、`MyClass`、`HelloWorld` のように、PascalCase で記述する (すべての単語を大文字で始める)。

### data class

`data` キーワードを使用して宣言されたクラスはデータ クラスと呼ばれる。  
データ クラスは、必ず1つ以上のパラメーターを持つコンストラクターを持たなければならない。また、すべてのパラメーターが不変 (`val`) であることが推奨される。

```kotlin
data class Sample(val a: Int)
```

データ クラスには、`esuals()`、`hashCode()`、`toString()` の3つのメソッドが暗黙的にオーバーライドされ、また、`copy()`、`componentN()` の2つのメソッドが暗黙的に定義される。

`equals()` は、2つのインスタンスを比較する等価演算子 == である。  
通常の `equals()` は異なるインスタンスであれば必ず false を返すようになっているが、データ クラスの `equals()` メソッドは、各パラメーターの値が等しければ true、そうでない場合は false を返す。


`hashCode()` は、各インスタンスのハッシュ値を返す。  
通常の `hashCode()` はパラメーターの値にかかわらず一意のハッシュを返すが、データ クラスの `hashCode()` は、各パラメーターが等しいインスタンスであれば同じハッシュを返す。

`toString()` は、そのインスタンスの文字列表現を返す。  
通常の `toString()` は `クラス名@ハッシュ値` の形式で表せられるが、データ クラスの `toString()` は、`クラス名(パラメーター1=値, パラメーター2=値, ...)` という形式になる。

`copy()` は、そのインスタンスのパラメーターを維持したインスタンスのコピーを提供する。また、任意のパラメーターを渡すことで、そのパラメーターのみを更新したコピーが得られる。

`componentN()` は、各パラメーターにアクセスするためのアクセサー (1つ目のパラメーターは `component1()`、2つ目のパラメーターは `component2()`...) を提供する。

これらのメソッドが有効なのはコンストラクターのパラメーターに対してだけであり、その他のプロパティは無視される。

`copy()` メソッドの存在により、データ クラスにおいては、コンストラクターを完全に隠ぺいすることはできない。

### enum class

ときおり、複数の `状態` を表したい場合がある。これらを Int や String として扱うのは、可読性、保守性、堅牢性、あらゆる点において不適切である。

こうしたケースでは列挙体 (enumeration) を使用する。列挙体は、`enum` キーワードを使用して宣言されたクラスである。

```kotlin
enum class Gender {
    MALE,
    FEMALE,
    OTHER
}
```

コンストラクターと各パラメーターに割り当てる値を指定することで、ほかの型と相互に変換することもできる。  
加えて、abstract なメソッドを定義して、各パラメーターごとにオーバーライドすることもできる。

```kotlin
enum class Gender(value: Int) {
    MALE(1) {
        override fun print(): Unit =
            println("MALE")
    },

    FEMALE(2) {
        override fun print(): Unit =
            println("FEMALE")
    },

    OTHER(9) {
        override fun print(): Unit =
            println("OTHER")
    },

    abstract fun print(): Unit
}
```
