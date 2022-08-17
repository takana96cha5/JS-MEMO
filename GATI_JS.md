# JSのメモ ガチのやつ

## 前提知識

### 変数

```js
// 変数
let a = 'a'
// 定数
const b = 'b'
```

### 関数

```js
const name = "Tom";

function hello(name) {
    console.log(`hello ${name}`);
};

hello(name);
```

### オブジェクト

```js
let obj = {
    // 変数
    property1: '',
    // 関数
    property2: function() {
    },
    // オブジェクト
    property3: {
        d: "Bye"
    }
}

// アクセス方法
obj.name
obj['name']
```

### ECMAScript

- JavaScript言語のコアの部分を ECMAScript として仕様策定 → ブラウザ間での仕様を統一
- Java Script とは ECMAScript の仕様に基づいて実装されているプログラミング言語
- Java Script は環境によって使える機能が異なる
  - クライアント(ブラウザ)環境: ECMAScript ＆ WebAPIs(DOMなど)
  - サーバー(Node.js)環境: ECMAScript ＆ CommonJS(モジュール管理)

### ES5 と ES6

- Living Standard: 機能毎に塩生を策定し、仕様が決まったものから、最新版の仕様書に順次追加していく。
- ECMAScript の仕様書: <https://tc39.es/ecma262/>

### 実行環境

- ブラウザ
  - User Interface
  - Browser Engine
  - Data Strage
  - Rendering Engine
  - JavaScript Engine
    - ECMAScript
    - WebAPIs
      - DOM API
      - WebRTC
      - Fetch API
      - XHR API
      - Geolocation API
      - etc...
  - Networking
  - UI backend

実行環境 まとめ

- JavaScriptはJavaScriptエンジンで実行される。
- 一番シェアがあるJavaScriptエンジンはV8と言われる。
- JavaScriptから WebAPIs を通じてブラウザを操作する。

### コードの実行

- 実行前
  - JavaScriptエンジン
    - コード
    - Window: JS Engine が提供するブラウザへのインターフェース
      - WebAPIs: JS Engine が提供する便利機能
    - this: コンテキストによって取得できるオブジェクトが変わる
    - グローバルオブジェクト: JSエンジンによって生成されるコード内のどこからでもアクセスできるオブジェクト
- まとめ
  - JSの実行前にはグローバルオブジェクトとthisが準備される。
  - ブラウザのグローバルオブジェクトは Window オブジェクトとなる。

### 実行コンテキスト

コードを実行する際の文脈・状況

- グローバルコンテキスト(main.js 直下)
  - 実行中のコンテキスト内の変数・関数
  - グローバルオブジェクト
  - this
- 関数コンテキスト
  - 実行中のコンテキスト内の変数・関数
  - arguments
  - super(継承先のクラスが継承元のクラスを参照する際に使用)
  - this
  - 外部変数(関数の外で定義された変数)
- evalコンテキスト(非推奨)

### コールスタック

実行中のコードがたどってきたコンテキストの記録

コールスタックはLIFO(後入れ先出し)

```js
function a() {
}
function b() {
    a();
}
function c() {
    b();
}
c();

// コールスタック
// a ← 一番上のコンテキストが実行中
// b
// c
// グローバル

```

### ホイスティング(宣言の巻き上げ)

コンテキスト内で宣言した変数や関数の定義をコード実行前にメモリーに配置すること。

```js
a(); // 既に関数がメモリ上に配置されているため実行可能

const a = functon() { // 関数式による関数宣言 こちらの定義の場合は変数のホイスティングと挙動が同じになる
    console.log(c);
    let c = 1;

    d(); //d is called
    function d() {
        console.log('d is called');
    };
    console.log('a is called');
}

var b ;

console.log(b); // undefined (var の使用は非推奨)

b = 0; // 値を更新

console.log(b); // 0

let b = 0;   // Uncaught Error (var と異なり undefined による初期化が行われないためエラーが発生)
const b = 0; // Uncaught Error

```

### JSエンジンによる挙動の違い

Chrome: V8エンジン

Fire Fox: Spider Monkey

---

## スコープ

実行中のコードから値と式が参照できる範囲のことをスコープといい、内側のスコープから外側のスコープは見えるが、外側のスコープから内側のスコープは見えない。

- グローバルスコープ = Windowオブジェクト
- スクリプトスコープ = Scriptオブジェクト
- 関数スコープ = 関数宣言の後に来る波カッコ{}の中のスコープのこと
- ブロックスコープ = 関数宣言の後にない、波カッコ{}の中のスコープのこと
- モジュールスコープ =

グローバルスコープとスクリプトスコープ

```js
let a = 0; // Script のプロパティ スクリプトスコープ
var b = 0; // Window のプロパティ グローバルスコープ
function c() {}; // Window のプロパティ グローバルスコープ

debugger; // JavaScript の実行がこの行で止まる。

console.log(b); // window を省略できる。 つまり、window.console.log(window.b); でも同様

window.d = 1; // Window のプロパティ グローバルスコープ var d = 1; と同様

let d = 2; // d の値は 2 に上書きされる。スコープチェーンに基づき参照される値が決定される。

```

### 関数スコープとブロックスコープ

```js

function a() { // 関数スコープ
    let b = 0;
    console.log(b);
}

// a(); // Error

// 変数
{
    // ブロックスコープ
    // ブロックスコープを生成するためには let または const で変数の定義を書かないといけない。
    // var で定義した変数はブロックスコープが生成されない。

    let c1 = 1; // ブロックスコープ
    const c2 = 1; // ブロックスコープ
    var d = 1; // グローバルスコープ
    console.log(c1); // 取得可能
    console.log(c2); // 取得可能
    console.log(d); // 取得可能

}
console.log(c1); // 取得不可
console.log(c2); // 取得可能
console.log(d); // 取得可能

// 関数
{
    function a() { // グローバルスコープ
        console.log('a is called');
    };

    const b = function(){ // ブロックスコープ
        console.log('b is called');
    }
}


// 一般的に if文 や for文 と一緒にブロックスコープは使用される

if() {
    // ブロックスコープを適用したいので var の使用禁止
}

for() {
    // ブロックスコープを適用したいので var の使用禁止
}
```

### スコープと実行コンテキスト

グローバルコンテキスト

- 実行中のコンテキスト内の変数・関数 = グローバルスコープ
- グローバルオブジェクト
- this

関数コンテキスト

- 実行中のコンテキスト内の変数・関数 = 実行中の関数スコープ
- arguments
- super
- this
- 外部変数

レキシカルスコープ

レキシカル(語彙, 辞書): ソースコードのどこに何を書いているかという意味。

レキシカルスコープ: コードを書く場所によって参照できる変数が変わるスコープ。つまり、どのようにしてスコープを決定するかというプログラミング言語の使用のこと。コードを記述(字句を解析)した時点でスコープが決定するため「静的スコープ」とも言う。対となる概念に、コードの実行時にスコープがダイナミックスコープがある。

下の例では レキシカルスコープは、実行中のコードから見た外部スコープのことを言う。

```js
let a = 2;
function fn1() {
    let b = 1;
    function fn2() {
        let c = 3;
        consle.log(b); // 呼び出し可能
    }
    fn2();
}

// スコープの可視化
// グローバルスコープ
// a, fn1
//   関数スコープ(fn1)
//   b, fn2
//     関数スコープ(fn2)
//     c

// －－－－－－－－－－－－－－－－－－－－－－－－－－－－
// ここでは外部変数がレキシカルスコープになっている
let a = 2;

function fn1() {
    let b = 1;
    fn2();
}

function fn2() {
    let c = 3;
    consle.log(b); // 外部スコープが変わったため呼び出し不可
}

// －－－－－－－－－－－－－－－－－－－－－－－－－－－－－

```

### スコープチェーン

スコープが複数階層で、連なっている状態

スコープが複数階層になって連なっている場合は、一番内側のスコープから変数を探しに行って、1つづつ外側のスコープに同じ変数名がないかを確認しに行きます。

グローバルスコープはスクリプトスコープより外側のスコープです。

```js
let a = 2; // スクリプトスコープ
window.a = 1; // グローバルスコープ
function fn1() {
    let a = 1; // fn1 の関数スコープ
    function fn2() {
        let a = 3; // fn2 の関数スコープ
        consle.log(a);
    }
    fn2();
}

```

### クロージャー: レキシカル(外部)スコープの変数を関数が使用している状態

クロージャーを使った実装

- プライベート変数の定義
- 動的な関数の生成

```js
// プライベート変数の定義

// Factory関数の中にプライベート変数と内部関数を定義する
// 内部関数はプライベート関数の値を参照する
// Factory関数は内部関数の関数オブジェクトを返す
// 関数を生成する関数をFactory関数と呼ぶ

function incrementFactory() {
    let num = 0;
    function increment() {
        num += 1;
        console.log(num);
    }
    return increment;
};

const increment = incrementFactory();

increment(); // 1
increment(); // 2
increment(); // 3
```

```js
// 動的な関数の生成
// Factory関数の引数を内部関数で利用している
// つまり、Factory関数の宣言時に渡された引数がプライベート変数として設定される

function addNumberFactory(num) {
    function addNumber(value) {
        return num + value;
    }
    return addNumber;
};

// 生成される関数が引数によって動的に変わる
const add5 = addNumberFactory(5);
const result = add5(10);
console.log(result); // 15

const add10 = addNumberFactory(10);
const result = add10(10);
console.log(result); // 20

```

### 即時関数(IIFE)

関数定義と同時に一度だけ実行される関数。実行結果が呼び出し元に返却される。名前を付ける必要がない

```js
// 即時関数を実行する場合は関数の定義部分を丸カッコ()で囲む必要がある。
let result = (function(仮引数){
    return 戻り値;
})(実引数);


function a() {
    console.log('called');
}

a();
(a)();

// 即時関数にも戻り値と引数を設定することが可能。
let c = (function a(d) {
    console.log('called' + d);
    return 0
})(10);

c();

// 関数式を即時関数として使用する場合は即時関数の定義を囲む丸カッコは不要。
let b = function() {
    console.log('called');
}();


// 即時関数のユースケース
let d = (function() {
    console.log('called');

    let privateVal = 0;
    let publicVal = 10;

    function privateFn() {
        console.log('privateFn is called');
    }
    function publicFn() {
        console.log('publicFn is called' + privateVal++); // プライベート変数にを参照している
    }
    return {
        publicVal, // publicVal: publicVal の略記法
        publicFn   // publicFn: publicFn の略記法
    };
})();

console.log(c.publicVal);
c.publicFn();

```

### クロージャーによるプライベート変数の復習

```js
{
    let num = 0; // increment で使用するためのプライベート変数として定義
    function increment() { //内部関数
        num += 1;
        console.log(num);
    }
}
num; // 参照エラー
increment(); // 1
increment(); // 2
increment(); // 3
increment(); // 4

```

### 四則演算を行うメソッドを持った関数を定義

```js
function calcFactory(val) {
    return {
        plus: function(target) {
            const newVal = val + target;
            console.log(`${val} + ${target} = ${newVal}`);
            val = newVal;
        },
        minus: function(target) {
            const newVal = val - target;
            console.log(`${val} - ${target} = ${newVal}`);
            val = newVal;
        },
        multiply: function(target) {
            const newVal = val * target;
            console.log(`${val} * ${target} = ${newVal}`);
            val = newVal;
        },
        devide: function(target) {
            const newVal = val / target;
            console.log(`${val} / ${target} = ${newVal}`);
            val = newVal;
        },
    }
}

const calc = calcFactory(10);

// calcFactory を即時関数として書き直す
const calc = (function(val) {
    return {
        plus: function(target) {
            const newVal = val + target;
            console.log(`${val} + ${target} = ${newVal}`);
            val = newVal;
        },
        minus: function(target) {
            const newVal = val - target;
            console.log(`${val} - ${target} = ${newVal}`);
            val = newVal;
        },
        multiply: function(target) {
            const newVal = val * target;
            console.log(`${val} * ${target} = ${newVal}`);
            val = newVal;
        },
        devide: function(target) {
            const newVal = val / target;
            console.log(`${val} / ${target} = ${newVal}`);
            val = newVal;
        },
    }
})(10);

calc.plus(5);
calc.minus(3);
calc.multiply(4);
calc.devide(2);

```

