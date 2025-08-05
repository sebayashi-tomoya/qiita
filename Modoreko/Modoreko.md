# プログラマの性
2023年に発売された『ゼルダの伝説 ティアーズ オブ ザ キングダム』を皆さんご存知でしょうか？

大人気のティアキンですが、ある画期的なシステムが話題となりました。そう、"モドレコ"というシステムです。なんとフィールド上のあらゆるオブジェクトの巻き戻しが可能なんです。しかも、動かせるものほとんどが対象で😳

そのシステムを見たとき、私はこう思ったわけです。
**これどうやってプログラミングしてるの？**
プログラマの性ですかね。

2年前のゲームなので旬は過ぎ去りましたが、超簡易版モドレコをC#の`Stack`を使って実装してみようと思います。

# Stackとは？

https://learn.microsoft.com/ja-jp/dotnet/api/system.collections.generic.stack-1?view=net-8.0

同じ型のインスタンスの可変サイズの先入れ先出し(Last In, First Out)のコレクションのことです。

実装例で見てもらった方が早いと思います。
`Push`で値を追加して、`Peek`または`Pop`で一番上の値を参照します。
`Peek`は値を残したままで、`Pop`を使うと参照した値は消えます。

```c#
// Stackの初期化
var numbers = new Stack<int>();

// Pushを使って1~5を追加
Enumerable.Range(1, 5)
    .ToList()
    .ForEach(numbers.Push);

// すべての要素をPeek
Console.WriteLine("Peek");
Enumerable.Repeat(0, 5)
    .Select(_ => numbers.Peek())
    .ToList()
    .ForEach(Console.WriteLine);

Console.WriteLine("残っている数字");
foreach (var num in numbers)
{
    Console.WriteLine(num);
}

// すべての要素をPop
Console.WriteLine("Pop");
Enumerable.Range(0, numbers.Count)
    .Select(_ => numbers.Pop())
    .ToList()
    .ForEach(Console.WriteLine);

Console.WriteLine("残っている数字");
foreach (var num in numbers)
{
    Console.WriteLine(num);
}
```

出力結果
```
Peek
5
5
5
5
5
残っている数字
5
4
3
2
1
Pop
5
4
3
2
1
残っている数字
```

今回簡易版モドレコ実装で使うのは`Pop`です。

# いざ実装！
```c#
using System;
using System.Collections.Generic;
using System.Threading;

class Program
{
    static void Main()
    {
        // Stackで位置の履歴を記録
        var history = new Stack<int>();
        // 現在の位置
        int position = 0;
        // 巻き戻しが実行中か
        bool isRewinding = false;

        Console.WriteLine("Enterでモドレコ（逆再生）を開始します。Escで終了");

        while (true)
        {
            if (Console.KeyAvailable)
            {
                var key = Console.ReadKey(true).Key;

                if (key == ConsoleKey.Enter)
                {
                    // Enterキー押下で巻き戻しを実行する
                    isRewinding = true;
                }
                else if (key == ConsoleKey.Escape)
                {
                    // Escでアプリ終了
                    break;
                }
            }

            if (isRewinding)
            {
                // 巻き戻し実行中の処理
                if (history.Count > 0)
                {
                    // 過去の位置に戻して位置を描画
                    position = history.Pop();
                    Draw(position);
                }
                else
                {
                    // Stackがない場合は巻き戻しを終了して通常の方向へ
                    isRewinding = false;
                    Console.WriteLine("巻き戻し終了！");
                }
            }
            else
            {
                // 巻き戻し実行中は現在位置を記録して前身させる
                history.Push(position);
                position++;
                Draw(position);
            }

            Thread.Sleep(300); // 動作間隔
        }
    }

    static void Draw(int pos)
    {
        Console.Clear();
        Console.WriteLine("位置: " + pos);
        Console.WriteLine(new string(' ', pos) + "●"); // ●が進んだり戻ったり
    }
}
```

`Stack`型の`history`に位置を記録させておいて、巻き戻し中か否かで進む方向を制御します。

**画面表示**

