# nullチェックが楽になるらしい
たまたま.NETの公式ドキュメントを漁っていたとき、とても興味深い新機能がリリースされたとの情報をキャッチしました。

C#14 の新機能でオブジェクトの null チェックの記述がさらになるらしいです。
今回はユルくただただ新機能の内容を見てみるだけの記事となります。

# 従来のnullチェック
例えば、以下のようなクラスがあるとします。

```c#
public class Item
{
    public string Name {get; set;}
}
```

このクラスのオブジェクトを他のクラスのメソッドで使用する場合。
（`Name`プロパティを他クラスから設定できるのは本来良い設計ではないですが…）

```c#
public void SetName(Item item)
{
    item.Name = "hoge";
}
```

このとき引数で受け取った `item` がnullであれば、System.NullReferenceExceptionが発生します。

`item` がnull出ないことが確実でない限り、nullチェックを入れますよね。

そこで、今までなら以下のように記述するはずです。
```c#
public void SetName(Item item)
{
    if (item != null)
    {
        item.Name = "hoge";
    }
}

// もしくはパターンマッチングを使ってみたり
public void SetName(Item item)
{
    if (item is not null)
    {
        item.Name = "hoge";
    }
}
```

`if` を使って判定するしかなかったわけです😩

# C#14のnullチェック
いよいよ本題です。

C#14以降では以下のようにスマートに記述することができます。
```c#
public void SetName(Item item)
{
    item?.Name = "hoge";
}
```

'?' を左辺で使用できるようになりました！
左辺がnullでない場合にのみ右辺が評価されるようになります。

簡単にnullチェックができる '?' にはめちゃくちゃお世話になっていますが、代入式の左辺で使用できなかったのは地味に不便だったので非常に助かります。

# でもまだ業務では使えない…
C#14は .NET SDK10 からの対応で現状ではまだプレビュー版しかありません。
ましてや業務で使用しているC#はたしか7.0とかだった気がするので、業務ではもちろん使えません😒

いつか使えるようになるかな…？
今後も最新のリリース情報はこまめに発信していこうと思います。