### 変数宣言を行うキーワード

| タイプ | 再宣言 | 再代入 | スコープ | 初期化(ホイスティング) |
| ---- | ---- | ---- | ---- | ---- |
| let | × | ○ | ブロック | × |
| const | × | × | ブロック | × |
| var | ○ | ○ | 関数 | undefined |

### 暗黙的な型変換: 変数が呼ばれた状況によって変数の方が自動的に変換されること

```js

// 型の確認用関数
function printTypeAndValue(val) {
    console.log(typeof val, val);
}

let a = 0;
printTypeAndValue(a);

let b = '1' + a;
printTypeAndValue(b);

let c = 15 - b;
printTypeAndValue(c);

let d = c - null;
printTypeAndValue(d);

let e = d - true;
printTypeAndValue(e);

```

厳格な等価性: (a === b)型の比較まで行う

抽象的な等価性: (a == b)型の比較は行わない

```js
function printEquality(a, b) {
    console.log(`strict: ${a === b}, loose: ${a == b}`);
}

let a = '1';
let b = 1;

let c = true;

printEquality(a, b); // strict false, loose true

let e = '';
let f = 0;
let g = "0";

printEquality(e, f); // strict false, loose true

```

- falsy な値: Booleanで真偽値に変換した場合に false になる値のこと
  - false
  - null
  - 0
  - undefined
  - 0n
  - NaN (Not a Number)
- truthy な値: Booleanで真偽値に変換した場合に true になる値のこと
  - falsy な値以外

```js

```

### AND条件とOR条件

AND条件: 左から順に評価していき、falsy な値が見つかった時点でその値を結果として返し、見つからなかった場合は最後の値を結果として返す。

OR条件: 左から順に評価していき、truthy な値が見つかった時点でその値を結果として返し、見つからなかった場合は最後の値を結果として返す。

AND と OR を混在して書きたい場合はグループ化を行う

```js
&& // AND
|| // OR
const a = 0;
const b = 1;
const c = 2;
console,log(a && b && c);
console,log(a || b || c);
console,log((a || b) && c);
console,log((a || b) && (c || d));
```

### AND条件とOR条件(応用)

```js
function hello(name = 'Tom') {
    // name = name || 'Tom'; // name の値が空だった場合は 'Tom' が初期値に設定される 0 に注意
    console.log('Hello ' + name);
}

hello('Bob');

let name = 'Bob';
// if(name) {
    // hello(name);
// }
name && hello(name); // このように記述することで name に値が格納されている時のみ関数を実行することができる。

```

### データ型

- プリミティブ型
  - String
  - Number
  - Boolean
  - Undefined
  - Null
  - Symbol
  - BigInt
- オブジェクト(ユーザー定義型)
  - Object(プリミティブ型以外の型)

プリミティブ型: 変数には値が格納される。 一度作成するとその値を変更することはできない。 immutable(不変)

値はメモリ番地に代入されている。再代入は値への参照先が格納された変数用のメモリ番地の値を入れ替えている。

オブジェクト型: 変数には参照が格納される。値を変更することができる。mutable(可変)

オブジェクトは名前と値をペアで管理する入れ物。つまり、名前(プロパティ)付きの参照を管理する入れ物である。

### 参照とコピー

プリミティブ型は値渡し、オブジェクトでは参照渡しが行われる。つまり、オブジェクトではコピー元の値の変更が意図しない変更を生む可能性がある。

```js
let a = 'hello';
let b = a;
b = 'bye';
console.log(a,b); // hello bye

let c = {
    prop: 'hello'
}
let d = c;
d.prop = 'bye';
console.log(c, d); // {prop: "bye"}, {prop: "bye"}
```

### const の挙動

```js
const a = 'hello';
a = 'bye'; // error

const b = {
    prop: 'hello'
}

// b = {}; // error
b.prop = 'bye';
console.log(b);
```

### 参照と引数

```js
function fn(a) {}
let b = 0;
fn(b);
// 上記3行は let a = b; と同じ
```

```js
// プリミティブ型を関数の引数として渡す場合は値はそれぞれ独立しているのでお互いに影響を受けない
let a = 0;
function fn1(arg1) {
    arg1 = 1;
    console.log(a, arg1); // aはレキシカルスコープ arg1は1になっている
}
fn1(a); // 0 1

// 上記のコードは下記のコードと同じである
let arg1 = a;
arg1 = 1;
console.log(a, arg1)
fn1(a); // 0 1
// －－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
// オブジェクトを関数の引数として渡す場合は参照渡しが行われるためお互いに影響を受ける
let b = {
    prop: 0
}
function fn2(arg2) {
    arg2.prop = 1;
    console.log(b, arg2);
}
fn2(b); // {prop: 1} {prop:1}
// －－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
// 参照が渡されるか値が渡されるかは意識する必要がある
function fn3(arg2) {
    arg2 = {}; // オブジェクトを渡す
    console.log(b, arg2);
}
fn2(b); // {prop: 1} {}

```

### 参照と分割代入

分割代入: オブジェクトから特定のプロパティーを抽出して宣言を行う。

分割代入を使用すると Key が持っている値の参照先がコピーされて、新しい変数からの参照が張られる。

つまりオブジェクトの分割代入で中身を抽出した時点で参照は切れ、オブジェクトが持つ Key とは異なる値になる

```js
let {a, b} = object;
```

```js
const a = {
    prop: 0
}

let {prop} = a; // 分割代入を行う際は Keyの名前 と 変数名 とを基本的に一致させる必要がある。
// こちらが一致していないとうまく値が見つからず代入ができない

prop = 1;

console.log(a, prop); // {prop: 0} 1

function fn(obj) {
    let {prop} = obj;
    prop = 1;
    console.log(obj, prop);
}

fn(a); // {prop: 0} 1
```

### オブジェクトが多階層だった場合を考える

```js
const c = {
    prop1: {
        prop2: 0;
    }
}

let { prop1 } = c;
prop1.prop2 = 1;
console.log(c); //{prop1: {...}} prop1: prop2: 1 <- prop1 への参照を持つ
console.log(prop1); // {prop2: 1}

// 分割代入を使って宣言した変数がオブジェクトだった場合にはオブジェクトの参照を保持しているため、分割代入元のオブジェクトにも影響が出る
```

### 参照の比較と値の比較

プリミティブ型では値の比較が行われ、オブジェクト型では参照の比較が行われる

```js
const a = {
    prop: 0
}

const b = {
    prop: 0
}

console.log(a === b); // オブジェクト同士の参照を比較しているため、参照先が変わるとエラーを吐く
console.log(a.prop === b.prop); // オブジェクトの構造を比較したい場合は、プロパティ同士を比較する必要がある
```

---

## 関数とオブジェクト

### 関数の挙動

```js
function fn(a, b) {
    console.log(a, b);
}
fn(1); // b のように定義された引数を呼び出さなくても実行できる b には undefined が入る

function fn(a) { // 重複する関数を定義した場合は後から定義された関数で更新される 引数の数が異なっても同じ関数とみなされる 重複を許したくなければ const を使った関数式で定義すると良い
    console.log(2);
}

function fn(a=1) { // デフォルト値を設定できる
    console.log(a);
}
fn(undefined); // 1 undefined はプログラム側が自動で設定する値なので基本的にデフォルト値が優先される
fn(null); // null は undefined より優先順が高い また、デフォルト値よりも null が優先される

function fn() {
    console.log(arguments); // 仮引数で定義していない実引数を配列で受け取ることができる ES6からレストパラメータの使用が推奨されている。
}
fn(1, "a"); // Arguments[1, "a"]

function fn(...args) { // レストパラメータを用いれば明示的に可変長引数を設定することができる
    console.log(args);
}
fn(1, "a"); // 1, a

```

### 関数とオブジェクトについて

関数は実行可能なオブジェクトである！

```js
function fn() {
    console.log('hello');
}

fn.prop = "prop"
fn.method = function() {
    console.log('method');
}

fn(); // hello
fn.method(); // method
console(fn.prop); // "prop"

```

### コールバック関数

コールバック関数とは他の関数に引数として渡される関数

```js
function hello(name) {
    console.log('hello' + name);
}

function bye(name) {
    console.log('bye' + name);
}

function fn(callback) {
    callback("Tom");
}

fn(hello); // 関数はオブジェクトなので関数の引数に渡すことができる
fn(bye); // 関数はオブジェクトなので関数の引数に渡すことができる

// callback 関数の実例
setTimeout(hello, 2000);
```

### this

this は関数コンテキストで使用されることが多い

this: 呼び出し元のオブジェクトへの参照を保持するキーワード

実行コンテキストによって this の参照先は変わる

呼び出し元のオブジェクトへの参照を保持する

```js
const person = {
    name: 'Tom',
    hello: function() {
        console.log('Hello' + this.name);
    }
}
person.hello();

```

### 参照先のコピーとthis

thisは実行されるオブジェクトの内容によって決まるのではなく、メソッドを実行する方法によって決まります。

オブジェクトのメソッドとして実行される場合 (オブジェクト内で実行される場合)
'this' => 呼び出し元オブジェクト

関数として実行される場合 (関数内で実行される場合)
'this' => グローバルオブジェクト

```js
window.name = 'John'

const person = {
    name: 'Tom',
    hello: function() {
        console.log('Hello' + this.name);
    }
}

person.hello(); // Hello Tom <- person を参照
const ref = person.hello;
ref(); // Hello John <- window を参照

person.hello(); // Hello Tom <- person を参照

function a() {
    console.log('Hello' + this.name); // Hello John <- window を参照
}
```

### コールバック関数とthis

thisは実行されるオブジェクトの内容によって決まるのではなく、メソッドを実行する方法によって決まります。

オブジェクトのメソッドとして実行される場合 (オブジェクト内で実行される場合)
'this' => 呼び出し元オブジェクト

関数として実行される場合 (関数内で実行される場合)
'this' => グローバルオブジェクト

```js
const person = {
    name: 'Tom',
    hello: function() {
        console.log('Hello' + this.name);
    }
}
person.hello(); // Hello Tom <- person を参照

function fn(ref) {
    ref() // この関数はグローバルスコープに属する
}
fn(person.hello); // Hello John <- window を参照 関数に比企恵右宇を

```

### bind と this

thisは実行されるオブジェクトの内容によって決まるのではなく、メソッドを実行する方法によって決まります。

オブジェクトのメソッドとして実行される場合 (オブジェクト内で実行される場合)
'this' => 呼び出し元オブジェクト

関数として実行される場合 (関数内で実行される場合)
'this' => グローバルオブジェクト

bindによるthisの束縛: bindによって this や 引数 を固定した新しい関数を作成できる

```js
const person = {
    name: 'Tom',
    hello: function() {
        console.log('Hello' + this.name);
    }
}
person.hello(); // Hello Tom <- person を参照

const helloTom = person.hello.bind(person);

function fn(ref) {
    ref();
}

fn(helloTom); // Hello Tom <- person を参照

```

```js
function a() {
    console.log('hello' + this.name);
}

const b = a.bind({name: 'Tim'});

b(); // hello Tim
```

```js
function a(name) {
    console.log('hello' + name);
}

const b = a.bind(null, 'Tim'); // bind を使うと第一引数のオブジェクトに this を固定 第二引数の値に引数を固定することができる

b("Tom"); // hello Tim <- person を参照
```

### call,applyと'this'

bind: this や 引数 の参照先を変更。使用時点では実行しない。

call, apply: this や 引数 の参照先を変更。

