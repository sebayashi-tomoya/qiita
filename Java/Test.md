私も問題を解いてみました。
実装までのフローをまとめたのでご参考までに。

# 問1: 掛け算表の出力
引数で受け取ったnumに応じて掛け算表を出力するメソッドを作る。

## STEP1: 問題文、解答欄を見て要点整理
```java
// 9x9の掛け算表を出力する
System.Out.Println("9x9の掛け算表");
multiplicationTable(9);

private static void multiplicationTable(int num){
    /* 解答記入位置ここから */

    /* 解答記入位置ここまで */
}
```

### この時点で考えること
- 呼び出し元から渡されるのはint型の整数（この数字を元に掛け算表を作成したい）
- メソッドの戻り値がvoidのため、メソッド内で掛け算表の出力を完結させる必要がある
  （何か値をreturnするメソッドではない）

## STEP2: メソッドで実装するフローを考える
出力したいのは num x num の掛け算表。<br/>
つまり、受け取った num 分の行と列を作ることを考えれば良い。

一旦ここまでで実装してみる。
```java
private static void multiplicationTable(int num){
    /* 解答記入位置ここから */

    // 行数分のループ
    for (int row = 1; row <= num; row++){
        // 列数のループ
        for (int col = 1; col <= num; col++){
            System.Out.Printf("A");
        }
        // 行ごとに改行
        System.Out.Println();
    }

    /* 解答記入位置ここまで */
}
```

出力結果
```
9x9の掛け算表
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
AAAAAAAAA
```

これで num=9 の場合は9x9の表が作成できる。
現状は全て"A"が出力される。

ここまでできればあとは"A"を期待する値に変えるだけ。
行 × 列（コードで言えば`row` ×　`col`）で求められる。

# 問2: n年後が西暦何年で、その年がうるう年かを出力
引数で受け取った値を元にうるう年かどうか判定するメソッドを作る

## STEP1: 問題文、解答欄を見て要点整理
```java
public class Test02{
    // 今年の定義
    private static final int thisYear = 2025;

    private static void main(String[] args){
        int[] latestList = {-25, -125, -5, 0, 75};

        for (int later : latestList){
            System.Out.Printf(
                "%d年後は%d年で%s\n`",
                later,
                this.Year + later,
                (isLeapYear(later) ? "うるう年です" : "平年です")
            );
        }
    }

    private static Boolean isLeapYear(int later){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }
}
```

### この時点で考えること
- `thisYear`は定数、メソッド内で使いそう
- `latestList`はn年後のnに該当(問題文を見ればわかるはず)
- ループ内の処理は`latestList`一個一個に対して実行される処理
- `isLeapYear`メソッドに渡される引数はn年後のn(2025にnが加算された後の数字ではないので要注意)
- 作成するメソッド(`isLeapYear`)はBooleanを戻り値としている

Stringのパラメータ変換や三項演算子の詳細についてはここでは触れません。

## STEP2: メソッドで実装するフローを考える
まずやらないといけないのは引数で受け取った`later`を`thisYear`に加算して判定対象の西暦を求めること。

```java
private static Boolean isLeapYear(int later){
    /* 解答記入位置ここから */

    // 判定対象の西暦を求める
    int targetYear = thisYear + later;

    /* 解答記入位置ここまで */
}
```

その後は、`targetYear`問題文の条件に沿ってうるう年か判定していくだけ。
ここでは模範解答とは別のフローを紹介。

条件を整理すると、<br/>
1. 4で割り切れる歳はうるう年
2. ただ、100で割り切れる年は平年
3. 2のうち、400で割り切れる年はうるう年

1だけであれば、`return (targetYear % 4 == 0);`すれば済む。<br/>
しかし、2,3に該当するケースがあるため単純にはいかない。

そういうときは邪魔なケースを先にリターンすることで解決できる。

邪魔なケースは確定で平年としたいケース。<br/>
2,3を言い換えると**100で割り切れるかつ400で割り切れない数は平年**となる。

```java
private static Boolean isLeapYear(int later){
    /* 解答記入位置ここから */

    // 判定対象の西暦を求める
    int targetYear = thisYear + later;

    /// 100で割り切れる & 400で割り切れないケースは平年
    if (targetYear % 100 == 0 && targetYear % 400 != 0){
        return false;
    }

    // それ以外は4で割り切れるかで判定
    return (targetYear % 4 == 0);

    /* 解答記入位置ここまで */
}
```

# 問3: 3の倍数と3のつく数字でアホになる
引数で受け取った整数を3の倍数または3のつく数字か判定するメソッドを作る。

## STEP1: 問題文、解答欄を見て要点整理
```java
public class Test03{
    public static void main(String[] args){
        for (int i = 1; i <= 40; i++){
            if (isAho(i)){
                System.Out.Println(i + "(アホ)");
            }
            else{
                System.Out.Println(i)
            }
        }
    }

    private static Boolean isAho(int num){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }
}
```

### この時点で考えること
- ループは1~40までの数値をループして処理している
- if の条件式内の`isAho`メソッドはBooleanを返す必要がある


## STEP2: メソッドで実装するフローを考える
これは単純に引数で受け取ったnumが3の倍数または3のつく数字ならtrueを返すメソッドを作れば良い。

int型の値に任意の値が含まれているかどうか判定する`contains`は知らないと使えないが、覚えとくと便利。

### パターンA: 一個ずつ判定（模範解答と一緒）
```java
// 3の倍数ならその時点でtrue
if (num % 3 == 0){
    return true;
}

