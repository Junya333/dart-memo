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
  int lineCount = countEnabled? CountLines() : 0;
  print(lineCount)
}
```

1. `late`キーワードでのちに初期化されることを明示した場合

```dart
late String description;

void main() {
  description = 'Feijoada!';
  print(description)
}
```

便利な場合もあるが、`late`をつけておきながら初期化を忘れた場合は runtime error となってようやくわかるため、バグが埋もれやすいので注意する。

`late`のついた変数は、宣言と同時の初期化を書いたとしても、変数が初めて使われる時に初期化が実行される。これは、以下のような時にうれしい

-   変数初期化の処理が重く、頑張って初期化しても使わないことがあるとき
-   インスタンス変数を初期化しようとしていて、その initializer が`this`にアクセスしないといけないとき(どういうこと?)

### const と final の存在

var の代わりか、型の前につける。
const はコンパイル時に決定する定数、final は再代入を行わない変数に使える。つまり、final したオブジェクトが必ずしも変更できないわけではない。例えばこう

```dart
final name = 'Bob';
final String nickname = 'Bobby';

name = 'Alice';  // 再代入はできないのでerror

const pi = 3.141592;
var radius = 5;
print(radius * radius * pi);
```