```js
function a() {
    console.log('hello ' + this.name);
}

const name = {name: 'Tim'}
const b = a.bind(name);

b(); // hello

a.apply(tim); // hello Tim
a.call(tim); // hello Tim
```

```js
function a(name, name1) {
    console.log('hello ' + name + ' ' + name1);
}

const name = {name: 'Tim'}
const b = a.bind(tim);

b(); // hello

a.apply(tim, ['Tim', 'Bob']); // hello Tim Bob <- 配列で複数の引数を受け取る
a.call(tim, 'Tim', 'Bob'); // hello Tim Bob <- 引数で複数の引数を受け取る
```

```js
// 実践的な apply の使い方
const array = [1, 2, 3, 4, 5];

const result = Math.max.apply(null, array); // 配列の最大値を取得 現在はspled 演算子を使うことが推奨されている。
const result = Math.max(...array); // このように記述することで配列を展開して引数に渡すことができる
console.log(result);
```

### アロー関数

アロー関数とは無名関数を記述しやすくした省略記法

() => {};

||無名関数|アロー関数|
| - | - | - |
|this|○|×|
|arguments|○|×|
|new|○|×|
|prototype|○|×|

```js
// 普通の関数定義
function fn(arg) {
    return `arg: ${arg}`;
}

// 無名関数
const fn = function(arg) {
    return `arg: ${arg}`;
}

// アロー関数
const fn = (arg) => {
    return `arg: ${arg}`;
}

// 省略アロー関数
const fn = arg => `arg: ${arg}`;
// 引数が一つの場合は()を省略できる 関数の中身が1行の場合は 波カッコ と return も省略できる
// 引数がない場合は()を渡すか、_ => のようにも書ける

```

### アロー関数とthis

||無名関数|アロー関数|
| - | - | - |
|this|○|×|
|arguments|○|×|
|new|○|×|
|prototype|○|×|

オブジェクトのメソッドとして実行される場合 (オブジェクト内で実行される場合)
'this' => 呼び出し元オブジェクト

関数 または アロー関数 として実行される場合 (関数内で実行される場合)
'this' => グローバルオブジェクト

```js
window.name = 'John'

const person = {
    name: 'Tom',
    hello: () => {
        console.log('Hello ' + this.name); // アロー関数は this や arguments を持たない
    }
}
person.hello(); // Hello John <- window を参照
```

```js
window.name = 'John'

const a = () => console.log('Bye ' + this.name); // ここでの this は window を指す

const person = {
    name: 'Tom',
    hello() { // 無名関数として定義
        console.log('Hello ' + this.name); // Hello John <- window を参照 アロー関数は this や arguments を持たない
        a(); // Hello Tom <- person  を参照
    }
}
person.hello();
```

```js
function b() {
    const a = () => console.log('Bye' + this.name);
    a();
}

b();
```

### おまけcalcFactory

```js
// 一秒後にconsole.log または alert で計算式が表示されるように改造
const calcFactory = (val, callback) => {

    function callbackAfter1s(str) {
        setTimeout(callback.bind(null, str), 1000); // callback関数に引数を渡したいときは bind を使う！ ()を使うと実行されてしまう
    }

    return {
        plus: function(target) {
            const newVal = val + target;
            callbackAfter1s(`${val} + ${target} = ${newVal}`);
            val = newVal;
        },
        minus: function(target) {
            const newVal = val - target;
            callbackAfter1s(`${val} - ${target} = ${newVal}`);
            val = newVal;
        },
        multipy: function(target) {
            const newVal = val * target;
            callbackAfter1s(`${val} * ${target} = ${newVal}`);
            val = newVal;
        },
        devide: function(target) {
            const newVal = val / target;
            callbackAfter1s(`${val} / ${target} = ${newVal}`);
            val = newVal;
        },

    }
}

const calc = calcFactory(10, alert);
calc.plus(5);
calc.minus(3);
calc.multiply(3);
calc.devide(2);
```

### コンストラクタ関数

コンストラクタ関数: 新しくオブジェクトを作成するためのひな形となる関数。

```js
const obj = new A(); // new で作成したオブジェクトを「インスタンス」という。 new でオブジェクトを作成することを「インスタンス化」という。
```

```js
function Person(name, age) { // コンストラクタ関数は最初の文字を大文字にして区別する
    this.name = name;
    this.age = age;
}

// インスタンス化してインスタンスを生成
const bob = new Person('Bob', 18);
const tom = new Person('Tom', 33);
const sun = new Person('Sun', 20);
```

### prototype

プロトタイプ: オブジェクトに存在する特別なプロパティ

プロトタイプ: コンストラクタ関数と合わせて使用

インスタンス化した際には `prototype` の参照が `__proto__` にコピーされる

```js
function Person(name, age) { // コンストラクタ関数は最初の文字を大文字にして区別する
    this.name = name;
    this.age = age;
}

// コンストラクタの prototype にメソッドを定義することで インスタンスの __proto__ プロパティに prototype の参照が追加される
Person.prototype.hello = function() {
    console.log('hello ' + this.name);
}

const bob = new Person('Bob', 18);
const tom = new Person('Tom', 33);
const sun = new Person('Sun', 20);

bob.hello();

```

### new演算子

new 演算子: コンストラクタ関数からインスタンスを作成するために使用する演算子

コンストラクタ関数からの戻り値が**オブジェクト**の場合、新しいインスタンスとして戻り値のオブジェクトを呼び出し元に返す

```js
function F(a, b) {
    this.a = a;
    this.b = b;
    return {}; // 戻り値がオブジェクトの場合
}

const instance = new F(1, 2);
console.log(instance) // {} 戻り値はオブジェクト
```

コンストラクタ関数からの戻り値が**プリミティブ型**の場合、コンストラクタの`prototype`のプロパティをオブジェクトの`__prototype__`にコピーして、コンストラクタ関数内で使用している`this`を呼び出し元に返却する

```js
function F(a, b) {
    this.a = a;
    this.b = b;
    return 1; // 戻り値がプリミティブの場合
}

const instance = new F(1, 2);
console.log(instance) // F {a: 1, b: 2} 戻り値は F の this
```

new演算子と同じ動きをする関数 newOpe を作成する

```js
function F(a, b) {
    this.a = a;
    this.b = b;
}

F.prototype.c = function() {}

function newOpe(Constructor, ...args) {
    const _this = Object.create(Constructor.prototype); // コンストラクタ関数の prototype が __prototype__ に格納された、空のオブジェクト _this を生成する
    const result = C.apply(_this, args); // _this を作成 インスタンス時に this が _that に設定される
    // console,log(args);
    // console.log(typeof result);
    if(typeof result === "object" && result !== null) {
        return result;
    }
    return _this;
}

const instance = newOpe(F, 1, 2);
console.log(instance)

```

### instanceof

instanceof: どのコンストラクタから生成されたオブジェクトかを確認する

```js
function F(a, b) {
    this.a = a;
    this.b = b;
}

F.prototype.c = function() {}

const instance = new F(1,2);
console.log(iinstance instanceof F)

// instanceof の内部の動き
console.log(instance.__proto__ === F.prototype); // instanceof は参照先が同じかを比較している

function fn(arg) {
    if(arg instanceof Array) {
        arg.push('value');
    } else {
        arg['key'] = value;
    }
    console.log(arg);
}

fn([])
```

### 関数コンストラクタ

関数コンストラクタは動的なコードを文字列から生成したいときにまれに使用するが脆弱性があるため非推奨

関数は実行可能なオブジェクトである

```js
function fn() {
    const fnObj = new Function('a', 'b', 'return a + b'); // 関数オブジェクトを作成する
    return fnObj // 関数オブジェクトを戻り値として返す
}

const f = fn(); // f は関数オブジェクト
const result = f(1, 2); // a=1 b=2 f1=a+b
console.log(result); // 3
```

```js
let d = 0; // fn はここの d を読んでいる
function fn() {
    let d = 1; // 関数なのでここで定義されている d は読まれない
    const fnObj = new Function('a', 'b', 'return a * b * d'); // 関数オブジェクトを作成する
    return fnObj // 関数オブジェクトを戻り値として返す
}

const f = fn(); // f は関数オブジェクト
const result = f(1, 2); // 1 * 2 * 0
console.log(result); // 0

```

```js

function fn2(a, b) {
    return a + b;
}

console.log(fn2 instanceof Function);
```

```js
const obj = new function() {
    this.a = 0;
}
const fn3 = new Function('return;');
const obj3 = new fn3();
console.log(obj, fn3);
```

### プロトタイプチェーン

プロトタイプの多重継承をプロトタイプチェーンという。

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.hello = function() {
        console.log('call OwnProperty' + this.name)
    }
}

Person.prototype.hello = funcion() {
    console.log('call Person')
}

Object.prototype.hello = funcion() {
    console.log('call Object')
}

const bob = new Person('Bob', 18);
bob.hello(); // __prototype__ を辿る順番は OwnProperty -> Person -> Object

const result = bob.hasOwnProperty('name'); // __prototype__ ではなくプロパティに値が存在するかを判定
console.log(result); // true

```

### hasOwnProrerty と in

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Object.prototype.hello = funcion() {
    console.log('call Object')
}

const bob = new Person('Bob,', 18);
const result = bob.hasOwnProperty('name'); // ture nameプロパティを持っている
bob.hasOwnProperty('hello'); // false helloはObjectに格納されているので __prototype__ に持っているが、Personのプロパティとしては存在していない
console.log('name' in bob); // true bobの__prototype__にnameを持っているかを調べる プロトタイプチェーンをさかのぼって調べる
console.log('hello' in bob); // ture
```

### プロトタイプ継承

プロトタイプ継承: ある関数の__prototype__を別の関数の__prototype__チェーンに追加すること。別のコンストラクタ関数のプロトタイプを受け継いで、機能を流用できるようにすること。コンストラクタ間で機能の受け渡しができる。

継承: 別のコンストラクタ関数を受け継ぐこと。

```js
function Person(name, age) {
    this.name = name;
    this.age = age;
}

Person.prototype.hello = function() {
    console.log('hello ' + this.name);
}

// 継承 （クラス継承ではない）
function Japanee(name, age) {
    Person.call(this, name, age);
}

// プロトタイプ継承
Japanee.prototype = Object.create(Person.prototype); //

// オーバーライド こちらのほうが優先順位が高いため
Person.prototype.hello = function() {
    console.log('kontiwa ' + this.name);
}

// インスタンス化
const taro = new Japanese('Taro', 23);
console.log(taro);

taro.hello();
```

### クラス

JavaScriptにおけるクラスとはコンストラクタ関数をクラス表記で書けるようにしたもの。つまり、クラスはコンストラクタ関数の糖衣構文。

```js
// クラス定義
class Person {
    constructor(name, age) {
    this.name = name;
    this.age = age;
    }

    hello() {
        console.log('hello ' + this.name);
    }
}

// インスタンス化
const bob = new Person('Bob', 23);
console.log(bob);
```

### クラス継承

クラス継承とは、他のクラスのプロパティとメソッドを継承すること

```js
class Person {
    constructor(name, age) {
        this.name = name;
    this.age = age;
    }

    hello() {
        console.log('hello ' + this.name);
    }
}

class Japanese extends Person {
    constructor(name, age, gender) {
        super(name, age); // コンストラクタ関数内で一番最初に呼び出す必要がある
        this.gender = gender;
    }

    // オーバーライド
    hello() {
        super.hello(); // 継承元の関数を呼び出すことができる
        console.log('kontiwa ' + this.name);
    }
}

// インスタンス化
const bob = new Person('Bob', 23);
console.log(bob);

// インスタンス化
const taro = new Japanese('Taro', 23, 'Male');
console.log(taro);
taro.hello();

```

### super

スーパー: 継承元の関数を呼び出すためのキーワード

```js
const american = {
    hello() {
        console.log('hello ' + this.name);
    }
}

const bob = {
    name: 'Bob',
    hello() {
        super.hello();
    }
}

bob.hello = function() {
    super.hello(); // super は クラスでしか使えないため文法エラーになる
}

Object.setPrototypeOf(bob, american);
bob.hello();

```

