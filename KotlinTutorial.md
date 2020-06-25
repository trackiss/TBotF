## Kotlin Tutorial

身内向け。Java が少し書ける程度の知識を要求します。

都合上、あとで解説する構文が先に登場することがありますのでご了承ください。

---

### IntelliJ IDEA のインストール

[こちら](https://www.jetbrains.com/ja-jp/idea/) から、IntelliJ IDEA Community をダウンロードしてインストール。

初回起動時はいろいろ訊かれるので、自力で進む。

日本語化したいなら、Japanese Language Pack EAP プラグインをインストールする。まだアーリーアクセスなので完全ではないが、公式の日本語訳である。  
その他のプラグインは、Rainbow Brackets や Material Theme などがおすすめ。Rainbow Brackets は、ペアとなっている丸括弧や波括弧を色分けしてくれる。Material Theme は、IDE の見た目をかっこよくしてくれる。別になくてもいい。

---

### 新規プロジェクトの作成

"新規プロジェクト" をクリックしたら、左のペインから "Gradle" を選択。  
"Java" のチェックを外し、"Kotlin/JVM" にチェックを入れ "次" へ。

任意のプロジェクト名を入力して "完了"。

---

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

---

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

---

### 定数

コンパイル時に解決される定数を使う場合は、`const val` キーワードを使って宣言する。

```kotlin
const val MY_CONSTANTS = "Hello, World!"
```

定数名は、`MY_CONSTANTS`、`HELLO_WORLD` のように、CONSTANT_CASE で記述する (すべて大文字、単語はアンダースコアで区切る)。

---

### Nullable

Kotlin は、デフォルトではすべての型において null の代入を禁じている。

null を許容する (nulleble) 型は、`?` 接尾辞を付与する。

```kotlin
val a: String = null  // Compile Error!
val b: String? = null // OK!
```

Nullable な型は、同じ Nullable でない型と互換性がない。たとえば、String 型と String? 型はまったくの別物として扱われる。

次のように、Nullable なオブジェクトのフィールドやメソッドを呼び出すことはできない。

```kotlin
val a: String = "Hello"
val b: String? = "Hello"

println(a.length) // OK!
println(b.length) // Compile Error!
```

Nullable なオブジェクトのフィールドやメソッドを呼び出すには、セーフタイプ演算子 `?.` を使用する。  
`?.` を使って呼び出されたフィールドやメソッドは、もし値が null だった場合は、評価されずにそのまま null を返す。ほかの言語における `map()` に相当する。

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

println(a?.length ?: -1) // "5"
println(b?.length ?: -1) // "-1"
```

`!!` 演算子は、`Nullableオブジェクト!!` のようにして使用する。これは積極的に使うべきでない手法で、強制的に Nullable を Non-null へ変換する。  
もし null が入っていた場合、NullPointerException がスローされる。他の言語における `get()` に相当する。

```kotlin
val a: String? = "Hello"
val b: String? = null

println(a!!.length) // OK!
println(b!!.length) // Runtime Error!
```

---

### 型システム

Kotlin の基底型 (basic types) は次のとおり。

| Name    | Desciption         | Literal              |
| :------ | :----------------- | :------------------- |
| Byte    | 8bit 符号付き整数  | `123 as Byte`        |
| Short   | 16bit 符号付き整数 | `123 as Short`       |
| Int     | 32bit 符号付き整数 | `123`                |
| Long    | 64bit 符号付き整数 | `123L`, `2147483648` |
| Float   | 32bit 浮動小数点数 | `0.123f`             |
| Double  | 64bit 浮動小数点数 | `0.123`              |
| Boolean | 論理値             | `true`, `false`      |
| Char    | Unicode 文字       | `'A'`                |
| String  | Unicode 文字列     | `"ABC"`              |

標準ライブラリとして `UInt`、`UByte` などの符号なし整数型も提供されているが、特別な事情でもない限り使わないほうがいいだろう。

整数リテラルでは、2進表記 (`0bXXXX`) と16進表記 (`0xXXXX`) が使用できる。また、途中のアンダースコアは無視されるため、区切り文字として使用できる。  
浮動小数点数リテラルでは、指数表記 (`X.XXeXX`) が使用できる。

```kotlin
val dec: Int = 1_234_567
val bin: Int = 0b00010010_11010110_10000111
val hex: Int = 0x12_D6_87

val sc: Double = 0.00123
val ex: Double = 1.23e3
```

文字リテラル、文字列リテラルでは、Java と同様にエスケープシーケンスをサポートする。

文字列リテラルでは、通常の文字列である `"ABC"` のほか、生文字列 (エスケープシーケンスなし、改行なども含まれる) である `"""ABC"""` をサポートする。  
また、文字列テンプレート (ほかの言語でいう文字列補間) もサポートする。

```kotlin
// ABC
// DEF
// G
println("ABC\nDEF\nG")

// ABC
// DEF
// G
println("""ABC
DEF
G""")

val a = 1
val b = 2

// 1 + 2 = 3
println("$a + $b = ${ a + b }")
```

Java では、もともとは `int` などの基底型のみがプリミティブ型として特別扱いを受けていたのだが、これらをそのままオブジェクト指向に取り入れることができなかったため、ラッパーとして `Integer` などのオブジェクトが追加された。  
このため、たとえば、整数型の変数を宣言するときは `int a;` と書けるが、整数型の List を宣言するときは `List<Integer> a;` と書かなければならないなどのギャップが生じてしまった。

いっぽう、Kotlin ではすべての型が純粋なオブジェクトであるため、このような問題は起こらない。

すべての Non-null な型およびクラスは、`Any` 型を暗黙的に継承する。  
また、すべての Nullable な型およびクラスは、`Any?` 型を暗黙的に継承する。

これとは対照的に、すべての Non-null な型およびクラスは、`Nothing` 型へ暗黙的に派生する。  
また、すべての Nullable な型およびクラスは、`Nothing?` 型へ暗黙的に派生する。

---

### Nothing の必要性

俗にトップ タイプと呼ばれる Any はともかく、なぜボトム タイプである Nothing が必要なのかについて。

たとえば、2つの整数 a と b を受け取り、a を b で除算する関数を考えてみる。ここで、b が0であった場合は、例外をスローする。

```kotlin
fun devide(a: Int, b: Int): Int =
    if (b != 0)
        a / b
    else
        throw ArithmeticException()
```

devide() は Int 型の値を返すと明記してあるのだから、本来であれば、返しうるすべての値が Int かその派生型でなければコンパイルは通らないはずである。  
実際、Java でこれと同じコードを書いた場合は、例外をスローする経路は特別に型チェックをパスするようになっている。

しかし、Kotlin にはすべての派生型である Nothing がある。  
Java で生じた問題を解決するために、Kotlin の throw 式は Nothing を返すようになっている。Nothing は Int の派生型でもあるので、これならば型システム上なにも問題はない。

また、たとえば「関数名や引数、返り値の型などは決まっているがまだ実装していない、しかしテストのためにとりあえずコンパイルを通したい」というようなケースが往々にして存在する。  
Kotlin では、このケースに対する自然な解決策として `TODO()` メソッドを提供する。

```kotlin
// OK!
fun placeholderFunc(a: Int, b: String): Int = TODO()

// Runtime Error!
val s = placeholderFunc(10, "ABC")
```

TODO() は、常に Nothing を返すメソッドである。Nothing はあらゆる型の派生型であるので、とりあえずこれを入れておけば、是非はともかく、コンパイルは通すことができる。  
当然だが、例にもあるように、この関数を呼び出そうとすると NotImplementedError がスローされるので注意。

---

### 標準入出力

先ほどから多用しているが、念のため。

標準出力には、`print()` または `println()` メソッドを使用する。引数として、任意の文字列リテラルか、任意のオブジェクトを渡す。オブジェクトが渡された場合、`toString()` の内容を出力する。

```kotlin
// "Hello, World!"
print("Hello, ")
print("World!")

// "Hello, World!"
println("Hello, World!")
```

標準入力には、`readLine()` メソッドを使用する。Java の BufferedReader を使用しているため、Nullableである。  
整数などが欲しいのなら `toInt()` するなり、空白で区切りたいのなら `split(' ')` するなり。

いろいろ書き方はあるが、もっとも安全なものは次のとおり。`toInt()` できない文字が含まれていた場合はどうしようもないが。

```kotlin
val a: String = readLine() ?: ""

val b: Int =
    readLine()
        ?.toInt()
        ?: 0
```

---

### コレクション

Kotlin には、主に4つのコレクションが存在する。  
これらは不変であり、可変なコレクションは `mutableXXX` クラスとして別に定義されている。

`Array<T>` は、Java のプリミティブな配列のラッパーである。パフォーマンス上の問題でもないかぎり、基本的には Java との相互運用性の維持のためだけに使用すべきである。したがって、ここでは取り上げない。

`List<T>` は、シーケンスである。複数の値を順序を維持したまま保持し、配列の代替として使用できる。`listOf()` メソッドを使って初期化できる。  
各要素にアクセスするには、`get()` メソッドに添字を渡すか、配列と同様に `リスト[添字]` のようにすることもできる。

```kotlin
val list: List<String> = listOf("Apple", "Orange", "Grape")

// "[Apple, Orange, Grape]"
println(list)
```

`Set<T>` は、集合である。Set は、順序を保証せず、また重複を許容しない。`setOf()` メソッドを使って初期化できる。

```kotlin
val set: Set<String> = setOf("Apple", "Orange", "Apple", "Grape")

// "[Apple, Orange, Grape]"
println(set)
```

`Map<K, V>` は、連想配列である。当然 順序は保証されず、また Key の重複を許容しない。`mapOf` メソッドに複数の `Pair<K, V>` オブジェクトを渡すことで初期化できる。  
Pair オブジェクトは、`Pair('A', "ABC")` のように生成することもできるし、`'A' to "ABC"` という糖衣構文も用意されている。

```kotlin
val map: Map<Char, String> = mapOf(
    'A' to "Apple",
    'B' to "Orange",
    'C' to "Grape"
)

// "{A=Apple, B=Orange, C=Grape}"
println(map)
```

---

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

---

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

---

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

---

### forEach と map

iterable (for で回せる) なオブジェクトの各要素を順番に処理したい場合は、for よりも適切な方法がある。

forEach は、iterable の各要素を利用して、任意の処理を行うためのメソッドである。  
通常のラムダ式のようにローカル変数を命名することもできるし、現在の要素を `it` キーワードによって取得することもできる。

```kotlin
val items = listOf(4, 7, 2, 8)

// "4728"
items.forEach { item ->
    print(item)
}

// "4728"
items.forEach { print(it) }
```

map は、iterable の各要素へ任意の関数を適用し、変換するためのメソッドである。

```kotlin
val items = listOf(4, 7, 2, 8)

// "5839"
items
    .map { it + 1 }
    .forEach { print(it) }
```

また、List の標準入力による初期化なども次のようにして可能。

```kotlin
val list =
    readLine()
        ?.split(' ')
        ?.map { it.toInt() }
```

このように、ラムダ式を渡すことができる関数を高階関数 (hyper-kind function) という。ほかにも、fold などの便利な高階関数が多数用意されている。

forEach や map などを利用したループ処理は遅いという批判があるが、Kotlin ではこれらの処理はインライン展開されるため、高速である。

---

### パターン マッチング

Kotlin には、switch 式に相当する when 式が存在する。

```kotlin
val a = readLine()!!.toInt()

// "3～5"
println(
    when (a) {
        1 -> "1"
        2 -> "2"
        in 3..5 -> "3～5"
        in 6..10 -> "6～10"
        else -> "それ以外"
    }
)
```

`when ()` の中で、その when 式内でのみ使用できるローカル変数を宣言することもできる。

```kotlin
println(
    when (val a = readLine()!!.toInt()) {
        1 -> "1"
        2 -> "2"
        in 3..5 -> "3～5"
        in 6..10 -> "6～10"
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

---

### キャスト

Kotlin では、is 演算子によって型を判定することができる。  
if 式や when 式を使用して型判定を行うと、そのスコープ内にかぎり、暗黙的にキャストが行われる (スマート キャスト)。

```kotlin
val obj: Any = "Hello!"

println(obj.length)     // Compile Error! Any 型は length プロパティを持っていない

if (obj is String)
    println(obj.length) // OK! obj は暗黙的に String 型にキャストされている
else
    println(obj.length) // Compile Error!

if (obj !is String)
    println(obj.length) // Compile Error!
else
    println(obj.length) // OK!

when (obj) {
    is Int -> println(obj + 10)         // OK! (ただし Runtime Error にはなる)
    is String -> println(obj.length)    // OK!
}
```

また、アンセーフ キャスト演算子 `as` によって明示的に型をキャストすることができる。ただし、キャストに失敗したときに ClassCastException がスローされるため、安全でない。  
したがって、基本的にはセーフ キャスト演算子 `as?` を使用するべきである。`as?` は、Nullable な型へキャストし、もしキャストに失敗したときは null を返す。

```kotlin
val obj: Any = "Hello!"

val castedObj1: Int = obj as Int    // Runtime Error!
val castedObj2: Int? = obj as? Int  // OK!

println(castedObj)  // "null"
```

---

### クラス

Kotlin の場合、`class クラス名 constructor(va(l|r) パラメーター名1: パラメーターの型1, ...)` のようにしてプライマリ コンストラクターとそのパラメーターを定義できる。ここで、一部のケースを除いて `constructor` キーワードは省略できる。  
プロパティの初期化以外にやりたいことがある場合、`init` ブロック内に処理を記述できる。

プロパティやメソッドは、`インスタンス名.フィールド`、`インスタンス名.メソッド()` のようにしてアクセスする。  
アクセス修飾子を指定しなかった場合、すべてのフィールドやメソッドは public 扱いとなる。

インスタンスの初期化時、Java などのように `new` キーワードは書かない。

```kotlin
class Sample constructor(val a: String, private val b: String, val c: String = "C") {
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

プライマリ コンストラクターそのものを private にすることもできる。この場合、プライマリ コンストラクターを通じて初期化することはできなくなる。また、`constructor` キーワードを省略することはできない。

```kotlin
class Sample private constructor(val a: String, val b: String)
```

明示的にセカンダリ コンストラクターを宣言することもできる。セカンダリ コンストラクターは `constructor()` メソッドとして定義され、オーバーロードが可能である。

```kotlin
class Sample(val a: String) {
    val b: String
    val c: String

    constructor(b: Int) {
        b = b.toString()
    }

    constructor(c: Double) {
        c = c.toString()
    }
}
```

クラス名は、`MyClass`、`HelloWorld` のように、PascalCase で記述する (すべての単語を大文字で始める)。

---

### コンパニオン オブジェクト

Kotlin には static キーワードがない。かわりに、コンパニオン オブジェクトを使用する。

`companion object` ブロック内に宣言されたフィールドやメソッドは、`クラス名.フィールド`、`クラス名.メソッド()` のようにアクセスでき、すべてのインスタンスで共有される。

```kotlin
class Sample(val a: String) {
    companion object {
      val b: String = "World!"
    }
}

val sample = Sample("Hello, ")
println(sample.a + Sample.b)  // "Hello, World!"
```

コンパニオン オブジェクトからであれば private なフィールドおよびメソッドにもアクセスできる。  
これを利用して、たとえば受け取った値が正常なものであるかどうかの検証などを行ってからインスタンスを返すなど、インスタンス生成前に任意の処理をはさむことができる。  
このような手法をファクトリー パターンという。

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

この invoke() をコンパニオン オブジェクト内に定義することで、ファクトリー メソッドをまるでコンストラクターのように見せかけることができる。

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

---

### ジェネリクス

Kotlin も当然、ジェネリクスによる多態性 (polymorphism) をサポートしている。  
Java と同様に、クラスや関数に総称型 T を指定することができ、また型パラメーター `<型>` を渡すことができる。

```kotlin
class Sample<T>(val value: T)

val sample1 = Sample<String>("ABC")
val sample2 = Sample("ABC")    // 型が推論されるので型パラメーターを省略できる
```

Kotlin の総称型は、Java のようにすべてが不変 (この「不変」は「可変/不変」の不変ではなく、「不変/共変/反変」の不変である) ではなく、一定の制限のもとで、共変 (派生型も許容する) および反変 (基本型も許容する) とすることができる。  
もっとも、あまり使うものでもないので読み飛ばしてもらってかまわない。

不変とする場合は、通常どおり `<T>` と書く。

```kotlin
class Sample<T> {
    fun sampleFunc1(): T = TODO()               // OK!
    fun sampleFunc2(value: T): Unit = TODO()    // OK!
}

// Compile Error! 不変なので Any に String は入らない
val strSample: Sample<String>
val sample1: Sample<Any> = strSample

// Compile Error! 不変なので String に Any は入らない
val anySample: Sample<Any>
val sample2: Sample<String> = anySample
```

共変とする場合は、`<out T>` と書く。このとき、T は所属するメソッドの返り値の型としてしか使用できない。

```kotlin
class Sample<out T> {
    fun sampleFunc1(): T = TODO()               // OK!
    fun sampleFunc2(value: T): Unit = TODO()    // Compile Error! <out T> な型は引数に使えない
}

// OK! 共変なので Any に String は入る
val strSample: Sample<String>
val sample1: Sample<Any> = strSample

// Compile Error! 不変なので String に Any は入らない
val anySample: Sample<Any>
val sample2: Sample<String> = anySample
```

反変とする場合は、`<in T>` と書く。このとき、T は所属するメソッドの引数の型としてしか使用できない。

```kotlin
class Sample<T> {
    fun sampleFunc1(): T = TODO()               // Compile Error! <in T> な型は返り値に使えない
    fun sampleFunc2(value: T): Unit = TODO()    // OK!
}

// Compile Error! 不変なので Any に String は入らない
val strSample: Sample<String>
val sample1: Sample<Any> = strSample

// OK! 反変なので String に Any は入る
val anySample: Sample<Any>
val sample2: Sample<String> = anySample
```

---

### 継承

Kotlin では、すべてのクラスがデフォルトで final である。継承させたいクラスは、`open` キーワードを付与する。  
open なクラスを継承するには、クラス宣言のうしろに `: クラス名()` を記述する。

また、デフォルトではすべてのメソッドがオーバーライドすることができない。オーバーライドさせたいメソッドは、`open` キーワードを付与する。  
もちろん、本体では実装を持たず、派生クラスで実装させたい (抽象メソッド) ときは `abstract` キーワードが使用できる。

```kotlin
open class Fruit(price: Int) {
    fun normalFunc(): Unit = TODO()
    open fun overridableFunc(): Unit = TODO()
    abstract fun virtualFunc(): Unit = TODO()
}

// Compile Error! abstract なメソッドは実装しなければならない
class Apple(price: Int): Fruit(price) {
    // Compile Error! open でないのでオーバーライドできない
    override fun normalFunc(): Unit = TODO()

    // OK!
    override fun overridableFunc(): Unit = TODO()
}
```

---

### インターフェース

Kotlin は Java と同様に多重継承をサポートしていないが、インターフェースによるミックスインをサポートしている。

インターフェースは `interface` キーワードを使用して宣言される。  
実装を持たないメソッドはすべて abstract 扱いとなる。逆にいえば、実装があれば普通のメソッド扱いとなり、明示的に `open` を指定しなければオーバーライドできない。

```kotlin
interface A {
    fun foo(): Unit
}

interface B {
    fun bar(): Unit
}

class AB: A, B {
    override fun foo(): Unit = TODO()
    override fun bar(): Unit = TODO()
}
```

---

### シングルトン オブジェクト

「アプリケーション中に1つしかインスタンスを持てないクラス」を実現する手法をシングルトン パターン (singleton pattern) という。  
シングルトンなクラスは、static class (Kotlin では宣言できないが) とは違い、継承することができ、また、継承されることができる。すなわち、インターフェースを実装することもできるし、抽象メソッドを持つこともできる。

シングルトン パターンは static class よりもコード量がかなり増えるという欠点があったが、Kotlin では、`object` キーワードを使ってシングルトンなクラスを宣言できる。

```kotlin
object Sample {
    val message: String = "Hello!"

    fun greet(): Unit =
        println(message)
}

val sample = Sample()   // Compile Error!
println(Sample.message) // "Hello!"
Sample.greet()          // "Hello!"
```

---

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
このメソッドの存在により、データ クラスにおいては、コンストラクターを完全に隠ぺいすることはできない。

`componentN()` は、各パラメーターにアクセスするためのアクセサー (1つ目のパラメーターは `component1()`、2つ目のパラメーターは `component2()`...) を提供する。

これらのメソッドが有効なのはコンストラクターのパラメーターに対してだけであり、その他のフィールドは無視される。

---

### enum class

ときおり、複数の `状態` を表したい場合がある。これらを Int や String として扱うのは、可読性、保守性、堅牢性、あらゆる点において不適切である。

こうしたケースでは列挙体 (enumeration) を使用する。列挙体は、`enum` キーワードを使用して宣言されたクラスである。

```kotlin
enum class Gender {
    MALE,
    FEMALE,
    OTHER
}

val gender: Gender = Gender.FEMALE
```

コンストラクターと各パラメーターに割り当てる値を指定することで、ほかの型の値へ変換することもできる。

また、enum class そのものは、フィールドやメンバを持つ通常のクラスのように使うことができる。加えて、abstract なメソッドを定義して、各パラメーターごとにオーバーライドすることもできる。

```kotlin
enum class Gender(val value: Int) {
    MALE(1) {
        override fun print(): Unit =
            println("Male")
    },

    FEMALE(2) {
        override fun print(): Unit =
            println("Female")
    },

    OTHER(9) {
        override fun print(): Unit =
            println("Other")
    };

    abstract fun print(): Unit
}

val gender = Gender.FEMALE

gender.print()          // "Female"
println(gender.value()) // "2"
```

列挙体では、enum class そのものは通常のクラスのように PascalCase、各フィールドは CONSTANCE_CASE で命名する。

### 値から列挙値への変換

列挙体のインスタンスから値を取り出すことは簡単にできるが、値から列挙体のインスタンスへ変換するには、自分でメソッドを定義しておくしかない。

enum class は、内包するすべてのパラメーターを配列として取り出せる `values()` メソッドを持っている。  
また、`firstOrNull()` メソッドを使えば、条件に合致する要素を null-safety に取り出すことができる。

```kotlin
enum class Gender(val value: Int) {
    MALE(1),
    FEMALE(2),
    OTHER(9);

    companion object {
        operator fun invoke(value: Int): Gender? =
            values().firstOrNull { it.value == value }
    }
}

println(Gender(2))      // "FEMALE"
println(Gender(999))    // "null"
```

### 例外処理

Kotlin にも try-catch-finally は存在しているが、その代替として使用できる `Result<T>` が用意されている。

Result\<T> は、T 型の返り値または Throwable のどちらかを内包する型である。すなわち、任意の例外をスローする可能性のある関数において、処理が滞りなく成功すれば T 型の値が、処理に失敗して例外がスローされればその Throwable オブジェクトが、この Result\<T> に格納される。

`Result.success(10)`、`Result.failure(RuntimeException())` のようにして明示的に成功/失敗時の Result\<T> を生成することもできるが、`tunCatching {}` メソッドを使用して例外をスローする可能性のあるメソッドを包むことにより、その実行結果が Result\<T> として返ってくる。

Result\<T> には、成功 (success) したか失敗 (failure) したかに応じて処理を行うメソッドが豊富に用意されている。

`getOrDefault()` は、`?:` 演算子と似ており、Success ならそのまま、Failure なら同じ型の任意の値を返す。  
`map {}` は `?.` と似ているもので、もし Success ならその値をラムダ式で変換し、Failure ならそのままにして、また Result\<T> に包み直して返す。map と逆のはたらきをするものが `recover {}` だ。`fold {}` なら、Success と Failure とで別のラムダ式 (返り値の型は同一) を適用させ、ついでに Result\<T> を外してくれる。  
また、forEach のように、Success 時または Failure 時にのみ、値を返さずにその場で任意の処理を行いたいときには `onSuccess {}` と `onFailure {}` が使用できる。

```kotlin
fun devide(a: Int, b: Int): Int =
    if (b != 0)
        a / b
    else
        throw ArithmeticException()

val resultS: Result<Int> = runCatching { devide(10, 2) }    // Result.success(5)
val resultF: Result<Int> = runCatching { devide(10, 0) }    // Result.failure(ArithmeticException())

println(resultS.getOrDefault(999))  // "5"
println(resultF.getOrDefault(999))  // "999"

println(resultS.map { (it * 10).toDouble() })   // "50.0"
println(resultF.map { (it * 10).toDouble() })   // "java.lang.ArithmeticExeption"

println(resultS.recover { "Failure!!!" })   // "5"
println(resultF.recover { "Failure!!!" })   // "Failure!!!"

// "50"
println(
    resultS.fold(
        onSuccess = {
            it * 10
        },
        onFailure = {
            999
        }
    )
)

// "999"
println(
    resultF.fold(
        onSuccess = {
            it * 10
        },
        onFailure = {
            999
        }
    )
)

resultS.onSuccess { println(it) }   // "5"
resultF.onSuccess { println(it) }   // 評価されない
```
