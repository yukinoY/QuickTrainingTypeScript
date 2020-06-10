# Part.7 型注釈としてのインターフェース
TypeScriptでは、インターフェイスをオブジェクト指向構文のほかに、型注釈として利用できる。

## 7.1 インターフェイスの基本
例）
```
interface Animal {
  type: string;
  run(): void;
}

let dog: Animal = {
  type: '犬',
  run() {
    console.log(`${this.type}が走ります。`);
  }
};

dog.run(); // 犬が走ります。
```
インターフェイスは型注釈として利用可能。

### オブジェクト型リテラル
インターフェースを定義するまででもないが、型情報をその場限りで明示したい場合、**オブジェクト型リテラル**を利用できる。<br>
例）
```
let animal: {
  type: string; // 「;」は「,」でも可
  weight: number; // 「;」は「,」でも可
} = {
  type: '犬',
  weight: 650
};
```
もっと具体的な例）
```
class Count {
  plus() { ... }
  minus() { ... }
}

let x: { plus(); } = new Count();
x.plus();
x.minus(); // エラー
```
その時々で不要な機能にアクセスできないよう、変数に制限ができる。

## さまざまな型注釈

### プロパティシグニチャ
- ?: 省略可能なプロパティ
- readonly: 読み取り専用プロパティ
例）
```
interface User {
  readonly name: string;
  age?: number;
};

let user = { name: '太郎' } // ageは省略可能
user.name = '花子'; // エラー。読み取り専用プロパティへの代入は不可
```

### コールシグニチャ
関数型を宣言するためのシグニチャ。
宣言形式 `(引数: 型, …): 戻り値`
例）
```
interface Count {
  (x: number, y: number): number; // number型の引数x, yを受け取り、number型の戻り値を返す
};

let count: Count = function(a: number, b: number): number {
  return a + b;
};
```
仮引数の名前は異なっていてもOK。（この例では x => a, y => b）

### メソッドシグニチャ
メソッド型を宣言するためのシグニチャ。
宣言形式 `メソッド名(引数: 型, …): 戻り値`
例）
```
interface Count {
  add(x: number, y: number): number; // number型の引数x, yを受け取り、number型の戻り値を返すaddメソッド
};

let count: Count = {
  add(a: number, b: number): number {
    return a + b;
  }
};
```
メソッドは本質的には関数型のプロパティである為、プロパティシグニチャとして以下のように表しても同じ意味にあんる。
` add: (x: number, y: number)=> number `
ただしこの場合は戻り値型は「=> 型」で表す。

### インデックスシグニチャ
ブラケット構文で型を表現する。<br>
連想配列の宣言は冗長になりがちな為、繰り返し利用する際はインターフェイスとして宣言しておくのがおすすめ。
例）
```
interface Data {
  [index: string]: number;  // 「文字列: 数値, ...」の配列を定義
}

let list: Data = {
  'hundred': 100,
  'thounsand': 1000,
};
```
### コンストラクターシグニチャ
コンストラクターの方はnewキーワードで宣言できる。<br>
例）
```
interface Animal {
  new(type: string, weight: number): Dog;
}
```