### ビルトインオブジェクト

コード実行前にJS エンジンによって自動的に生成され、windowオブジェクトに格納されるオブジェクト

例: String, Object, Number, Function, Math, Boolean, Date, Symbol, etc...

```js
console.log(Array() {[native code]}); // native code は C++ で記述されているコードなので中身は確認できない

const array = new Array(1,2,3,4); // const array = [1,2,3,4] と指定しても同じ
console.log(array); // (4) [1,2,3,4]

array[0]; // 0番目の要素を指定
array["0"]; // 文字型でインデックス番号を指定しても暗黙的な型変換によって数値型に変換される

array.hasOwnProperty(0);
```

### ラッパーオブジェクト

プリミティブ値を内包するオブジェクト

```js
// 文字型を生成
const a = new String('hello'); // const a = 'hello'; と同じ
console.log(a); // String {"hello"}   __prototype__ 内にその型が使用できるメソッドが格納されている。

// 数値型を生成
const b = new Number(100); // const b = 100; と同じ
console.log(b);

```

### Symbol

シンボル: プロパティの重複を避けるために、必ず一意の値を返す関数

シンボルがES6 から導入された経緯は、ES5 の時点で開発者が`String.prototype.iterator`という独自の関数を定義していたと仮定します。
このようなビルドインオブジェクトの拡張はprototype汚染と呼ばれ推奨されていません。
この状態でES6に上がったタイミングで `prototype.iterator` というビルドインオブジェクトが追加された場合は競合を起こして意図した動作にならないということが容易に考えられます。
ここで考え出されたのがシンボルを使ったプロパティの格納と取得になります。
シンボルを使い一意なプロパティやメソッドを定義することで既存の実装に影響を与えず後方互換性を担保することができます。

```JS
const s = Symbol('hello'); // new は用いないことに注意
const s2 = Symbol('hello'); // 引数はシンボル名を指定しているだけなので
console.log(s === s2); // false  // 必ず重複の無い一意な値を返す
console.log(typeof s);

const str = new String('Tom');
console.log(str);

String.prototype.iterator = function() {
    // 独自実装
}
```

### プロパティーとディスクリプター

オブジェクトを定義した際は各プロパティには値である Value をセットすることになります。
プロパティは値以外にも設定値を保持していて、それがディスクリプタと呼ばれています。
ディスクリプタによってプロパティがとる挙動を制御することができます。

- プロパティ
  - 値
    - value: 値を保持
  - ディスクリプタ
    - configurable: 設定変更可能性 設定の書き換えが可能か否か
    - enumerable: 列挙可能性 inなどを使った時に参照されるか 反復可能か
    - writable: 値の変更可能性 writable が false の場合 イミュータブルなオブジェクトになる

```js
const obj = {prop: 0};
const descriptor = Object.getOwnPropertyDescriptor(obj, 'prop');
console.log(descriptor); // 値とディスクリプタが取得できる
```

ディスクリプタの値を設定してみる

```js
'use strict'
const obj = {};
Object.defineProperty(obj, 'prop', {
    value: 0;
})

Object.defineProperty(obj, 'prop', {
    enumerable: true // configurable が false なので descriptor の変更が許可されておらずエラーになる
})

delete obj.prop; // こちらも configurable が false なのでエラーになる

obj.prop = 1; // writable が false のため値は 0 のまま エラーを出したい場合は ファイルの先頭で 'use strict' を指定する
console.log(obj.prop); // 0
const descriptor = Object.getOwnPropertyDescriptor(obj, 'prop');
console.log(descriptor); // 値が 0 , ディスクリプタが全て false となったプロパティを取得できる

```

### getter/setter と static

ディスクリプタには、value, configurable, enumerable, writable の他にも set と get を設定できる

set にはセッターと呼ばれる値を渡す際に実行したい処理を関数として定義できる

getにはゲッターと呼ばれる値を取得する際に実行したい処理を関数として定義できる

```js
function Person1(name, age) {
    this._name = name; // プライベート変数は _(アンダースコア)プレフィックスが必要
    this._age = age;
}

Object.defineProperty(Person1.prototype, 'name', {
    // ゲッター
    get: function() {
        return this._name;
    },
    // セッター
    set: function(val) {
        this._name = val;
    }
});

const p1 = new Person1('Bob', 23);

p1.name = 'Tom'; // セッター
console.log(p1.name); // ゲッター
```

ES6 以降のクラス構文による定義方法

```js
class Person2 {
    constructor(name, age) {
        this._name = name;
        this._age = age;
    }
    // getter
    get name() {
        return this._name;
    }
    // setter
    set name() {
        this._name = val;
    }

    // 静的メソッドの定義(インスタンス化せずに使用することができる静的なメソッド)
    static hello() {
        console.log('hello');

    }
}

Person2.hello(); // 静的メソッドはインスタンス化せずに呼び出し可能
```

### チェーンメソッド

1つのインスタンスについて連続した処理を適用したい時は、チェーンメソッドを使用する

チェーンメソッドは 戻り値に自身のオブジェクト(this)を返すメソッドです。

```js
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    hello(person) {
        console.log(`${this.name} says hello ${person}`);
        return this;
    }

    introduce() {
        console.log(`Hi, I'm ${this.name}, ${this.age}`);
        return this;
    }

    shakeHands(preson) {
        console.log(`${this.name} shake hands with ${person}`);
        return this;
    }

    bye(person) {
        console.log(`Goodbye, ${person.name}`);
        return this;
    }
}

const bob = new Prson('Bob', 23);
const tim = new Prson('Tim', 33);

// チェーンメソッドの呼び出し
bob.hello(tim)
    .introduce()
    .shakeHands(tim)
    .bye(tim);
```

練習問題 クラスと継承

```txt
問題：ログインの制御を行う loginController という名前の関数が存在します。
この関数はuserインスタンスを渡すと

ログイン処理（login）->ユーザー権限チェック（checkRoll）->適切なページへのリダイレクト（redirect）

を内部で行います。

以下の呼び出し方をした場合に該当のメッセージがコンソールに出るように実装してみてください。

******************************************************************************

１．一般ユーザーがログインした時。
loginController(new User('Bob'));

ログイン成功した場合：
User: Bob
you have normal roll
redirect : /
login success

ログイン失敗した場合：
User: Bob
you have normal roll
login failed <- checkRollで失敗した場合

******************************************************************************

２．管理者（AdminUser）でログインした場合
loginController(new AdminUser('Bob'));

ログイン成功した場合：
User: Bob
you have admin roll
redirect : /admin
login success

ログイン失敗した場合：
User: Bob
login failed <- loginで失敗した場合

```

```js
// 一般ユーザー
class User {

    // コンストラクタ
    constructor(name) {
        this.name = name;
        this.redirectTo = '/';
    }

    // ログイン
    login() {
        console.log(`User: ${this.name}`);
        return true;
    }

    // 権限チェック
    checkRoll() {
        console.log('you have normal roll');
        return true;
    }

    // リダイレクト
    redirect() {
        console.log(`redirect : ${this.redirectTo}`);
        return true;
    }

}

// 管理者ユーザー
class AdminUser extends User {

    // コンストラクタ
    constructor(name) {
        super(name);
        this.redirectTo = '/admin';
    }

    // 権限チェック
    checkRoll() {
        console.log('you have admin roll');
        return true;
    }

}

// ログインコントローラー関数
function loginController(user) {

    if (user.login() && user.checkRoll() && user.redirect()) {
        console.log('login success');
    } else {
        console.log('login failed');
    }

}

// loginController(new User('Bob'));
loginController(new AdminUser('Bob'));

```

練習問題: クラスとコールバック

```txt
問題：Person.helloメソッドをsetTimeoutで１秒後に実行しようとしましたが、
"hello Bob"と表示されませんでした。
setTimeoutに渡す方法がおかしそうなのですが、
どのようにすればよいでしょうか？

※２通りの方法で実装してみてください。
```

```js
// 人間クラス
class Person {

    // コンストラクタ関数
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    // ハロー関数
    hello() {
        console.log('hello ' + this.name);
    }
}

const bob = new Person('Bob', 23);

// bindで this が参照するオブジェクトを bob に固定する必要がある
setTimeout(bob.hello.bind(bob), 1000);
setTimeout(Person.prototype.hello.bind(bob), 1000); // 上と同じ意味

setTimeout(function() {
    bob.hello(); // 無名関数の内部で実行されているため
} , 1000);

```

練習問題: チェーンメソッド

```txt
問題：
電卓の入力と同じような挙動をするチェーンメソッドを作成してみましょう。

例えば、次のように使用し、結果が表示されるようにします。


例１）
const calc = new Calculator();

calc.set(10)
.minus()
.set(3) -> '7'を出力(10 - 3)

例２）
const calc = new Calculator();

calc.set(10)
.minus()
.set(3) -> '7'を出力
.mutiply()
.set(6) -> '42'を出力（10 - 3) * 6
```

```js
// 計算機クラス
class Calculator {

    // コンストラクタ
    constructor() {
        this.val = null;
        this._operator;
    }

    // 入出力
    set(val) {

        if(this.val === null) { // 値を保持していない場合
            this.val = val; // 値を保存
        } else {
            // 保持している値 と 引数 に演算子を適用
            this._operator(this.val, val);
        }
        return this; // チェーンメソッド化
    }

    // 和 演算子
    plus() {
        this._operator = function(val1, val2) {
            const result = val1 + val2; // 足し算処理
            this._equal(result); // 結果をthis.val に格納する
        }
        return this; // チェーンメソッド化
    }

    // 差 演算子
    minus() {
        this._operator = function(val1, val2) {
            const result = val1 - val2; // 引き算処理
            this._equal(result); // 結果をthis.val に格納する
        }
        return this; // チェーンメソッド化
    }

    // 積 演算子
    multiply() {
        this._operator = function(val1, val2) {
            const result = val1 * val2; // 掛け算処理
            this._equal(result); // 結果をthis.val に格納する
        }
        return this; // チェーンメソッド化
    }

    // 除 演算子
    divide() {
        this._operator = function(val1, val2) {
            const result = val1 / val2; // 割り算処理
            this._equal(result); // 結果をthis.val に格納する
        }
        return this; // チェーンメソッド化
    }

    // 等価
    _equal(result) {
        this.val = result;  // 結果を次の値としてセットする
        console.log(result);  // 結果を表示
    }

    // 表示
    print() {
        console.log(this.val);
        return this;
    }
}

// インスタンス化
const calc = new Calculator();

// チェーンメソッドで計算
calc.set(10).print()   // 10
    .minus().set(3)    // 7
    .multiply().set(6) // 42
    .divide().set(2)   // 21
    .plus().set(2)     // 23
```

---

## 反復処理

### ループ文

```js
for(let i = 0; i < 10; i++) {
    console.log(i);
}

let i = 0;
while(i < 10) {
    console.log(i);
    i++;
}

```

### 演算子と優先順位

演算子: 値(オペランド)を元に処理を行い、**結果を返す**記号。

<https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Operator_Precedence>

```js
let a = 0;
let b = a++; // 後置
console.log(a, b); // 1 0

let a = 0;
let b = ++a; // 前置
console.log(a, b); // 1 1
```

```js
let a = 0;
let b = a++; // 後置 a = (b = a) + 1;
console.log(a, b); // 1 0

// 上の動きを詳しく見てみる
let a = 0;
let b = a;
a = a + 1;
```

```js
let a = 0;
let b = ++a; // 前置 a = b = a + 1;
console.log(a, b); // 1 1

// 上の動きを詳しく見てみる
let a = 0;
let b = a + 1;
a = b;
```

```js
function fn() {
    let a = 0;
    return a++;
}
console.log(fn()); // 0

