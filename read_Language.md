# 読んでなんとなくわかったことをメモする

## Variables

### 変数の宣言方法

型を書けば変数宣言ができるが、型は推測が効く。

```dart
int age = 13;
var greeting = 'Junya333'
```

型が定まらない時は、`Object`か`dynamic`を使う

```dart
Object name = 'Junya333'
name = 333  //  エラーにならない
```

Dart は null 安全らしい。静的解析で null に対してメソッドが実行されることがわかると、コンパイルが通らない。 被演算子として null が認められている`toString()`や`hashCode`などは大丈夫。
挙動はこう。

-   通常の変数宣言では、null を許容しない。null を認めるなら、T?とする
-   nullable は初期化がなければ null で初期化される。non-nullable は代入が必須(例外を後述)

特に、トップレベル変数とクラス変数は、変数が初めて使われたときに初期化される。

```dart
// int height;  // この行は怒られる
int height = 179  // これならOK
int? lineCount  // これはOK
assert(lineCount == null);  // nullで初期化されている
```

ちなみに、、、  
開発中は、assert が false なら exception を投げる。  
production code なら assert は無視される。

### 初期化を伴わない non-nullable が許される場合

1. ローカル変数で、使用前に必ず初期化される場合

```dart
void main() {
  int lineCount;

  if (countEnabled) {
    lineCount = CountLines();
  } else {
    lineCount = 0;
  }

  print(lineCount)
}
```

wiki の例を持ってきたが、これなら三項演算子を使ったほうがいいと思う。

```dart
void main() {
  int lineCount = countEnabled ? CountLines() : 0;
  print(lineCount)
}
```

2. `late`キーワードでのちに初期化されることを明示した場合

```dart
late String description;

void main() {
  description = 'Feijoada!';
  print(description)
}
```

便利な場合もあるが、`late`をつけておきながら初期化を忘れた場合は runtime error となってようやくわかるため、バグが埋もれやすいので注意する。

`late`のついた変数は、宣言と同時の初期化を書いたとしても、変数が初めて使われる時に初期化が実行される。これは、以下のような時にうれしい:

-   変数初期化の処理が重く、頑張って初期化しても使わないことがあるとき
-   インスタンス変数を初期化しようとしていて、その initializer が`this`にアクセスしないといけないとき(どういうこと?)

### const と final の使い方

var の代わりか、型の前につける。
const はコンパイル時に決定する定数、final は再代入を行わない変数に使える。つまり、final したオブジェクトが必ずしも変更できないわけではない。  
例えばこう

```dart
final name = 'Bob';
final String nickname = 'Bobby';

name = 'Alice';  // 再代入はできないのでerror

const pi = 3.141592;
var radius = 5;
print(radius * radius * pi);
```

## Operator

演算子の話。基本的なところは Python と同じ。
`expr++`、`++expr`、`expr--`、`--expr`が使えるのと、三項演算子`cond ? expr1 : expr2`が使える点は一応あげておく。

expression が null にならないことを、明示するときは、`expression!`とする

他にも、`null`という特別なオブジェクトに対して`?`を使った表現が何個かある。

```dart
String playerName(String? name) => name ?? 'Guest';
```

補足: アロー構文で、return だけのメソッドを簡潔に記述できる。  
これを使わないと、以下のクソ長コードを書くことになる。

```dart
String playerName(String? name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

### Type test operator (型テストのための演算子)

実行時に型を確認するための演算子  
`as` : 型がわかりきっている場合にのみ使う。違ったら error  
`is` : 型が一致するかで true/false を返す。  
`is!`: is の not

### Assignment operator (代入演算子)

`??=`で、「変数が null なら代入する」という処理ができる。

```dart
int? foo;
int? bar = 1;

foo ??= 2;
bar ??= 2;  // barはnullでないので、変更されない

print(foo)  // 2
print(bar)  // 1
```

<!-- TODO: unsigned shiftってなに -->

<!-- TODO: cascade notationってなに、null許容のやつも知りたい。可読性が下がりそうだなという感じ -->
