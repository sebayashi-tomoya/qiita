# 基礎編
まずは使用頻度の高い基礎編から。
LINQはここから覚えよう！

<table border="1">
  <thead>
    <tr>
      <th>メソッド名</th>
      <th>主な用途</th>
      <th>典型的な使い方</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Select</td>
      <td>変換や射影</td>
      <td>コレクションの各要素を別の形に変換する</td>
    </tr>
    <tr>
      <td>Where</td>
      <td>抽出・絞り込み</td>
      <td>条件を満たす要素のみを抽出する</td>
    </tr>
    <tr>
      <td>ToList / ToArray</td>
      <td>コレクション化</td>
      <td>クエリ結果をList型や配列型に変換する</td>
    </tr>
    <tr>
      <td>OrderBy / OrderByDescending</td>
      <td>並び替え</td>
      <td>昇順や降順で要素を並べ替える</td>
    </tr>
    <tr>
      <td>First / FirstOrDefault</td>
      <td>最初の要素取得</td>
      <td>先頭の要素／先頭がなければデフォルト値取得</td>
    </tr>
    <tr>
      <td>Any / All</td>
      <td>条件判定</td>
      <td>いずれか／全ての要素が条件を満たすか判定</td>
    </tr>
    <tr>
      <td>Count</td>
      <td>要素数カウント</td>
      <td>コレクション内の要素数を取得する</td>
    </tr>
  </tbody>
</table>

## 実装例
### Select
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("Select(要素をすべて2倍)");
var doubledNums = nums.Select(n => n * 2);
foreach (var n in doubledNums)
{
    Console.WriteLine(n);
}
```

```
Select(要素をすべて2倍)
2
4
6
8
10
```

### Where
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("Where(偶数のみを抽出)");
var evenNums = nums.Where(n => n % 2 == 0);
foreach (var n in evenNums)s
{
    Console.WriteLine(n);
}
```

```
Where(偶数のみを抽出)
2
4
```

### ToList, ToArray
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("ToArray(配列化)");
var arrayNums = nums.ToArray(); // Listから配列への変換
Console.WriteLine(arrayNums.GetType());

Console.WriteLine("ToList(リスト化)");
var numList = arrayNums.ToList(); // 配列からListへの変換
Console.WriteLine(numList.GetType());
```

```
ToArray(配列化)
System.Int32[]
ToList(リスト化)
System.Collections.Generic.List`1[System.Int32]
```

### OrderBy, OrderByDescending
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("OrderByDescending(降順)");
var descNums = nums.OrderByDescending(n => n);
foreach (var n in descNums)
{
    Console.WriteLine(n);
}

Console.WriteLine("OrderBy(昇順)");
var ascNums = descNums.OrderBy(n => n);
foreach (var n in ascNums)
{
    Console.WriteLine(n);
}
```

```
OrderByDescending(降順)
5
4
3
2
1
OrderBy(昇順)
1
2
3
4
5
```

### First, FirstOrDefault
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("First(最初の要素を取得)");
// 条件なし
Console.WriteLine(nums.First());
// 条件あり(条件に合致する中で最初の要素を返す)
Console.WriteLine(nums.First(n => n % 2 == 0)); 
// 条件に一致する要素がない場合は例外が発生する
try
{
    Console.WriteLine(nums.First(n => n > 5));
}
catch (InvalidOperationException e)
{
    Console.WriteLine(e.Message);
}

Console.WriteLine("FirstOrDefault(最初の要素を取得)");
// 条件なし
Console.WriteLine(nums.FirstOrDefault());
// 条件あり(条件に合致する中で最初の要素を返す)
Console.WriteLine(nums.FirstOrDefault(n => n % 2 == 0));
// 条件に一致する要素がない場合は0 (objectの場合はnull) を返す
Console.WriteLine(nums.FirstOrDefault(n => n > 5));
```

```
First(最初の要素を取得)
1
2
Sequence contains no matching element
FirstOrDefault(最初の要素を取得)
1
2
0
```

### Any, All
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };

Console.WriteLine("Any(コレクション内に条件に一致する要素が一つでも存在するか)");
Console.WriteLine(nums.Any(n => n % 2 == 0));
Console.WriteLine(nums.Any(n => n > 5));

Console.WriteLine("All(コレクション内のすべての要素が条件に一致するか)");
Console.WriteLine(nums.All(n => n < 10));
Console.WriteLine(nums.All(n => n % 2 == 0));
// 空リストに対してAllを使うとtrueが返ってくる
var emptyList = new List<int>();
Console.WriteLine(emptyList.All(n => n % 2 == 0));
```

```
Any(コレクション内に条件に一致する要素が一つでも存在するか)
True
False
All(コレクション内のすべての要素が条件に一致するか)
True
False
True
```

### Count
```c#
var nums = new List<int> { 1, 2, 3, 4, 5 };
var emptyList = new List<int>();

Console.WriteLine("Count(要素数のカウント)");
Console.WriteLine(nums.Count());
Console.WriteLine(emptyList.Count());
```

# 使う機会少ないけど使えたらカッコいい編
次は頻繁には使わないけど覚えておくといざというときに役立つ編。
これを習得すればスマートなコードを書けること間違いなし！

<table border="1">
  <thead>
    <tr>
      <th>メソッド名</th>
      <th>主な用途</th>
      <th>典型的な使い方</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Reverse</td>
      <td>コレクションを逆順に</td>
      <td>コレクションの要素順を反転させて新しいシーケンスを返す<br>
<code>var reversed = collection.Reverse();</code></td>
    </tr>
    <tr>
      <td>ToLookup</td>
      <td>高速な辞書的アクセスの変換</td>
      <td>コレクションをキーでグループ化し、キーに紐づく複数要素をまとめる<br>
<code>var lookup = collection.ToLookup(x => x.Key);</code></td>
    </tr>
    <tr>
      <td>OfType</td>
      <td>型フィルタリング</td>
      <td>指定した型にキャスト可能な要素のみ抽出する<br>
<code>var filtered = collection.OfType<DesiredType>();</code></td>
    </tr>
  </tbody>
</table>

## 実装例
### Reverse
```c#
var strArray = new[] { "A", "C", "Z", "B" };

Console.WriteLine("Reverse(逆順処理)");
foreach (var item in strArray.Reverse())
{
    // アルファベットの昇順・降順に関わらず単純に逆順で処理
    Console.WriteLine(item);
}
```

```
Reverse(逆順処理)
B
Z
C
A
```

### ToLookup
```c#

// ジョジョキャラを例にやってみます
public class Character
{
    public string? Name { get; set; }
    public int Part { get; set; }
}

// 部ごとにオブジェクト化してリストへ格納
var characters = new List<Character>
{
    new Character { Name = "Jonathan", Part = 1 },
    new Character { Name = "Elenor", Part = 1 },
    new Character { Name = "Joseph", Part = 2 },
    new Character { Name = "SuziQ", Part = 2 },
    new Character { Name = "Jotaro", Part = 3 },
    new Character { Name = "Avdol", Part = 3 }
};

// Lookup化して部ごとに分類
ILookup<int, Character> lookup = characters.ToLookup(c => c.Part);

Console.WriteLine("ToLookUp(辞書的アクセス可能なオブジェクトへの変換)");

foreach (IGrouping<int, Character> group in lookup)
{
    // key = Character.Part
    Console.Write($"{group.Key}部:");
    foreach (var character in group)
    {
        Console.Write($" {character.Name}");
    }
    Console.WriteLine(); // 改行
}
```

```
ToLookUp(辞書的アクセス可能なオブジェクトへの変換)
1部: Jonathan Elenor
2部: Joseph SuziQ
3部: Jotaro Avdol
```

### OfType
```c#
ArrayList arrayList = new ArrayList { "記事を", 2, 6, "読んでくれて", null, 8, "ありがとう", 10, "!" };


// stringだけを抽出
IEnumerable<string> strings = arrayList.OfType<string>();

Console.WriteLine("OfType(型フィルタリング)");
foreach (var str in strings)
{
    Console.Write(str);
}
```

```
OfType(型フィルタリング)
記事を読んでくれてありがとう!
```