// 上の動きを詳しく見てみる
function fn() {
    let a = 0;
    return a;
    a = a + 1;
}
```

### ループ文とブロックスコープ

```js
for(let i = 0; i < 5; i++) {
    const j = i * 2; // ここで const に再代入が行われているがエラーが出ないことから、for 文のブロックスコープ {} はループごとに逐次作成されていることが分かる。つまりループごとに値を累積したいような場合はループの外に累積用の変数を定義する必要がある
    console.log(j);
}
```

### 配列とループ文

```js
const array = [1,2,3,4,5];

for(let i = 0; i < array.length; i++) {
    console.log(array[i]);
}

// while文で上のfor文を書き換える
let v, i = 0;
while(v = array[i++]) { // 配列内に falsy な値(例: 0)が入っていると機能しない
    console.log(v);
}
```

### for...in と列挙可能性

列挙可能性とはディスクリプタの持つ enumerable という設定値のこと

for ... in: 列挙可能プロパティに対して順不同で反復処理を実行する。
また、プロトタイプチェーンも列挙対象となので、自分自身に設定されているプロパティのみを列挙したい場合は、Object.hasOwnProperty()を使うと良い。
Symbol で定義下プロパティは for ... in で列挙対象にならない

```js
const obj = {
    prop1: 'value1',
    prop2: 'value2',
    prop3: 'value3',
}

// for in を使ったループ
for(let key in obj) {
    consolelog(key, obj[key]);
}
```

オブジェクトのプロトタイプに対して、何らかの独自のメソッドを追加してあげた場合、enumerable を false に設定して列挙型から外すと良い

```js
Object.prototype.method = function() {} // このままでは method 自身も列挙対象としてコンソールに上がって列挙されてしまう。基本的には Object自身のプロパティのみ列挙したいことが多いので、enumerable という設定値を false に設定してやる必要がある
Object.defineProperty(Object.prototype, 'method', { // enumerable を false に設定
    enumerable: false
});

const d =Object.getOwnPropertyDescriptor(Object.prototype, 'method');
console.log(d); // enumerable: false <- これで列挙されなくなった
```

### for...fo と反復可能性

イテレーターを持つオブジェクトの反復操作を行う。

イテレーターとは反復可能オブジェクトととも呼ばれる、反復操作を行うに使用するオブジェクト。

String, Array, Map, Set, arguments, etc...

```js
const array = ['a', 'b', 'c'];

array[4] = 'e'; // a b c undefined e と出力される つまり設定されていない値に対しても undefined という形で返却する

Object.prototype.method = function() {} // prototype に定義された独自メソッドは、イテレーターではないので for of で返却されない。

Object.defineProperty(array, 0, { // 配列の0番目の要素の enumerable を false に設定しても、もともと enumerable を参照していないため for of で返却される
    enumerable: false
})

for(let v of array) {
    console.log(v);
}
```

### MapとSet

データを管理する入れ物 -> コレクション

マップは特殊なデータ構造なので、自身のプロパティに値を持つ Object とは構造が異なり、`[[Entries]]`という特殊な場所にあ値を持っている

||Object|Map|
|-|-|-|
|キー|文字列|制約なし|
|for...in|○|×|
|for...of|×|○|

||Array|Set|
|-|-|-|
|重複値|○|×|
|for...in|○|×|
|for...of|○|○|

```js
// Map について 連想配列
const map = new Map();

const key1 = {};
// 追加
map.set(key1, 'value1');
// 取得
console.log(map.get(key1));

const key2 = function() {};
map.set(key2, 'value2');
console.log(map.get(key2));

let key3 = 0;
map.set(key3, 'value3');
console.log(map.get(key3));

map.delete(key3);
console.log(map.get(key3));

// map では for in のループが行えないことに注意
// for of だと配列で取得できる
for(const [k, v] of map) {
    console.log(k, v);
}
```

```js
// Set について 集合
const s = new Set();

// 追加
s.add(key1);
s.add(key2);
s.add(key3);

// 削除
s.delete(key3)

// 存在するか
console.log(s.has(key2)); // true
console.log(s.has(key3)); // false

// const array = Array.from(s);
const array = [...s]; // 上よりこっちの書き方の方がおすすめ
console.log(array);

for(let k of s) {
    console.log(k)
}
```

### イテレーター

反復操作を行う際に使用するオブジェクト

決められたルールによって記述する

```js
// イテレーターの記述方法
function genIterator() {
    return { // イテレーターを返している
        next: function() { // next メソッドを保持
            return { // オブジェクトを返却
                done: true / false, // ループの継続
                value: 値 // 返却する値
            }
        }
    }
}
```

```js
function genIterator(max = 10) { // 引数の max は上限値 デフォルト値を設定していないと無限ループに陥る危険性がある
    let i = 0; // 初期化 クロージャーを用いたプライベート変数
    return { // イテレーターを返す
        next: function() { // イテレーター
            if(i >= max) { // 上限を超えた場合
                return {
                    done: true // 完了
                }
            } else { // 上限値に達していない場合
                return {
                    done: false, // 未完了
                    value: i++ // インクリメント
                }
            }
        }
    }
}

// インスタンス化
const it = genIterator(10); // 0 ~ 9 まで出力される

let a = it.next();
while(!a.done) { // 完了状態になるまで繰り返す
    console.log(a.value); // 現在の値を出力
    a = it.next(); // イテレーターを呼び出す
}

const obj = {
    // イテレーターのシンボルを用意
    [Symbol.iterator]: genIterator.bind(null, 100); // bind で実行せずに引数を渡している
}

for(const i of obj) {
    console.log(i);
}

const s = new Set(obj);
console.log(s);
```

### 反復可能オブジェクト（イテレーター）

イテレーターを使った反復可能オブジェクトの作成

この実装は勉強用途のため現場では非推奨

```js
// 普通オブジェクトの作成
const obj = {
    prop1: value1,
    prop2: value2,
    prop3: value3,
}

// イテレーターを使った反復可能オブジェクトの作成
Object.prototype[Symbol.iterator] = function() {
    const keys = Object.keys(this);  // key 情報を取得 for of が呼ばれる段階で this は obj を参照する
    let i = 0; // key 取得用のインデックス番号
    let _that = this;
    return {
        next() {
            let key = keys[i]; // key 情報を取得
            i++; // インデックス番号用の変数を更新
            // 上の2行は let key = keys[i++]; のように後方インクリメント演算子を使って1行で書ける
            console.log(_that) // this だと next が入ったオブジェクト を参照してしまうため、 _that を利用している
            return {
                value: [key, _that[key]],
                done: i > keys.length
            }
        }
    }
}

// obj は反復可能オブジェクトになった
const items = Object.entries(obj);
for (let [k, v] of items) {
    console.log(k, v);
}
```

### ジェネレーター

イテレーターを生成するための特殊な関数 -> より簡略化して記述可能！

```js
// イテレーターの記述方法
function genIterator() {
    return { // イテレーターを返している
        next: function() { // next メソッドを保持
            return { // オブジェクトを返却
                done: true / false, // ループの継続
                value: 値 // 返却する値
            }
        }
    }
}
```

```js
// ジェネレータの記述方法
function* gen() { // function に * を付けることでこの関数はジェネレータであることを表す
    if(ループ継続) {
        yield 値;
    } else {
        return 値;
    }
}
```

yield とは

yield は ジェネレータ関数の中で使える next メソッドの略記法です。

yield が呼ばれた場合

- done が false
- value が yield に続く値

return が呼ばれた場合

- done が true
- value が return に続く値

ループを終了させたい場合は return を呼びます。

```js
function* gen() {
    yield 1;
    yield 2;
    return 3;
}

const it = gen();
console.log(it.next()); // done: false , value: 1
console.log(it.next()); // done: false , value: 2
console.log(it.next()); // done: true  , value: 3
console.log(it.next()); // done: true  , value: undefined
```

genIterator を ジェネレータ で書き換える

```js
// genIterator
function genIterator(max = 10) { // 引数の max は上限値 デフォルト値を設定していないと無限ループに陥る危険性がある
    let i = 0; // 初期化 クロージャーを用いたプライベート変数
    return { // イテレーターを返す
        next: function() { // イテレーター
            if(i >= max) { // 上限を超えた場合
                return {
                    done: true // 完了
                }
            } else { // 上限値に達していない場合
                return {
                    done: false, // 未完了
                    value: i++ // インクリメント
                }
            }
        }
    }
}

// インスタンス化
const it = genIterator(10); // 0 ~ 9 まで出力される

let a = it.next();
while(!a.done) { // 完了状態になるまで繰り返す
    console.log(a.value); // 現在の値を出力
    a = it.next(); // イテレーターを呼び出す
}
```

```js
// ジェネレータで書き換えたgenIterator
function genIterator(max = 10) {
    let i = 0;

    while(i < max) {
        yield i++;
    }
    return;
}

// インスタンス化
const it = genIterator(10); // 0 ~ 9 まで出力される

let a = it.next();
while(!a.done) { // 完了状態になるまで繰り返す
    console.log(a.value); // 現在の値を出力
    a = it.next(); // イテレーターを呼び出す
}// インスタンス化
const it = genIterator(10); // 0 ~ 9 まで出力される

let a = it.next();
while(!a.done) { // 完了状態になるまで繰り返す
    console.log(a.value); // 現在の値を出力
    a = it.next(); // イテレーターを呼び出す
}

// // 反復可能オブジェクトの生成
// const obj = {
//     [Symbol.iterator]: genIterator
// }
// for(const i of obj) {
//     console.log(i);
// }

// 反復可能オブジェクトの呼び出し 反復可能オブジェクトをわざわざ作成する必要がない!
for(let i of genIterator()) {
    console.log(i)
}
```

ジェネレータと反復可能オブジェクトを組み合わせて定義

```js
// イテレータを設定
const obj = {
    [Symbol.iterator]: function* genIterator(max = 10) {
    // *[Symbol.iterator](max = 10) { と省略することも可能
        let i = 0;

        while(i < max) {
            yield i++;
        }
        return;
    }
}

for(let i of obj) {
    console.log(i);
}
```

### 反復可能オブジェクト（ジェネレーター）

ジェネレーターを使って反復可能オブジェクトを定義する

```js
const items = {
    prop1: 'value1',
    prop2: 'value2',
    prop3: 'value3'
}

// イテレーターを使った定義と比べると超短くなった
Object.prototype[Symbol.iterator] = function*() {
        for(let key in this) {
        yield [key, this[key]];
    }
}

for (let [k, v] of items) {
    console.log(k, v);
}
```

### イテレーターとスプレッド演算子

スプレット演算子とは、 反復可能や列挙可能オブジェクトの展開を行う。

let a = [...array]

残余引数とは、実引数に渡された変数を配列にまとめる仮引数

function(...args)

スプレット演算子はイテレーターの操作に従う

```js
// スプレット演算子

const array1 = [1,2,3,4,5];
const array2 = [...array1]; // 新しい同じ要素を持つ配列を定義
console.log(arra1 == array2); // false

// 引数を全て足し合わせる関数
function sum(...args) {
    let ret = 0;
    for(let v of args) {
        ret += v;
    }
    return ret;
}
const result = sum(1,2,3,4);
console.log(result);
```

```js
// イテレータとスプレット演算子の関係
conost obj1 = {
    prop1: 'value1';
    prop2: 'value2';
    prop3: 'value3';
}

const array4 = { ...obj1 }; // オブジェクトの値渡しでコピーが取れる
console.log(array4)

// イテレータを定義
Object.prototype[Symbol.iterator] = function*() {
        for(let key in this) {
        yield [key, this[key]];
    }
}

const array5 = [ ...obj1 ]; // イテレータにしかスプレット演算子は使えない

console.log(array5)
```

練習問題

ジェネレータ

```js
/**
 * 問題：
 * 引数で与えた範囲の値をステップ毎に返却する
 * genStepというジェネレーター関数を作成しましょう。
 *
 * - genStepの要件
 * 引数にmin, max, stepを取ります。
 * min：下限値
 * max：上限値
 * step：ステップ
 *
 * 以下のように実行した場合には
 * const it = genStep({min: 4, max: 10, step: 2});
 *
 * for(let value of it) {
 *   console.log(value); -> 4, 6, 8, 10
 * }
 *
 * の値が順番にコンソールに表示されます。
 */
