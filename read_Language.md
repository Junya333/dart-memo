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

Dartはnull安全らしい。nullに対してメソッドが実行される場合はコンパイルが通らない。
被演算子としてnullが認められている`toString()`や`hashCode`などは大丈夫。
挙動はこう。

- 通常の変数宣言では、nullを許容しない。nullを認めるなら、T?とする
- nullableは初期化がなければnullで初期化される。non-nullableは代入が必須(例外を後述)

特に、トップレベル変数とクラス変数は、変数が初めて使われたときに初期化される。

```dart
// int height;  // この行は怒られる
int height = 179  // これならOK
int? lineCount  // これはOK
assert(lineCount == null);  // nullで初期化されている
```

ちなみに、、、
開発中は、assertがfalseならexceptionを投げる。
production codeならassertは無視される。

### 初期化を伴わないnon-nullableが許される場合

1. ローカル変数で、使用前に必ず初期化される場合
例えば以下。
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

wikiの例を持ってきたが、これなら三項演算子を使ったほうがいいと思う。

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

便利な場合もあるが、`late`をつけておきながら初期化を忘れた場合はruntime errorとなってようやくわかるため、バグが埋もれやすいので注意する。

`late`のついた変数は、宣言と同時の初期化を書いたとしても、変数が初めて使われる時に初期化が実行される。これは、以下のような時にうれしい
- 変数初期化の処理が重く、頑張って初期化しても使わないことがあるとき
- インスタンス変数を初期化しようとしていて、そのinitializerが`this`にアクセスしないといけないとき(どういうこと?)

### constとfinalの存在

varの代わりか、型の前につける。
constはコンパイル時に決定する定数、finalは再代入を行わない変数に使える。つまり、finalしたオブジェクトが必ずしも変更できないわけではない。例えばこう

```dart
final name = 'Bob';
final String nickname = 'Bobby';

name = 'Alice';  // 再代入はできないのでerror

const pi = 3.141592;
var radius = 5;
print(radius * radius * pi);
```
