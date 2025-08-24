# Lazyとは？
C#で遅延初期化をサポートをするための機能です。

https://learn.microsoft.com/ja-jp/dotnet/api/system.lazy-1?view=net-8.0

**主な特徴**
- 初回アクセス時までインスタンス生成を遅延
- 必要となるまでオブジェクトを生成しないことで、パフォーマンスとメモリ消費の最適化
- スレッドセーフな遅延初期化

# どんなときに使う？

## 1.アプリ起動時に重い処理や大きなオブジェクト生成を避けたいとき
アプリケーション起動時に大規模なデータ構造や重たい初期化処理を行うと、ユーザビリティの低下につながります。（例えば、大きな配列やデータベース接続、ファイル読み込みなど）

大きいオブジェクトを必要になるときまで初期化を遅延させるために使うことができます。

```c#
var lazyObject = new Lazy<LargeObject>(() => new LargeObject()); // ここではオブジェクトの生成はされない
Console.WriteLine(lazyObject.Value.Data.Count); // Valueにアクセスして初めて初期化される

// メモリ消費量の大きいオブジェクト
public class LargeObject
{
    public readonly List<long> Data = new List<long>();

    public LargeObject()
    {
        for (var i = 0; i < 100_000; i++)
        {
            Data.Add(i);
        }
    }
}
```

## 2.必要になるまでインスタンス化したくない共通部品やキャッシュ
アプリ全体で使用する共通部品は必ずしも利用されるわけではないですよね。
不要な場面でインスタンス化してしまうと、無駄なメモリやCPUリソースを消費してしまいます。

データが必要になるまでは初期化をせず、情報を保持しておくことができます。

```c#
var cache = new Cache();
cache.GetValues().ForEach(x => Console.WriteLine(x));

// アプリ共通データを保持するキャッシュクラス
public class Cache
{
    private static readonly Dictionary<int, string> dic = new Dictionary<int, string>
    {
        {1, "One"},
        {2, "Two"},
        {3, "Three"},
    };

    public Lazy<Dictionary<int, string>> cache;

    public Cache()
    {
        this.cache = new Lazy<Dictionary<int, string>>(() => dic);
    }

    public List<string> GetValues()
    {
        // 必要なときだけインスタンス化する
        return cache.Value
            .Select(x => x.Value)
            .ToList();
    }
}
```

## 3.DIによるサービスの遅延解決
依存性注入(DI)では、サービスや部品をコンストラクタやプロパティを通じて外部から注入しますが、そのサービスが早いタイミングで使用されるとは限りません。

注入されたタイミングではサービスを解決せず、利用されるときに初めて解決することができます。

```c#
// サービスをDIで受け取るクラス
public class Consumer
{
    private readonly Lazy<IMyService> myService;

    public Consumer(Lazy<IMyService> service)
    {
        this.myService = service;
    }

    // このメソッドが呼ばれて初めてIMyServiceが解決される
    public void Execute() => this.myService.Value.DoSomething();
}

// サービスのインターフェース
public interface IMyService
{
    public void DoSomething();
}
```

# 注意点
`Lazy`はオブジェクトの"遅延初期化"をスレッドセーフを保証してくれますが、生成されたオブジェクトの操作に対してまで保証してくれるものではありません。

```c#
var lazyObj = new Lazy<LargeObject>(() => new LargeObject());
var obj = lazyObj.Value; // Lazy<T>はここまでをスレッドセーフにしてくれる

obj.Data = 123; // ← ここから先はLargeObjectクラス側の責任
```

# 参考
https://www.hanachiru-blog.com/entry/2023/01/30/120000

https://www.perplexity.ai/search/c-nolazy-9goQvCTURUGHiE_0h6ua7g