function* genStep({min = 0, max = Infinity, step = 1} = {}) {
    for(let i = min; i <= max; i += step) {
        yield i;
    }
}

const it = genStep({max: 1000});

for(let value of it) {
    console.log(value)
}
```

## PubSub パターン

```js
/**
 * 問題：
 * my-library.jsに記載。
 */
function customFn1() {
    console.log('customFn1');
}
function customFn2() {
    console.log('customFn2');
}
function customFn3() {
    console.log('customFn3');
}

events.on('beforeInit', customFn1);
events.off('beforeInit', customFn1);
events.on('beforeInit', customFn2);
events.on('afterInit', customFn3);

new MyLibrary();

```

```js
/**
 * 問題：
 * MyLibraryという汎用的な使用を想定したライブラリのクラス
 * を作成したいとします。
 *
 * 第3者に使用されることを想定しているため、このクラスを
 * 初期化した際（new MyLibrary()）にコンストラクター内で
 * ユーザーが何らかの独自の関数を実行できる仕組みを設けたい
 * と思いました。
 *
 * そこで以下のように動作するeventsというオブジェクトを
 * 一つ作成する事にしました。
 *
 * どのようにしてeventsオブジェクトを実装すれば良いか
 * 考えてみてください。
 *
 * eventsオブジェクトはon、off、emitのメソッドを持っています。
 *
 ***********************************
 *
 * １．onの第一引数で実行場所、第二引数で実行したいコールバック関数を登録します。
 * events.on('beforeInit', customFn1);
 * events.on('beforeInit', customFn2);
 * events.on('afterInit', customFn3);
 *
 ***********************************
 *
 * ２．offで"on"と同じ実引数を渡すことで、onで登録した関数を解除できます。
 *
 ***********************************
 *
 * ３．emitでonで登録した関数をすべて実行します。
 * （１）のように登録されていた場合には、new MyLibrary()
 * とすると以下の内容がコンソールに表示されるものとします。
 *
 * customFn1
 * customFn2
 * library process
 * customFn3
 *
 */
const events = (function() { // 即時関数
    const eventStack = new Map(); // プライベート変数 イベントを登録する連想配列

    return {
        // publisher
        on(type, fn) { // type は トリガー  fn は実行したいコールバック関数
            const fnStack = eventStack.get(type) || new Set(); // 初回は空の連想配列を初回以降は  eventStack から取得する
            fnStack.add(fn); // 関数の登録
            eventStack.set(type, fnStack); // fnStack 自体を eventStack として登録
        },
        off(type, fn) {
            const fnStack = eventStack.get(type);
            if(fnStack && fnStack.has(fn)) {
                fnStack.delete(fn); // 登録された関数を削除
            }
        },
        // subscriber
        emit(type, _this) {
            const fnStack = eventStack.get(type);
            if(fnStack) {
                for(const fn of fnStack) {
                    fn.call(_this);
                }
            }
        }
    }
})();

class MyLibrary {
    constructor() {
        events.emit('beforeInit', this); // ユーザーが登録したイベントを実行できるようなポイント トリガーやフックと呼ばれる

        console.log('library process');

        events.emit('afterInit');
    }
    method() {
        // do something
    }
}
```

## 非同期処理

### ブラウザとJavaScript

スレッドとは連続して実行される一本の処理

```txt
イメージ
-A-B-C->
```

スレッドの種類

- Main Thread
- Service Worker
- Web Worker

メインスレッドでは JavaScript のコードの実行 と レンダリング(画面描画処理) の2つの処理を行う。

JavaScript -> レンダリング

FPS とは Frames Per Second の略で 一秒当たりの画面(フレーム)更新頻度

60fps(1秒間に60回の画面更新 = 16.7m 秒 / 更新) で人間の目には違和感なくスムーズに映像が流れる

### 同期処理と非同期処理

同期処理ではメインスレッドでコードが順番に実行される。

同期処理では1つの処理が完了するまでは次の処理には進まない

非同期処理では一時的にメインスレッドから処理が切り離されることで、後続の処理を実行したりクリックイベントを受け取ったりすることができる。
その後、切り離された処理がメインスレッドに戻ってくる

つまり、非同期処理は実行に時間がかかる重い処理の完了を待たずに他の処理を進めることができる

async await を使うのが一般的であったが、ES20 から Top level await が登場し、HTMLの呼び出し時に

```html
<script type="module" src="src/index.js"></script>
```

と書くことで Top level await が使用できる

```js
// 引数の秒数だけスレッドを占有する同期処理
function sleep(ms) {
    const startTime = new Date();
  while (new Date() - startTime < ms);
  console.log('sleep done');
}

const btn = document.querySelector('button');
btn.addEventListener('click', function(){
    console.log('button clicked');
});

// 第2引数の秒数だけスレッドを占有する 非同期処理
setTimeout(function() {
    sleep(3000)
}, 2000)
```

### タスクキューとコールスタック

タスクキュー: 実行待ちの非同期処理の行列。 -> 非同期処理の実行準を管理

キューの仕組みを「先入れ先出し」という。 -> FIFO(First In, First Out)

非同期処理の実行を確認

コールスタックに渡されているコンテキストから順番に処理される

処理待ち状態のタスクはタスクキューに積まれている

イベントループ: 簡単にいうとコールスタック監視用の無限ループ

イベントループがコールスタックにコンテキストが積まれているかというのを定期的に確認し、積まれていなければ「コールスタックが空いている状態です」とタスクキューに通知することになります。
その通知を受け取ったタスクキューがコールスタックにタスクを渡すといった仕組みになっています。

つまりタスクキューに入った順番で非同期処理が実行されます

メインスレッドが占有されている状態とはコールスタックにコンテキストが積まれている状態と同じ意味になる

### コールバック関数と非同期処理

### 非同期処理のチェーン

コールバック地獄は読むのがつらい -> プロミス構文だと読みやすい

非同期で渡された処理はコールバックが空になったタイミングで実行される

Promise構文

```js
new Promise(function(resolve, reject){
    resolve("hello"); // 処理成功時に呼ばれる
    reject("bye"); // 処理失敗時に呼ばれる

}).then(function(data) {
    console.log(data) // -> "hello"  // 処理成功時に呼ばれる

}).catch(function(data) {
    console.log(data) // -> "bye"  // 処理失敗時に呼ばれる

}).finally(function() { // finally は引数を取れない
    console.log("終了処理"); // -> "終了処理"  // 終了時に必ず実行される

});
```

```js
new Promise(
    // 同期処理
).then(
    // 非同期処理 (resolve を待つ)
).catch(
    // 非同期処理 (reject を待つ)
).finally(
    // 非同期処理 (then、またはを待つ)
);

```

```js
new Promise(function(resolve, reject){
    resolve("hello"); // resolve の引数は次の then の引数になる
}).then(function(data) {
    console(data); // -> "hello"
    return data; // then 内の return で返される値は次の then の引数になる
}).then(function(data) {
    console(data); // -> "hello"
    return data; // then 内の return で返される値は次の then の引数になる
}).then(function(data) {
    console(data); // -> "hello"
    return data; // finally に引数を渡すことはできない
}).finally(function() { // finally は引数を取れない
});

```

```js
new Promise(function(resolve, reject){
    reject("bye");
}).catch(function(data) {

});
```

### Promise

### Promiseチェーン

Promise を使って複数の非同期処理を順次実行すること

```js
function sleep(val) {
    return new Promise(function(resolve) {
    setTimeout(function() {
        console.log(val++);
        resolve(val);
    }, 1000);
    });
}

// Promise チェーンにはインスタンスを返す
sleep(0).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
})

```

### Promise と 並列処理

並列処理を使うとプログラムの待機時間を短くすることができる

```js
function sleep(val) {
    return new Promise(function(resolve) {
    setTimeout(function() {
        console.log(val++);
        resolve(val);
    }, val * 500);
    });
}

// 並列処理 には Promise.all を使う
Promise.all([sleep(2), sleep(3), sleep(4)])
.then(function(data) {
    console.log(data);
});

sleep(0).then(function(val) {
    return Promise.all([sleep(2), sleep(3), sleep(4)])
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
}).then(function(val) {
    return sleep(val);
})
```

```js
Promise.race([sleep(2), sleep(3), sleep(4)])
.then(function(data) {
    console.log(data);
})
```

```js
Promise.allSettled([sleep(2), sleep(3), sleep(4)])
.then(function(data) {
    console.log(data);
}).catch(function(e) {
    conosle.error(e);
});

// allSettled で status(状態)を確認できる fulfilled rejected
```

### Macrotasks(マクロタスク) と Microtasks(マイクロタスク)

マクロタスクとは、これまでのレクチャーでタスクキューと呼んでいたもの。

マイクロタスクとは、タスクキューとは別で存在する非同期処理の待ち行列。 -> ジョブキューと呼ばれる

1. Macrotasks から1個タスクを受け取り処理をする
2. Microtasks から全てのタスクを受け取り処理をする
3. 必要があれば描画に関連する処理を行う requestAnimationFrame レンダリング

マクロタスク(タスクキュー): スクリプト実行, setTimeout, I/O, Network, Mouse, Keyboard

マイクロタスク(ジョブキュー): Promise

```js
setTimeout(function task1() {
    console.log('task1');
});

new Promise(function promise(resolve) {
    console.log('promise');
    resolve();
}).then(function job1() {
    console.log('job1');
});

console.log('grobal end');
```

コールスタック: 処理を実行

非同期API: 非同期処理が待機

イベントループ: キューを監視

MicroTasks(ジョブキュー): 待ちジョブが待機するキュー

MacroTasks(タスクキュー): 待ちタスクが待機するキュー

コールスタックのグローバルコンテキストから setTimeout(task1) が 非同期API に呼び出されます。この setTimeout は MacroTasks(タスクキュー) に task1 を登録します。次にコールスタックに Promise という関数が呼ばれまして、Promise の .then メソッド(job1) が非同期処理として待機することになります。ここですぐさま resolve が呼ばれますので、このタイミングで MicroTasks(ジョブキュー) に job1 が登録されます。コールスタックがの処理が終了すると、イベントループが MicroTasks(ジョブキュー) にジョブがないかを確認します。ここでは job1 が見つかるので job1 がコールスタックでまず実行されます。この時点では MicroTasks(ジョブキュー) にジョブはありませんので、次に実行されるのは MacroTasks(タスクキュー) に登録されている task1 となります。MicroTasks(ジョブキュー) と MacroTasks(タスクキュー) が同じイベントのループで発見された場合は必ず MicroTasks(ジョブキュー) の方から先に実行されます。

MicroTasks(ジョブキュー): 順番が回ってきたら全てのジョブを実行

MacroTasks(タスクキュー): 順番が回ってきたら1つずつタスクを実行

実行の流れを整理するとコールスタックの処理が全て実行されたら、 MicroTasks(ジョブキュー) の処理が全てコールスタックに移された後実行される。その後、 MacroTasks(タスクキュー) の処理が1つだけコールスタックに移された後に実行される。 MacroTasks(タスクキュー) の処理が1つだけ実行された後は、再度、MicroTasks(ジョブキュー) の処理が全て実行される。その後、MacroTasks(タスクキュー) の処理が1つだけ実行され、このループを繰り返す。

代表的な関数

MacroTasks(タスクキュー): setTimeout setInterval requestAnimationFrame

MicroTasks(ジョブキュー): Promise queueMicrotask MutationObserver

```js
new Promise(function promise(resolve) {
    console.log('promise');

    setTimeout(function task1() {
        console.log('task1');
        resolve();
    });
}).then(function job1() {
    console.log('job1');
});

console.log('grobal end');

