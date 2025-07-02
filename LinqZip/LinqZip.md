# LINQのZipって何？
C#の実装者なら日頃からかなりLINQにはお世話になっていますよね？

普段よく使うのは`Where`とか`Foreach`とか`Select`ですが、
この前かなり便利なメソッドに出会いました

そう、それが`Zip`です

公式ドキュメントによると
> 2 つのシーケンスの対応する要素に対して、1 つの指定した関数を適用し、結果として 1 つのシーケンスを生成します。

と記載されており、複数のリストを使用して任意の型の`IEnumerable`を返す処理です

# 基本的な使い方
実際にコードで見てもらった方がイメージが湧くと思います

```c#
var characters = new[] { "ジョナサン", "ジョセフ", "承太郎" };
var titles = new[] { "ファントムブラッド", "戦闘潮流", "スターダストクルセイダーズ" };

// 二つのコレクションを結合して文字列に変換
IEnumerable<string> map = characters.Zip(titles, (character, title) => $"title: {title} character:{character}");

foreach (var item in map)
{
    Console.WriteLine(item);
}

// 3つのコレクションの結合も可能
var partNumbers = new[] { 1, 2, 3 };

IEnumerable<string> tripleMap = characters
    .Zip(titles, (character, title) => new { title, character })
    .Zip(partNumbers, (map, partNumber) => $"parNumber: {partNumber} title: {map.title} character: {map.character}");

foreach (var item in tripleMap)
{
    Console.WriteLine(item);
}
```

出力内容(見やすいように余白の調整をしてます)
```
title: ファントムブラッド 　　　　character:ジョナサン
title: 戦闘潮流 　　　　　　　　　character:ジョセフ
title: スターダストクルセイダーズ character:承太郎

parNumber: 1 title: ファントムブラッド 　　　　character: ジョナサン
parNumber: 2 title: 戦闘潮流 　　　　　　　　　character: ジョセフ
parNumber: 3 title: スターダストクルセイダーズ character: 承太郎
```

# こんなこともできる
上記の例だと単純に文字列をくっつけただけですが、
intが格納された複数のコレクションの計算にも有効です

```c#
var numbers1 = new[] { 1, 2, 3, 4, 5 };
var numbers2 = new[] { 5, 4, 3, 2, 1 };

var added = numbers1
    .Zip(numbers2, (a, b) => a + b);

foreach (var item in added)
{
    Console.Write(item);
}
```

出力内容
```
6 6 6 6 6
```

二重ループのような実装をしなくても実現可能になるわけです！

# 注意点
すごく便利ですが、もちろん注意点もいくつかあります

## シーケンス数が異なる場合は少ない方に合わせて処理される
例えば、以下のような場合
```c#
var numbers1 = new[] { 1, 2, 3, 4, 5 };
var numbers2 = new[] { 5, 4, 3 };

var added = numbers1
    .Zip(numbers2, (a, b) => a + b);

foreach (var item in added)
{
    Console.Write(item);
}
```

出力内容は少ない方に合わせて3つになります
```
6 6 6
```

シーケンス数が同値であることが確実、または少ない方に合わせても問題ないことを確認する必要があります

## 遅延遅延実行のためタイミングによっては予期しない値になる可能性がある
遅延実行のため`foreach`で列挙したり、`ToList()`などでコレクション化したタイミングで初めてクエリが実行されます

以下のようにZipを実行した後にどちらかのリストの内容に変更があると、Zipしたかったリストの内容と差異が出る可能性があるので注意ですね☝️

```c#
var numbers1 = new[] { 1, 2, 3, 4, 5 };
var numbers2 = new[] { 5, 4, 3, 2, 1 };

// ここでZipしたいのは変更前のコレクション
IEnumerable<int> added = numbers1
    .Zip(numbers2, (a, b) => a + b);

// Zip後にコレクションの中身が変更される
numbers1[0] = 5;

foreach (var item in added)
{
    Console.Write(item);
}
```

出力内容
```
10 6 6 6 6
```

## nullのコレクションを渡すと例外に
Zipの実装は以下のようになっています
```c#
public static IEnumerable<TResult> Zip<TFirst, TSecond, TResult>(this IEnumerable<TFirst> first, IEnumerable<TSecond> second, Func<TFirst, TSecond, TResult> resultSelector)
{
    if (first is null)
    {
        ThrowHelper.ThrowArgumentNullException(ExceptionArgument.first);
    }

    if (second is null)
    {
        ThrowHelper.ThrowArgumentNullException(ExceptionArgument.second);
    }

    if (resultSelector is null)
    {
        ThrowHelper.ThrowArgumentNullException(ExceptionArgument.resultSelector);
    }

    return ZipIterator(first, second, resultSelector);
}
```

オーバーロードメソッドがいくつかありますが、他も同様です
引数に渡すコレクションがnullになる可能性があればnullチェックを行いましょう

# 参考
https://learn.microsoft.com/ja-jp/dotnet/api/system.linq.enumerable.zip?view=net-8.0

https://nakamuuu.blog/c-sharp-do-you-know-what-linq-zip-is/

https://zenn.dev/sakaki_web/articles/6a07616da68617
