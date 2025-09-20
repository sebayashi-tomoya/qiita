# privateメンバがアクセスできる範囲
C#において、privateメンバがアクセスできる範囲は**同じクラスであるか**です。
当たり前のことと思うかもしれませんが、下記のようなコードを想像してみてみださい。

```c#
// 銀行の口座情報を管理するクラス
public class BankAccount
{
    // 名義
    private string ownerName;

    // 残高
    private int balance;

    public BankAccount(string ownerName, int balance)
    {
        this.ownerName = ownerName;
        this.balance = balance;
    }

    // 他銀行への振り込み
    public void Withdraw(BankAccount target, int amount)
    {
        this.balance -= amount;
        target.balance += amount; // 他インスタンスのprivateメンバにアクセスしている
    }
}
```

このコードを見たとき、私は「別インスタンスならprivateメンバにアクセスできないのでは？🤔」と思ったわけです。

ただ、調べてみるとインスタンスが別かどうかは関係なく、同じクラスの実装であればアクセスできるらしい！

https://learn.microsoft.com/ja-jp/dotnet/csharp/language-reference/keywords/private

C#に触れて3年ほど経ちますが、恥ずかしながらこの事実は知らなかった…
ということでこの記事の執筆に至ったわけです。

# なぜそのような実装になっているのか？
じゃあ同じクラスであればアクセス可能とした理由はなんですか？という疑問に至りますよね。
きっと頭のいい人が考えたであろうC#の言語仕様ですから、しっかりと理由がありました。

## ずばり、EqualやCloneメソッドの実装を考慮しているため！
EqualやCloneメソッドをオーバーライドする際はどうしても別インスタンスのフィールドやプロパティにアクセスする実装になることが多いと思います。

例えば、こんな感じ。
```c#
class Point
{
    private int x, y;

    public Point(int x, int y)
    {
        this.x = x;
        this.y = y;
    }

    public override bool Equals(object obj)
    {
        if (obj is Point other)
        {
            // 他の Point の private フィールドにアクセスできる
            return this.x == other.x && this.y == other.y;
        }
        return false;
    }
}
```

そんなときに、アクセス修飾子を拡張するしかないとなるとカプセル化の利点が失われてしまいます。

同じクラス内でしか触っていないのに外部からもアクセス可能になってしまうからですね。
思い返せばこのような実装やったことありましたね… 意識してなかった…

# まとめ
privateメンバの目的は、あくまでも「外部に見せない」こと。
外部というのは別インスタンスではなく、別のクラス（型）を指します。

異なるインスタンスでも、同じクラスであれば中身を知っているはずなのでprivateでもアクセスを許容してもカプセル化の本質が崩れることはありません。

カプセル化の本質を守りつつ、柔軟性を確保するためにこのような仕様になっていたわけです。