```

上記のスクリプトを実行したときのブラウザの動きを追っていきます。
まず、コールスタックにグローバルコンテキストが詰まれ promise が実行されます。
promise が実行されたことで、 promise の中で setTimeout が非同期API で呼ばれます。
setTimeout のコールバック関数の中の task1 と Promise のコールバック関数である resolve が呼ばれます。
これと併せて Promise の .thenメソッドのコールバック関数である job1 が 非同期APIで resolve の実行を待ちます。
setTimeout のコールバック関数である task1 が MacroTasks(タスクキュー) に格納されます。
そして,コールスタックの処理が掃けると, MicroTasks(ジョブキュー) に何も登録されていないことから,
MacroTasks(タスクキュー) から task1 がコールスタックに移動することになります.
この task1 は resolve を保持しているので、 この resolve が実行されたときに 非同期APIで resolve の実行を待っていた .then メソッドが呼ばれ, job1 が MicroTasks(ジョブキュー) に登録されます。
そしてコールタックの resolve と task1 が掃けると、次は job1 がコールスタックに上がってくるため、一番最後に job1 が呼ばれることになります。

```js
new Promise(function promise(resolve) {
    console.log('promise');

    setTimeout(function task1() {
        console.log('task1');
        resolve();
    });

}).then(function job1() {
    console.log('job1');
    setTimeout(function task2() {
        console.log('task2');
        resolve();

        queueMicrotask(function job4() {
            console.log('job4')
        })
    });

    queueMicrotask(function job4() { // ミクロタスクを登録するメソッド
        console.log('job4')
    })

}).then(function job2() {
    console.log('job2');
}).then(function job3() {
    console.log('job3');
});

console.log('grobal end');
```

非同期処理が含まれる上記のスクリプトを実行したときのブラウザの実行結果。

```text
promise      <- ミクロ
grobal end
task1        <- マクロタスク
job1         <- ミクロタスク
job4         <- ミクロタスク
job2         <- ミクロタスク
job3         <- ミクロタスク
task2        <- マクロタスク

```

### Await と Async

ES8 から導入された Promise を さらに直感的に記述できるようにした構文。

内部的には Promise と同じ挙動を取る

Async: Promise を返却する関数の宣言を行う。つまり。Promise オブジェクトを返す。

Await: Promise を返却する関数の非同期処理が完了するまで待つ制御を加えることができる。 Promise のインスタンスを受ける

```js
function sleep(val) {
    return new Promise(function(resolve) {
        setTimeout(function() {
            console.log(val++);
            resolve(val);
        }, 1000);
    });
}

async function init() {
    // Promise オブジェクトに await を付けることで new Promise 内の resolve に渡された引数を返すことができる.
    // await は async function 内でしか使うことができない
    let val = await sleep(0); // 0
    val = await sleep(val);   // 1
    val = await sleep(val);   // 2
    val = await sleep(val);   // 3
    val = await sleep(val);   // 4
    console.log(val)          // 5
}

// async function も Promise オブジェクトを返すので then メソッドを繋げることができる
init();

console.log(init());
```

### fetch

サーバー上から JSON 形式のデータを Promise オブジェクトとして取得することができる

JSONは JavaScript の配列やオブジェクトで表現されたデータフォーマット方式

JSON 特有のルール

- シングルクオーテーションで囲むことができず必ずダブルクオーテーションを使用する必要があります
- 必ずダブルクオーテーションで キー(プロパティ) を囲む必要があります
- 格納されている要素の中で一番最後の要素の最後にカンマを付けてはいけません

users.json

```json
[
    {
        "name": "Bob",
        "age": 23
    },
    {
        "name": "Tim",
        "age": 30
    }
    {
        "name": "Sun",
        "age": 25
    }
]
```

main.js

```js
window.fetch('users.json') // window はグローバルオブジェクトなので省略可能

fetch('users.json').then(function(response) {
    console.log(response); // Response に通信結果などの状態が格納されている
    return response.json();
    }).then(function(json) {
        console.log(json);
        for (const user of json) {
            console.log(`I'm ${user.name}, ${user.age} years old`)
        }
    })
```

```js
async function fetchUsers() {
    const response = await fetch('users.json');
    if(response.ok) {
    const json = await response.json();
    if(!json.length) {
        throw new NoDataError('no data found');
    }
    return json;
    }
};

async function init() {
    try {
        const users = await fetchUsers();
        for (const user of users) {
            console.log(`I'm ${user.name}, ${user.age} years old`);
        }
    } catch(e) {
        if (e instanceof NoDataError) {
            console.error(e);
        } else {
            console.error('Oops, something went wrong')
        }
    } finally {
        console.log('bye');
    }
    console.log('end');
}

// カスタムエラー 例外処理の中で条件分岐したい時などに使用する
class NoDataError extends Error {
    constructor(message) {
        super(message);
        this.name = 'NoDataError'
    }
}

init();
```

### 例外処理とエラー

例外処理: エラーが発生した際に飛ぶ特別な処理。

```js
try {
    throw new Error()
} catch(e) {
    // エラーハンドリング
} finally {
    // 終了処理
}

```

タイムラインの作成

friendsOf1.json

```json
{
  "friendIds": [2,3,4,5]
}
```

message1432.json

```json
{
  "userId": 2,
  "message": "waiting at station."
}
```

message1485.json

```json
{
  "userId": 4,
  "message": "how's it going?"
}
```

message1687.json

```json
{
  "userId": 5,
  "message": "drink?"
}
```

message1879.json

```json
{
  "userId": 3,
  "message": "go shopping?"
}
```

user1.json

```json
{
  "id": 1,
  "name": "Bob",
  "age": 20,
  "latestMsgId": 1735
}
```

user2.json

```json
{
  "id": 2,
  "name": "Tim",
  "age": 30,
  "latestMsgId": 1432
}
```

user3.json

```json
{
  "id": 3,
  "name": "Sun",
  "age": 25,
  "latestMsgId": 1879
}
```

user4.json

```json
{
  "id": 4,
  "name": "John",
  "age": 50,
  "latestMsgId": 1485
}
```

user5.json

```json
{
  "id": 5,
  "name": "Sun",
  "age": 10,
  "latestMsgId": 1687
}
```

user6.json

```json
{
  "id": 6,
  "name": "Emma",
  "age": 80,
  "latestMsgId": 1688
}
```

main.js

```js
/**
 * 問題：
 * myFetch関数を使って以下の「データ取得の流れ」の通り、
 * 順次JSONを取得し、取得したデータを加工して、
 * 以下のメッセージをコンソールに表示してください。
 *
 * --Bob's timeline--
 * Tim says: waiting at station.
 * Sun says: go shopping?
 * John says: how's it going?
 * Sun says: drink?
 *
 ******************************
 * データ取得の流れ
 * １．"user1.json"を取得（Bobのユーザー情報取得）
 *
 * ２．"user1.json"のidの項目を元に
 *     `friendsOf${id}.json`でフレンド一覧を取得
 *
 * ３．取得したフレンドのID（ユーザーID）を元に、
 * 　　`user${id}.json`で各ユーザーの情報を取得
 *
 * ４．各ユーザー情報のlatestMsgIdが最後に投稿した
 * 　　メッセージのIDになりますので、そのidを用いて
 * 　　`message${friend.latestMsgId}.json`
 * 　　を取得。
 *
 * １～４で取得したデータをもとにコンソールログに
 * タイムラインの文字列を作成してください。
 *
 * ※await/asyncで記述してみてください。
 */
async function myFetch(fileName) { // JSON を取ってくるための関数
    const response = await fetch(`../json/${fileName}`);
    const json = await response.json();
    return json;
}

(async function() { // 丸カッコを使って即時関数として定義
    // 1. "user1.json"を取得(Bobのユーザー情報取得)
    const me = await myFetch('user1.json');
    console.log(`--${me.name}'s timeline--`);

    // 2. "user1.json" の id の項目を元に `friendsOf${id}.json` でフレンド一覧を取得
    const friendList = await myFetch(`friendsOf${me.id}.json`);

    // 3. 取得したフレンドのID（ユーザーID）を元に、`user${id}.json`で各ユーザーの情報を取得
    const friendIds = new Set(); // Promise.all で実行するため反復可能オブジェクトを定義
    for(const id of friendList.friendIds) {
        friendIds.add(myFetch(`user${id}.json`));
    }
    const friends = await Promise.all(friendIds);

    // 4. 各ユーザー情報のlatestMsgIdが最後に投稿したメッセージの ID になりますので、そのidを用いて `message${friend.latestMsgId}.json` を取得。
    const msgIds = new Set();
    for(const friend of friends) {
        msgIds.add(myFetch(`message${friend.latestMsgId}.json`));
    }
    const msgs = await Promise.all(msgIds);

    for(const friend of friends) {
        for(const msg of msgs) {
            if(friend.id === msg.userId) {
                console.log(`${friend.name} says: ${msg.message}`);
            }
        }
    }
})();

```

## モジュラーJavaScript

ES6 で導入されたモジュールの使い方

### ES Modules と CommonJS

ソースコードを機能毎に分割して、メンテナンスしやすくする仕組み。

モジュール管理の代表的なものに ESM と CJS が存在する。

CommonJS(CJS) は Node.js 上でモジュールを管理する仕組みで require / exports という予約語を用いる

ES Modules(ESM) は ECMAScript の仕様に基づいて、ブラウザ上でモジュールを管理する仕組みで import / export という予約語を用いる

ESM vs CJS

EJS

- import/export
- Browser
- .mjs

CJS

- require/exports
- Node.js
- .cjs

### import と exxport

Import は EJS 形式でのモジュールの読み込みに使用

Exxport は EJS 形式でのモジュールの露出に使用

html

```html
<script type="module" src="moduleB.js"></script>
```

moduleA.js

```js
export let publicVal = 0;

export function publicFN() {
    console.log('publicFn called: ')
}

```

moduleB.js

```js
import { publicVal as val, publicFn as fn } from './moduleA.js';

console.log(val);
fn();
```

export default について

moduleC.js

```js
export default 0;
```

moduleD.js

```js
import defaultVal, {} from './moduleC';
console.log(defaultVal); // 0
```

### ESModules と 即時関数

ESModules と 即時関数 の動きは似ており、モジュールのインポートはモジュールにつき一回のみ実行される

即時関数

```js
const moduleA = (function () {
    console.log('IIFE called');

    let privateVal = 1;
    let publicVal = 10;

    function publicFn() {
        console.log('publicFn called: ' + privateVal++);
    }

    function privateFn() {

    }

    return {
        publicFn,
        publicVal
    }
})(); // 即時関数

moduleA.publicFn()

const moduleB = (function({ publicFn: fn, publicVal: val}) {
    publicFn();
    console.log(publicVal++);
})(moduleA);
```

ESModules

```js
console.log('ESmodule called');

let privateVal = 1;
let publicVal  = 10;

function publicFn() {
    console.log('publicFn called: ' + privateVal++);
};

function privateFn() {};
```

```js
import { publicFn as fn, public as val } from './moduleA'
```

プリミティブ型の値を外部から操作したい場合は参照を渡す必要があるため関数を通して変更する必要があります

### モジュールコンテキストとモジュールスコープ

コンテキスト

- グローバルコンテキスト -> モジュールコンテキスト(ESmodule を使った場合)
- 関数コンテキスト
- evalコンテキスト

モジュールコンテキストでは this を使用することができない

スコープ

- グローバルスコープ
- スクリプトスコープ -> モジュールスコープ(ESmodule を使った場合)
- 関数スコープ
- ブロックスコープ

モジュールコンテキストについて

A -> B : A は B に依存 (A が消えると B も消える)

index.html -> moduleB.js -> moduleA.js

moduleB.js

```js
import '/.moduleA.js'

console.log(this) // undefined

function fn() {
    console.log(this) // undefined
}
fn();

const obj = {
    fn // {fn: f}
}
obj.fn()

console.log(window)

