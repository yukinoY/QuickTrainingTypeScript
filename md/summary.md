# Summary

## Part2 データ型

- letかconstの宣言が必ず必要
```
[構文]変数の宣言
let name: type = initial;
- name: 変数名
- type: データ型
- initial: 初期値
```
```
データ型の種類
- number(int/float)
- string
- boolean
- symbol
- any
- オブジェクト
```

- let命令ではデータ型を省略して宣言できる
```
初期値を代入した場合はその型で扱われる
let data = 100;
data = 150; // OK
data = ‘hoge’; // NG
```
```
初期値を代入しなかった場合はany型として扱われる
let data;
data = 150; // OK
data = ‘hoge’; // OK
```

- const命令では初期値を省略できない
```
const DATA; // NG
```

- constは「再代入できない」のであって、「変更」はできる（配列など）

- 初期値をundefined/nullとした場合はany型として扱われる
```
let = data = undefined;
data = 150; // OK
data = ‘hoge’; // OK
```
```
ずっとany型として扱われるのではなく、その時々で変わる
let data = undefined;
data = 150; // number
data = ‘hoge’; // string
```

- リテラル表現
```
数値リテラル
- 16進数 0x
- 8進数  0o
- 2進数  0b
```
```
数値セパレーター
- _で区切る
文字列リテラル
- `では改行も表現できる
```
```
型アサーション（型キャスト）
function show(result: string) {
  return `結果は${result}です`;
}

console.log(show(<any>100)); // OK
```

## Part3

- 配列
```
[構文]配列の宣言
let name: type[] = initial;
- name: 配列名
- type: 要素のデータ型
- initial: 初期値（配列リテラル）

（別解: let name: Array<type> = initial;
```

- 多次元配列
```
let data: number[][] = [[10, 20], [30, 40], [50, 60]];
次元の数だけプラケット（[]）を置く
```

- 読み取り専用の配列
```
readonlyキーワード
let data: readonly string[] = [‘Java’, ‘Python’];
data[1] = ‘Python 3’; // NG
```
```
ただしreadonlyがチェックするのは一次元目の要素だけ
let data: number[][] = [[10, 20], [30, 40], [50, 60]];
data[1][1] = 5;
console.log(data);
// [[10, 20], [30, 5], [50, 60]]

readonlyが効くパターン
data[1] = [30, 45];
```

- 連想配列
```
[構文]連想配列の宣言
let name: {[index: i_type]: v_type} = initial;
- name: 配列名
- i_type: インデックスのデータ型
- v_type: 値のデータ型
- initial: 初期値（オブジェクトリテラル）

★index はインデックスシグニチャなので任意の名前で良い（keyなど）
```
連想配列を使用する際は型宣言が実質必要。

- 列挙型
```
[構文]列挙型
enum ename {name, ...}
- ename: 列挙型の名前
- name: 定数
// a.列挙体を定義
enum Sex {
  MALE,
  FEMALE,
  UNKNOWN
}

// b.MALEにアクセス
let m: Sex = Sex.MALE;
console.log(m); // 0
console.log(Sex[m]); // MALE
```
```
任意の値を割り当てることも可能
enum Sex {
  MALE = 1,
  FEMALE = 2
}
enum Sex {
  MALE = 'male',
  FEMALE = 'female'
}
```

- タプル型
```
let data: [string, number, boolean] = ['hoge', 10.355, false];
console.log(data[0].substring(2)); // ge
console.log(data[1].toFixed(1)); // 10.4
console.log(data[2] === false); // true
```

## Part4 関数

- function命令による宣言
```
[構文]function命令
function name(param: ptype, …) : rtype {…}
- name: 関数名
- param: 仮引数
- ptype: 仮引数のデータ型
- rtype: 戻り値のデータ型
```

- 関数リテラルによる宣言
```
let triangle = function(base: number, height: number): number {
  return base * height / 2;
}
console.log(triangle(10, 5)); // 25

