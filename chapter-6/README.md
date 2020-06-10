## class命令
```
[構文]クラスの定義
class name {
  pname: ptype;

  ...

  constructor(param: type, ...) {
    statements
  }

  mname(param: type, ...): rtype {
    statement
  }

  ...

}

- name: クラス名
- pname: プロパティ名
- ptype: プロパティのデータ型
- mname: メソッド名
- param: コンストラクター/メソッドの仮引数
- type: 引数のデータ型
- rtype: 戻り値のデータ型
- statement: コンストラクター/メソッドの本体
```
- コンストラクターには戻り値の型を指定できない。


## アクセス修飾子
- `private` ... クラスの外からも自由にアクセス可能（デフォルト）
- `protected` ... 同じクラス、またはその派生クラスのメンバーからのみアクセス可能
- `private` ... 同じクラスからのみアクセス可能

例）
```
class User {
  private name: string;
  private age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  // publicは付けてもつけなくても同じ
  public show(): string {
    // クラス内からはアクセスできる
    return `${this.name}は${this.age}歳です。`;
  }

let user = new User('太郎', 12);
console.log(user.show()); // 太郎は12歳です。
console.log(user.name); // エラーとなる
```

### #によるプライベートフィールド
- 「#」と従来のアクセス修飾子は併用不可
例）
```#age = 12;```

- privateとの違い
```
class User {
  private name = '太郎';
  #age = 12;
}

let user = new User();
console.log(user.name); // エラー
console.log(user.#age); // エラー
console.log(user['name']); // 太郎
console.log(user['#age']); // エラー
```
`private`の場合 ... ブラケット構文ではアクセスできる
`#〇〇`の場合 ... ブラケット構文でもアクセスできない

## プロパティの初期化
クラスによっては、コンストラクターとは別にインスタンス化の後でプロパティの初期化をしたい状況がある。<br>
その場合、コンストラクターでの初期化を省略すると、エラーが発生する。<br>
そのような場合には、プロパティの末尾に「!」を付与する。<br>
例）`age! :number;` 

一般の変数にも利用できる。
例）`let x!: string;`

「?」（任意プロパティ）にも似ているが、代入が必要であることを明示できる。

### コンストラクタの省略構文
コンストラクターの初期化式を簡略化するための省略構文が用意されている。

```
class User {
  private name: string;
  private age: number;

  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
```

⇅これらは同義

```
class User {
  constructor(private name: string, private age: number) {
  }
}
```

## getter/setterアクセサー
```
[構文]getter/setterアクセサー
get pname(): type {
  return this.vname;
}

set pname(param: type) {
  this.vname = param;
}

- pname: プロパティ名 （例 **age**）
- type: プロパティのデータ型（例 number）
- vname: プロパティ値を格納するprivate変数（例 **_age_**）
- param: 設定値を受け取る仮引数（例 value）
```

これらを利用した例）
```
class User {
  private _age!: number;

  get age(): number {
    return this._age;
  }

  set age(value: number) {
    if (value < 0) {
      throw new RangeError('ageプロパティは整数で指定してください');
    }
    this._age = value;
  }
}

let user = new User();
user.age = 10;
console.log(user.age); // 10
```
getterの場合にはプロパティのように扱えている。

### 利用するうえでのメリット
1. 読み書きを制御できる<br>
どちらかを省略することで、読み取り専用/書き込み専用を表現できる。
1. 値チェック/戻り値の加工が可能<br>
getter/setterともにコードブロックなので、処理が差し挟める。<br>
内部的に値の持ち方が変更になっても、呼び出し側に影響が及ばない。

## 静的メンバー
static修飾子を用い、静的メンバー（静的メソッド/プロパティ）を定義できる。
```
class User {
  public static count: number = 0;

  public static addUser(): number {
    return this.count + 1;
  }
}

console.log(User.count); // 0
console.log(User.addUser()); // 1
```
クラス内で静的メンバーにアクセスする際も、`this`を使用する。

## 継承
```
[構文]継承
class name extends parent {
  definition
}

- name: クラス名
- parent: 継承元となるクラス名
- definition: 派生クラスの定義
```

### オーバーライド
- super.〇〇() という呼び出し方で、親クラスのメソッドを実行できる。<br>
例）
```
show(): string {
  return super.show() + `${this.age}です`;
}

```
コンストラクターは`super(○○, 〇〇)`で呼び出す。

### 抽象メソッド
初期化のコンストラクター、実装してほしいメソッドなどのみを定義しておく。
```
abstract class User {
  constructor(procted name: string) {}
  abstract getName(): string;
}

class Account extends User {
  getName(): string {
  return this.name;
  }
}

let user = new User('花子');
console.log(user.getName());
```
- 抽象メソッドを定義するには`abstract`修飾子をつける。<br>
その際、「{}」はつけずに「;」で終わること。
- 抽象メソッドを継承した場合、オーバーライドが必須となる。

## インターフェース
- ざっくり言うとすべてのメソッドが抽象メソッドである特別なクラス」
- インターフェースは同時に複数のものを継承（実装）できる（例 implements Hoge, Fuga）
```
[構文]インターフェース宣言
interface name {
  definition
}

- name: インターフェース名
- definition: インターフェースの定義
```

インターフェースには以下のような制限がある。
```
1. メソッドはすべて抽象メソッドであること。abstract修飾子は利用しない。
2. すべてのメンバーはpublicであり、アクセス修飾子は指定できない。
3. staticメンバーは宣言できない。
```

### インターフェースの継承
- インターフェースを継承して、新たなインターフェースを宣言することが可能
- クラスを継承することも可能<br>
この場合、クラスの実装は無視されてシグニチャだけが継承される
```
interface Hoge extends Foo, Bar { ... }
```
```
class User {
  show() { ... }
}

// Userを継承したHogeインターフェース
interface Hoge extends User { ... }
```

### 構造的部分型
- TypeScriptでは、型の互換性（=何が派生クラスなのか）を判断する基準として**構造的部分型**を採用している。
例）
```
interface Human {
  getAge(): number;
}

// getAge()を持つが、明示的にHumanインターフェースを実装しない
class User {
  constructor(private name: string, private age: number) {}
  
  // getAge()を実装
  getAge(): number {
    return this.age;
  }
}

let user: Human = new User('花子', 10);
console.log(user.getAge());
```
明示的に特定のクラス/インターフェースを継承/実装しなくても、<br>
UserクラスはHumanインターフェースが備えるメソッドをすべて備えている為、互換性があるとみなされる。

### 型としてのthis
例）
```
class Count {
  constructor(private _value: number) {}

  get value(): number {
    return this._value;
  }

  plus(value: number): this {
    this._value += value;
    return this;
  }

  minus(value: number): this {
    this._value -= value;
    return this;
  }
}

let count = new Count(15);
console.log(count.plus(10).minus(5).value); // 20

```
- 戻り値を自分自身とすることで、メソッドチェーンのような記述ができる。
- 型を明示的にせず、thisとすることで呼び出し元のクラスに応じて型が変化するため、<br>
TypeScriptではthis型のことを**Polymorphic this types**（多態性あるthis型）と呼ばれている。