```

moduleA.js

```js
window.d = 1;
```

モジュールスコープはこのファイル内で生成されるスコープ

### モジュールの特徴

index.html

```html
<h1>Module Lecture</h1>

<!-- モジュール機能に対応していないブラウザで実行した場合に警告を出す -->
<script>alert('このブラウザには対応していません')</script>

<!-- index.html -> main.js -->
<script src='main.js' defer></script>
<!-- defer を指定すると非同期で読込が走る。つまり、DOM の解析が終わった段階でスクリプトが読まれる -->

<!-- index.html -> moduleB.js -->
<script type="module" src='moduleB.js'></script>
<!-- type="module" を指定するとファイルが自動的に Strict モードに設定され、モジュールファイルのスクリプトが非同期で読み込まれる -->
```

```js
const h1 = document.querySelector('h1');
const text = h1.textContent;
console.log(text); // Module Lecture
```

### Strictモード

Strictモードとは、 通常の JavaScript で許容されている一部の書き方を制限する機能。

Strictモードの目的

- 意図しないバグの混入の防止
- 将来使われるかもしれない予約語の確保
- コードのセキュア化

など

Strictモードの有効化

"use strict"

ファイルの先頭、もしくは関数内の先頭行に記述する

また、モジュールファイルはデフォルトで Strictモード が有効化されている

```js
'use strict'
a = 0; // Strictモード だと let や const の記述がないためエラーになる この記述だと window.a に 0 が格納されてしまう。
const implements, interface, package // Strictモードだと予約語に登録されているというエラーが発生する

function fn() {
    'use strict'；
    b = 0;  // この記述だと window.b に 0 が格納されてしまう。
    return this; // Strictモード の関数内で this を返すと undefined を返すように設計されている
};
fn();
console.log(a);

console.log(fn.call("3")); // 3  Strictモードではプリミティブ型の値をオブジェクトでラップぜずに返す。 もし、Strictモードで無かった場合は Number {3} というオブジェクトが返る
```

### Strictモード と クラス

クラスのコンストラクタやメソッドの中はデフォルトでStrictモードが有効化されている

```js
class C {
    constructor() {
        function fn() {
            console.log(this); // undefined <- Strictモードが有効化されている
        }
        fn();
    }

    method() {
        function fn() {
            console.log(this); // undefined <- Strictモードが有効化されている
        }
        fn();
    }
}

const c = new C();
c.method();

```

### ダイナミックインポート

moduleA.js

```js
export let publicVal = 0;

export function publicFn() {
    console.log('publicFn called')
}

export default 1;
```

moduleB.js

```js
// 同期的なインポート
import { publicVal, publicFn } from './moduleA.js';
publicFn();

// 非同期的なインポート import関数は Promise Object を返す
import('./moduleA.js').then(function(modules) {
    console.log(modules);
    modules.publicFn();
});

// 非同期的なインポートを async await で書き換えた
async function fn() {
    const modules = await import('./moduleA.js');
    console.log(modules);
    module.publicFn();
}

```

モジュール化

calc.js

```js
/**
 * 問題：
 * 以下の即時関数をモジュール化してください。
 * ※calcオブジェクトはmain.js内で行い、実行してください。
 */
let val = 0; // モジュールの内部変数

export const calc = { // const calc = の部分は default に置き換えても良い
    plus: function(target) {
        const newVal = val + target;
        console.log(`${val} + ${target} = ${newVal}`);
        val = newVal;
    },
    minus: function(target) {
        const newVal = val - target;
        console.log(`${val} - ${target} = ${newVal}`);
        val = newVal;
    },
    multiply: function(target) {
        const newVal = val * target;
        console.log(`${val} x ${target} = ${newVal}`);
        val = newVal;
    },
    divide: function(target) {
        const newVal = val / target;
        console.log(`${val} / ${target} = ${newVal}`);
        val = newVal;
    },
}
```

main.js

```js
import { calc } from './calc.js'; // default で定義されている場合は波カッコはいらない
calc.plus(5);
calc.minus(3);
calc.multiply(3);
calc.divide(2);
```

## 様々なオブジェクト

### Proxy

ES6 から導入されたプロパティの操作に独自の処理を追加するためのオブジェクト

プロキシ経由で値を参照、変更することにより、独自の操作を追加することができます。

```js
const targetObj = { a: 0};
const handler = {
    // set や get や delete などの Object のプロパティに変更があった際に動くメソッドはトラップと呼ばれます。
    // target(第一引数) には Proxy の第一引数で渡したオブジェクトが渡ってきます。
    // prop(第二引数) にはプロパティにアクセスされた際のプロパティの名前が渡ってきます。
    // value(第三引数)には set から渡ってきた新しい値が渡ってきます。
    // receiver(第四引数)には Proxy Object が渡ってきます。
    set: function(target, prop, value, receiver) { // 値の追加を検知
        console.log(`[set]: ${prop}`);
        target[prop] = value; // 値を格納する
    },
    // 値を受け取らないので value は定義されていない
    get: function(target, prop, receiver) { // 値の参照を検知
        console.log(`[get]: ${prop}`);
        return target[prop]; // 値を取得する
    },
    deleteProperty: function(target, prop) {
        console.log(`[delete]: ${prop}`);
        delete target[prop]; // 値を削除する
    }
}
const pxy = new Proxy(targetObj, handler)
pxy.a = 1;     // [set]: a
pxy.a ;        // [get]: a
delete pxy.a   // [delete]: a

```

```js
// Proxy を使って値の変更を許可しないような処理を追加
set: function(target, prop, value, receiver) { // 値の追加を検知
    console.log(`[set]: ${prop}`);
    // target[prop] = value; // 値を格納する
    throw new Error('cannot add prop.')
},

// Proxy を使って値の参照時に値が無かった場合は デフォルト値を返すような処理を追加
get: function(target, prop, receiver) { // 値の参照を検知
    if(target.hasOwnProperty(prop)) {
        console.log(`[get]: ${prop}`);
        return target[prop]; // 値を取得する
    } else {
        return -1;
    }
},

```

### Reflect

JS エンジンの内部の汎用的な関数を直接呼び出すためのメソッドが格納されているオブジェクト。

| 内部メソッド | Reflect |
| - | - |
| [[Get]] | get |
| [[Set]] | set |
| [[Delete]] | deleteProperty |
| [[Construct]] | construct |

Reflect の目的

1. 内部メソッドを呼び出す関数の格納場所
2. Proxy と併せて使用するため

内部メソッドを呼び出す関数の格納場所としての使用例

```js
class C {
    constructor(a, b) {
        this.a = a;
        this.b = b;
    }
}

const obj1 = new C(1, 2); // new C(1, 2) は Reflect.construct(C, [1, 2]) と同じ
console.log(obj1);
console.log('c' in obj1); // 'c' in obj1 は Reflect.has(obj1, 'c') と同じ

Reflect.defineProperty // 失敗した際は false が返る
```

```js
const bob = {
    name: 'Bob',
    _hello: function() {
        console.log(`hello ${this.name}`);
    }
}

const tom = {
    name: 'Tom',
    _hello: function() {
        console.log(`hello ${this.name}`);
    },
    get hello() { // get トラップメソッド
        return this._hello();
    },
}

tom.hello;
Reflect.get(tom, 'hello', bob) // 第三引数の receiver に登録されたオブジェクト(ここでは bob )が get メソッド内の this に束縛される. つまり第三引数は bind と同じ意味がある. 第三引数が省略された場合は、第一引数が第三引数のオブジェクトとして使用される

```

### ReflectとProxy

Reflect と Proxy を内部で同時に使用する方法

内部メソッドとの関係性

| 内部メソッド | Reflect | Proxy |
| - | - | - |
| [[Get]] | get | get |
| [[Set]] | set | set |
| [[Delete]] | deleteProperty | deleteProperty |
| [[Construct]] | construct | construct |

```js
const targetObj = {
    a: 0,
    get value() {
        return this.b;
    }
};

const handler = {
    get: function(target, prop, receiver) {
        console.log(`[get]: ${prop}`);
        if(target.hasOwnProperty(prop)) {
            return target[prop]; // return Reflect.get(target, prop, receiver);
        } else {
            return -1;
        }
    }
}

const pxy = new Proxy(targetObj, handler);
console.log(pxy.value); // getter 経由なのでログが出力される
console.log(targetObj.b);
console.log(pxy.b);
```

### WeakMap

WeakMap とは 弱い参照でオブジェクトを保持するコレクション

キーは必ずオブジェクト

```js
const wm = new WeakMap();

let o = {};
wm.set(o, 'value1');

o = null;

o = {}; // 上の方で let o = {} と定義した o とはメモリ番地の異なる別物 なぜなら一度 null が代入された段階で WeakMap においては GC(ガベージコレクション) されるから

console.log(wm.get(o));

console.log(wm.delete(o));
console.log(wm.get(o));

```

### WeakMap と プライベート変数

```js
export class Person {
    constructor(name) {
        this._name = name; // プライベート変数
        wm.set(this, {
            name
        });
    }

    hello() {
        console.log(`hello ${wm.get(this).name}`);
    }
}
```

```js
import { Person } from './Person.js';

const tim = new Person('Tim');
const bob = new Person('Bob');
tim.hello();
bob.hello();

```

### JSON

JSON はあくまで文字列

`JSON.parse`

JSON -> Object

`JSON.stringify`

Object -> JSON

```js
const obj = {a: 0, b: 1, c: 2};

function replacer(prop, value) {
    if(value < 1) {
        return;
    }
    return value;
}

const json = JSON.stringify(obj, ["a", "b"]); // オブジェクトから文字列に変換する
console.log(typeof json);

const obj2 = JSON.parse(json);
console.log(obj2);
```

### Strage

Strage ブラウザの保存領域にデータを格納するためのオブジェクト -> localStrage

開発者ツールの Local Strage で保存された値が確認できる

もし使用できるメソッドを忘れてしまった時は `localStrage.__proto__` 内を参照すれば使用可能なメソッドやプロパティを参照できる

```js
localStrage.setItem('key', 'value'); // 辞書のような形式でブラウザの保存領域に値を格納することができる

const result = localStrage.getItem('key'); // 格納した値を取得することができる
console.log(result);
```

JSON と localStrage 組み合わせた例

```js
const obj = {a: 0};
const json = JSON.stringify(obj);
localStrage.setItem('key', json);
const result = localStrage.getItem('key');
console.log(result);
console.log('end');
```

### Array

...arry を使うと配列を展開できる 配列の連結なんかに多用される

配列の要素のそれぞれに対して操作をしたい時は forEach メソッド を使用する

forEach

```js
const arry = [1,2,3,4,5];

// 全ての配列の要素を出力
arry.forEach(function(v, i, arry) {
    console.log(v);
})
```

配列の要素の値を元にして新しい配列を作成したいときは Map メソッドを使用します

map

```js
const arry = [1,2,3,4,5];

// 全ての配列の要素に対して2倍
const newArry = arry.map(function(v, i, arry) {
    console.log(v);
    return v * 2;
})

console.log(newArry);
```

filter

```js
const arry = [1,2,3,4,5];

// 1 以上の値を持つ配列の要素のみを返す
const filterArry = arry.filter(function(v, i, arry) {
    return i >= 1;
})
```

reduce は配列の要素1つ1つに対し処理を行い最終的に1つの値にまとめる役割を持つ関数

第一引数にコールバック関数、第二引数に初期値を取ります。

コールバック関数の第一引数 accu に対し前のループの戻り値が引数として渡ります ループの最後に返る値が関数の戻り値になります。

また、コールバック関数の台に引数 curr に対し配列の要素が1つずつ渡ります。

reduce

```js
const arry = [1,2,3,4,5];

const result = arry.reduce(function(accu, curr) {
    console.log(accu, curr)
    return accu + curr;
}, 0);

console.log(result);
```

JavaScript を関数型で書きたい場合は forEach map filter reduce の4つの配列操作関数の書き方を押さえる必要がある