この場合、triangleの型宣言をしていないので型推論によりfunction型となる
```
```
型宣言を明示的にしたい場合
[構文]関数型の宣言
(param: ptype, …) => rtype
- param: 仮引数
- ptype: 仮引数のデータ型
- rtype: 戻り値のデータ型

例）
let triangle:(base: number, height: number) => number =
  function(base: number, height: number): number {
    return base * height / 2;
  }
```

- アロー関数（ラムダ式）による宣言
```
[構文]アロー関数
(param: ptype, ...): rtype => {}
- param: 仮引数
- ptype: 仮引数のデータ型
- rtype: 戻り値のデータ型

例）
let triangle = (base: number, height: number): number => {
  return base * height / 2;
}

短くも書ける
Let triangle = (base: number, height: number): number => base * height / 2;
```

- データ型
```
void … 戻り値なし
never … 例外スロー、無限ループで戻り値なし
```

- 省略可能な引数
```
「?」を付ける
function addLine(count? :number) { ...
```

- 引数にデフォルト値を設定する
```
function addLine(count: number = 1) {…

補足
  - 任意引数の後ろに必須引数は配置できない
  - デフォルト値には式も指定できる
```

- 可変長引数
```
「...」を付ける
function addChar(...char :number[]) { …

「…」は配列で受け取るので[]必須
```

- オーバーロード
  - メソッドを実装する際は、オーバーロードした全ての引数を受け取れるように引数はany型を選択する

## Part5 高度な型
- 共用型
```
let data: number | boolean;
data = 0; // OK
data = true; // OK
data = 'hoge'; //NG

配列の場合
let data: (number | boolean) = [ ... ];

戻り値
function addLine(count: number): number | boolean { ...

引数
function addLine(count: number | boolean): void { ...
```

- 型ガード<br>
引数にnumberとstringを設定した時...<br>
toUpperCase()など両方に当てはまらないメソッドを使用する場合<br>
if文で分岐させる必要がある。<br>
プリミティブ型の場合 `typeof`<br>
クラス型の場合 `instanceof`<br>

- ユーザー定義の型ガード関数<br>
ここはまだ理解が必要。

- unknown型<br>
中身がわからないので、何でもありの型。<br>
any型と違う点は、「中身がわからない」ので ｀.trim()` など型に依存するものは使えない。<br>
使う場合は `typeof` を使って中身が何なのか判定する必要がある。<br>
　→ 型チェックを必ず行う必要があるので、any型より安全に使える

- null非許容型<br>
tsconfig.jsonでnullの代入を禁止するように設定できる。<br>
オプションを有効にした状態で許可するには、以下のようにする。
```
let count = number | null = null;
let day = number | undefined = undefined;
```

- null許容型に便利な演算子<br>
unknown型と同じく、null/undefined許容型のメンバーにアクセスするには<br>
オブジェクトがnull/undefinedでないことを判定する必要がある。
```
「?.」を付ける
let currentCount = count?.toString();

連続でも呼び出せる
hoge?.fuga?.bar();
```

- null/undefinedの場合にデフォルト値を設定する「??」演算子
```
let hoge = fuga ?? 1;

例）
文字列長を取得する例
let hoge = str?.length ?? 0;
```

- 型エイリアス
```
任意の方に対してエイリアスを設定する
主にタプル型や、共用型に用いる

例）
type Kata = [number. boolean, string];
let Cata: Kata = [1, true, 'あ’];

type Hoge = number | string;
```
タプル型及び共用型以外でエイリアスを使いたくなったら、<br>
インターフェースで実現できないかを考える

- 文字列リテラル型
```
特定の文字列を型として利用できる仕組み

例）
type Name = ‘myoji’ | ’name’;

function getName(n: Name) {
}
getName('myoji'); // OK
getName('middle'); // NG
```

- リテラル型と型推論<br>
`const` は 数値リテラル<br>
`let` は 型推論<br>
```
const count = 1; // 常に1
let day = 12; // number型
```
