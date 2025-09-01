# 直面した問題
DBからデータを取得した際に、その値がnullだった場合にの処理をC#側で実装したかったのですが、以下のようなコードを書きました。
(使用していたDBはSQLiteです)

id と name が格納されている users テーブルがあると想定してください。

```c#
using System;
using System.Data.SQLite;

// データベース接続・クエリ実行メソッド
public void ExecuteQuery(string query, Action<SQLiteDataReader> action)
{
    using (var conn = new SQLiteConnection("Data Source=test.db"))
    {
        conn.Open();
        using (var cmd = new SQLiteCommand(query, conn))
        using (var reader = cmd.ExecuteReader())
        {
            while (reader.Read())
            {
                action(reader); // readerを渡す
            }
        }
    }
}

// クエリ生成メソッド
public string SelectWhereId(int id)
{
    const int nameIndex = 0;
    string result = string.Empty;
    var query = $"SELECT name FROM users WHERE id={id}";

    // 取得後の値をラムダで渡して処理する
    ExecuteQuery(query, reader =>
    {
        var fetchedName = reader.GetString(nameIndex);
        
        // データ取得できなければその旨を出力する
        // 単純にC#のnullと比較
        if (fetchedName is null)
        {
            Console.WriteLine("name が取得できませんでした");
        }
        else
        {
            result = fetchedName;
        }
    });
    return result;
}

```

これでいけるやろ！と実行してみた結果、なぜか期待通りの結果にならない。
値がないはずのid で検索しても `if (fetchedName is null)` が true にならないわけです。

# なぜnullチェックができなかったのか？
C# の`SqlDataReader`では値が取得できなかったとき、一般的な null として返ってきません。
`System.DBNull`として返ってきます。

公式ドキュメントの説明は以下。

> オブジェクト指向プログラミング言語の の概念 null を オブジェクトと DBNull 混同しないでください。 オブジェクト指向プログラミング言語では、 null はオブジェクトへの参照がないことを意味します。 DBNull は、初期化されていないバリアントまたは存在しないデータベース列を表します。

https://learn.microsoft.com/ja-jp/dotnet/api/system.dbnull?view=net-9.0&viewFallbackFrom=net-9.0-pp

このことを知らずにしばらく途方にくれていました😮‍💨

# どのようにnullチェックすべきだったのか？
となると、次は C# で DB を扱う際に値が存在しなかったときはどうすればいいの？という疑問が浮かぶと思いますが、
もちろんフレームワーク内で考慮されてます。

`SqlDataReader`クラスには、`IsDBNull(int)`というメソッドが用意されています。
このメソッドを使えば簡単に値が存在しないケースの処理が実装できます。

https://learn.microsoft.com/ja-jp/dotnet/api/system.data.sqlclient.sqldatareader.isdbnull?view=net-9.0-pp

## 使い方
先ほどのメソッドを修正してみましょう。

```diff
public string SelectWhereId(int id)
{
    const int nameIndex = 0;
    string result = string.Empty;
    var query = $"SELECT name FROM users WHERE id={id}";
    ExecuteQuery(query, reader =>
    {
-       var fetchedName = reader.GetString(nameIndex);
        // データ取得できなければその旨を出力する
-       // 単純にC#のnullと比較
-       if (fetchedName is null)
+       // IsDBNullで値の存在チェック
+       if (reader.IsDBNull(nameIndex))
        {
            Console.WriteLine("name が取得できませんでした");
        }
        else
        {
-           result = fetchedName;
+           // IsDBNullがfalseの場合のみ取得処理を実行する
+           result = reader.GetString(nameIndex);
        }
    });
    return result;
}
```

これでOK！
初めて使うライブラリのソースはちゃんと読まなきゃダメですね...