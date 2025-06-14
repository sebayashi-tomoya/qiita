# ラムダ式とは？

匿名関数を簡単に作成できる機能のことです
メソッドとして宣言するまでもない処理やメソッド自体を引数として渡したいときによく使用されます

匿名関数という堅苦しい言葉を使いましたが、要はその場のみでしか使用しないメソッドを簡単に記述できるということですね

代表的なのが、LINQ に渡す引数です
C#で実装をしているとき、LINQ をよく使いますよね？

例えば、Where を呼び出す際は以下のように記述すると思います

```c#
int[] numbers = [ 1, 2, 3, 4, 5];

// 偶数のみを抽出
int[] evenNumbers = numbers
    .Where(num => num % 2 == 0)
    .ToArray();
```

このとき、Where の引数に渡している`num => num % 2 == 0`がラムダ式に該当します
偶数判定を行うメソッドを新設することなくその場で処理を書くことができます

# 基本構文

```
(引数リスト) => 式またはステートメントブロック
```

- 引数リスト：メソッドの引数のようにカンマ区切りで指定
- =>：ラムダ演算子
- 右辺：1 行の式、または複数行の場合は { ... } ブロック

# ラムダ式があると嬉しいこと

### 使い捨てメソッドを楽に実装できる

まず、その場でしか使用しないメソッドを楽に実装できるのが大きなメリットの一つです

例えば、ある処理の終了後に呼ばれるメソッド(OnSomethingCompleted)にて、共通処理と呼び出し元によって異なる個別処理を実行したい場合

```c#
public void OnSomethingCompleted(Action onCompleted)
{
    // 処理終了後の共通処理
    Console.WriteLine("共通処理を実行しました");

    // 引数で受け取った個別処理を実行
    onCompleted.Invoke();
}
```

ラムダ式なしの呼び出し

```c#
OnSomethingCompleted(CompletedA);
OnSomethingCompleted(CompletedB);

// 引数に渡すためのメソッドを作らないといけない
private static void CompletedA()
{
    Console.WriteLine("Aの処理終了");
}

private static void CompletedB()
{
    Console.WriteLine("Bの処理終了");
}
```

ラムダ式ありの呼び出し

```c#
// その場でメソッドを記述できる
OnSomethingCompleted(() => Console.WriteLine("A の処理終了"));
OnSomethingCompleted(() => Console.WriteLine("B の処理終了"));
```

A と B の処理終了後に渡しているコンソール出力の処理はここでしか使用しませんが、新たにメソッドを作る必要がなくなるわけですね 👍

記述量も削減できますし、1 箇所からしか呼ばれない不要なメソッドを作る必要もなくなるのです

上記のような記述方法は非同期処理やそのコールバックの実装時にも有効です

### LINQ との組み合わせでデータ操作が直感的に

みなさんが一番お世話になっているのはこの LINQ との組み合わせではないでしょうか？

例えば、コレクションの全ての要素に同じ処理を実行する Select メソッドの引数は以のようになっています

```c#
public static IEnumerable<TResult> Select<TSource, TResult>(
    this IEnumerable<TSource> source,
    Func<TSource, TResult> selector)
```

https://github.com/dotnet/runtime/blob/5535e31a712343a63f5d7d796cd874e563e5ac14/src/libraries/System.Linq/src/System/Linq/Select.cs#L16C13-L63C85

第一引数は拡張メソッドとして利用するための記述ですね

IEnumerable<T> に対する拡張メソッドになっているため、
コード上では`コレクション.Select()`で呼び出せるというわけです

拡張メソッドって何？という方は以前記事にまとめているので、
よかったらそちらも合わせてご覧ください！

https://qiita.com/sebayashi-tomoya/items/e9c665b4692b40b71f5f

話が逸れましたが、第二引数の`Func<TSource, TResult> selector`にラムダ式を渡すことができます
内部的に色々な処理が挟まりますが、最終的には`selector(/* コレクションの一要素 */)`が実行されます

めちゃくちゃ端折って自前で実装するとしたら以下のイメージになります

```c#
public static class IEnumerableExtensions
{
    public static IEnumerable<TResult> Select<TSource, TResult>(
        this IEnumerable<TSource> source,
        Func<TSource, TResult> selector)
    {
        // source = numbers
        foreach (var current in source)
        {
            // 要素の一つ一つに引数で受け取ったメソッドを実行する
            yield return selector(current);
        }
    }
}

// 呼び出し元
int[] numbers = [1, 2, 3, 4, 5];
var squared = numbers.Select(number => number * number); // numbersの一要素を引数として2乗処理を実行する
foreach (var item in squared)
{
    Console.WriteLine(item);
}
```

出力内容

```terminal
1
4
9
16
25
```

# まとめ

ここまで偉そうに書いてきましたが、筆者自身は最近まで「LINQ で使うやつだよね？」ぐらいの理解でなんとなく使ってました 😅

自前でラムダ式使ってやろうと意気込んで実装しようとした際に思いっきり躓いたので、この記事を書くに至りました

わかった気になるのは良くないですね…
ちゃんと調べてみて理解が深まった気がします 💪

**めざせラムダマスター!**

# 参考

https://nankurunikki.com/%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0/c/357/

https://qiita.com/toRisouP/items/98cc4966d392b7f21c30

https://qiita.com/namakoRice/items/3af2990674b66c259e45