// それ以外は3が含まれるかで判定
return String.valueOf(num).contains("3");
```

### パターンB: 一緒に判定
```java
return (num & 3 == 0) || String.valueOf(num).contains("3");
```

# 問4: 銀行口座の入金、出金、振り込み、残高照会
クラスとインスタンスを理解して、各インスタンスで銀行口座としての振る舞いを実装する

## STEP1: 問題文、解答欄を見て要点整理
```java
public class Test04{
    public static void main(String[] args){
        // 口座開設
        BankAccount takeda = new BankAccount("竹田", 20000);
        BankAccount naitou = new BankAccount("内藤",  4000);

        // 以下省略
    }
}

// 銀行口座クラス
class BankAccount{
    // 名義
    private String ownerName;
    // 残高
    private int balance;

    // コンストラクタ
    // 別クラスからnewされた（インスタンス生成された）ときはここが呼ばれる
    public BankAccount(String ownerName, int balance){
        this.ownerName = ownerName;
        this.balance = balance;
    }

    // 入金
    public void deposit(int amount){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }

    // 出金
    public void withdraw(int amount){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }

    // 他口座への振り込み
    public void withdraw(BankAccount target, int amount){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }

    // 残高照会
    public void getBalance(){
        System.Out.println(this.ownerName + "さんの口座照会" + this.balance + "円");
    }
}
```

### この時点で考えること
- `main`メソッド内で竹田用と内藤用の銀行口座インスタンスが生成されている
- その後は各インスタンスのメソッドを呼び出して処理実行

## STEP2: メソッドで実装するフローを考える
落ち着いて各メソッドでやりたいことをまとめられればそこまで難しくはない。

### 入金
- `amount`が0以上であるかチェック →　マイナス値ならエラーとしてそのまま処理終了
- `balance`に`amount`を加算（入金額を残高に加算）

### 出金
- `amount`が0以上であるかチェック →　マイナス値ならエラーとしてそのまま処理終了
- `balance`が`amount`以上であるかチェック →　不足しているならエラーとしてそのまま処理終了
- `balance`から`amount`を引く（残高から出金額を引く）

### 他口座への振り込み
- `amount`が0以上であるかチェック →　マイナス値ならエラーとしてそのまま処理終了
- `balance`が`amount`以上であるかチェック →　不足しているならエラーとしてそのまま処理終了
- 自インスタンスの`balance`から`amount`を引く（自分の口座から振り込む金額を残高から引く）
- 別インスタンスである`target`の`balance`に`amount`を加算（振り込まれる側は残高が増える）

コツは自分のインスタンスのフィールドに触っているのか、他のインスタンスのフィールドに触っているのかをしっかり区別して実装を進めること。

# 問5: n番目の素数
指定されたn番目の素数を返すメソッドを作る。

## STEP1: 問題文、解答欄を見て要点整理
```java
public class Test05{
    public static void main(String[] args){
        int[] list = { 3, 5, 10, 25 };

        for (int n : list){
            System.Out.Println(n + "番目の素数は" + findNthPrime(n));
        }
    }

    // n番目の素数を返すメソッド
    public static int findNthPrime(int n){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }

    // 素数かどうかを判定するメソッド
    public static Boolean isPrime(int num){
        /* 解答記入位置ここから */

        /* 解答記入位置ここまで */
    }
}
```

### この時点で考えること
- `list`は判定したいn番目のnに該当する（問題文を見ればわかるはず）
- ループ処理は`list`の数分素数を見つける処理 ＋ コンソール出力を実行
- `findNthPrime`はn番目のnの部分を受け取って、見つけ出した素数を返す
- 対象の数字が素数かどうか判定するメソッドとn番目の素数を返すメソッドが分かれている（実際のフローも分けて考えた方がいい）

## STEP3: メソッドで実装するフローを考える
まず、n番目の素数を返す`findNthPrime`がどのような振る舞いをすべきか考える。

nを受け取る<br/>
↓<br/>
1から順番に素数かどうか判定していく（ループ）<br/>
↓<br/>
素数だったらそのカウントを覚えておく（変数に入れる）<br/>
↓<br/>
素数のカウントがnと一致したらその時点での素数をreturnする<br/>

ここまでで`findNthPrime`は実装できる。
素数かどうか判定する条件はこの時点で考える必要はない。

```java
// n番目の素数を返すメソッド
public static int findNthPrime(int n){
    /* 解答記入位置ここから */

    // 見つけた素数が何番目かを保持しておく
    int count = 0;
    // 素数かどうか判定する対象の数字
    int num = 1;

    // ループ回数の上限が定まっていないためwhileを使う
    while (true){

        if (isPrime(num)){
            // 素数だったらカウントを上げる
            count ++;
        }

        // カウント数 = n、つまりn番目の素数が見つかった場合、その時点での数を返す
        if (count == n){
            return num;
        }

        // 順番に素数か判定しなければならないためループごとにnumを+1
        num ++;
    }

    /* 解答記入位置ここまで */
}
```

※　nに0以下の数値が渡された場合は無限ループになるため、引数チェックを入れてもいい。

後は`isPrime`メソッドの中身を実装するだけ。
ループで回しながら引数に渡している`num`が1と自分自身で割り切れるかどうか判定する条件を実装。