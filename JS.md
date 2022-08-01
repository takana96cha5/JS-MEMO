# Java Script メモ

変数定義

```js
const one = 1;
let a = 'a';
```

関数定義

```js
function hello(name) {
    return 'Hello' + name;
};

hello('ken');
```

無名関数

```js
const hello = function(name) {
    return 'Hello' + name;
};
```

メソッドとプロパティ

```js
let hello = 'hello world';
console.log(hello.toUpperCase());

// 便利な文字列操作メソッドに slice split などもあり

```

配列

```js
const array1 = new Array(1, 2, 3, 4);
const array2 = [1, 2, 3, 4];

array1.push(5);
const item = array1.pop(5);
array1.unshift(0);
const val = array1.shift();
console.log(array1.length);

```

オブジェクト

```js
const person = {
    // プロパティ
    age: 20,

    // メソッド
    getAge: function() {
        console.log(this.age);
    },

};
person['name'] = 'taro yamada';
preson.getAge();

```

ループ処理

```js
const array = [1, 2, 3, 4, 5]

for(let i = 0; i < 5; i = i + 1) {
    console.log(i);
};

for(let i = 0; i < array.legth; i++) {
    console.log(i);
};

for(let i in array) {
    console.log(i);
};

for(let i in array) {
    console.log(array[i]);
};
for(let val of array) {
    console.log(val);
};

```

ループ処理(発展)

```js
const todos = [
    {
        id: 1,
        title: 'Go to school',
        completed: true,
    },
    {
        id: 2,
        title: 'Go shopping',
        completed: true,
    },
    {
        id: 3,
        title: 'Go to kitchen',
        completed: true,
    },
    {
        id: 4,
        title: 'Go to bed',
        completed: true,
    },
]

for(let i = 0; i < todos.length; i++>) {
    let todo = todos[i].title;
    if(todo.completed === true) {
        console.log(i, todo.title);
    };
};

for(let todo of todos) {
    if(todo.completed === true) {
        console.log(todo.title);
    };
};

```

比較演算子

```js
const num = 0;

if (1 === "1" && 0 === "0") {
    // 処理
} else if(1 === true || 0 === false || !num) {
    // 処理
} else {
    // 処理
};
```

アロー関数の定義

```js
function hello(name) {
    console.log('hello' + name);
};

const hello = function(name) {
    console.log('hello' + name);
};

// アロー関数
const hello = name => console.log('hello' + name);

// アロー関数を使ったfor文
const array = [1, 2, 3, 4, 5];
array.forEach(value => console.log(value));

```

コールバック関数

```js
// 引数に渡す関数をコールバック関数という 今回はgetNameがコールバック関数
function hello(callback) {
    console.log('hello' + callback());
};

function getName() {
    return 'taro'
};

hello(getName);

// ---------------------------------
// 無名関数 無名関数はよくコールバック関数として使われる
const hello = (callback) => console.log('hello' + callback());

const getName = () => 'taro';

hello(getName);

// ---------------------------------
const doSomething = (a, b, callback) => {
    const result = callback(a, b);
    console.log(result);
};

const add = (a, b) => a + b;
const multiply = (a, b) => a * b;

doSomething(2, 2, add);
doSomething(2, 2, multiply);
```

コールバック関数とループ

```js

const array = [1, 2, 3, 4, 5];

// 配列にコールバック関数を適用する高階関数
function forEach(array, callback) {
    for(let i = 0; i < array.length; i++) {
        callback(array[i]);
    };
};

// ログを出力する関数
function log(val) {
    console.log(val);
};

// 引数を２倍して返す関数
function double(val) {
    val = val * 2;
    log(val);
};

// 関数呼び出し
forEach(array, double);

```

forEach メソッド
配列内の全ての要素に処理を順番に適用する

```js
const array = [1, 2, 3, 4, 5];
array.forEach(function(val, i, array) {
    console.log(val, i, array);
});

array.forEach((val, i, array) => console.log(val, i, array));
```

reduce メソッド
配列の畳み込み処理
| 引数 | 必須or任意 | 役割 |
| ---- | ---- | ---- |
| アキュムレータ | 必須 | コールバック関数の計算結果(前のループの戻り値)が格納される。コールバック関数の初回呼び出し時は、reduce()メソッドの第二引数である初期値が格納される。 |
| 現在の値  |  必須  |  添え字の若い順に配列の要素が1つずつ格納される。 |
| 添字 | 任意 | 第二引数の値に対応する配列の添字が格納される。 |
| 配列 | 任意 | 配列がそのまま格納される。 |

```js
const array = [1, 2, 3, 4, 5];
array.reduce(function(accu, curr) {
    console.log(accu, curr);
    return accu * curr;
}, 10);

const str = 'animation';
const strArray = str.split('');

const result = strArray.reduce((accu, curr) => {
    return `${accu}<${curr}>`;
}, '')

console.log(result);

```

reduce コールバック関数 バージョン

```js
const str = 'animation';
const strArray = str.split('');

function tag(accu, curr) {
    console.log(accu, curr);
    return `${accu}<${curr}>`;
}

function reduce(array, callback, defaultValue) {
    let accu = defaultValue;
    for(let i = 0; i < array.length; i++) { // for of で書き換えられる
        let curr = array[i];
        accu = callback(accu, curr);
    }

    return accu;
};

const result = strArray.reduce(tag, "");
console.log(result);

```

DOM 操作

DOM とは HTML を JavaScript から簡単に操作できるようにしたインターフェースのこと

document に HTML の情報が格納されている

```HTML
<html lang="en">
<head>...</head>
<body>
    <h1 id="main-title">JavaScript基礎</h1>
    <h2 class="sub-title">DOM(Document Object Model)とは?</h2>
    <ul>
        <li class="item item1">HTMLを<b>JavaScript</b>から操作できるようにしたインターフェース。</li>
        <li class="item item2">JavaScriptからは <b>DOM API を通じて HTML の情報を取得・変更します。</b></li>
        <li class="item item3"><b>情報の取得・変更・イベントの登録</b>などができます。</li>
    </ul>
    <script src="manin.js"></script>
</body>
```

DOM の取得

```js
// getElementById は非推奨
document.querySelector('#main-title'); // id の取得
document.querySelector('.sub-title');  // class の取得
document.querySelector('h1');          // tag の取得
document.querySelector('.item');    // 一番初めの item クラスの値を取得
document.querySelectorAll('.item');    // 全ての item クラスの値を取得
document.querySelectorAll('.ul > li');    // ul タグ内の li クラスの値を取得

```

DOM 要素の更新

```js
// DOM の書き換え
const h1 = document.querySelector('#main-title');
h1.innerHTML = "書き換え完了";
h1.innerHTML = "書き換え完了 <span style="color: blue">もっと書き換える</span>"; // HTML 要素を渡せる
h1.textContent; // HTML 要素はエスケープされる
h1.style.color = 'yellow'; // style を書き換える
h1.style.backgroundcolor = 'gray'; // style を書き換える

// class 情報の更新
h1 = document.querySelector('#main-title');
h1.classList.add('underline'); // クラスリストの要素を追加
h1.classList.remove('underline'); // クラスリストの要素を削除
h1.classList.toggle('underline'); // クラスリストの要素をトグル
h1.classList.toggle('underline'); // クラスリストの要素をトグル
h1.classList.toggle('underline'); // クラスリストの要素をトグル

// 要素を絞った検索
const ul = document.querySelector('.ul');    // ul クラスの値を取得
ul.style.color = 'red';                      // ulの子要素を赤色に変更
const firstlList = ul.querySelector('li');   // ul クラス内の一番初めの li の値を取得
firstList.style.color = 'black';             // ul 内の一番最初の li だけ黒色に変更(その他のリストは赤色)

// 複数要素の更新
const li = document.querySelectorAll(li);
li.forEach(node => node.style.color = 'purple');

```

DOM にイベントを追加する

```HTML
...
<body>
    <h1 id="main-title">JavaScript基礎</h1>
    <h2 class="sub-title">DOM(Document Object Model)にイベントリスナーを登録してみよう。</h2>
    <img src="https://farm1.staticflicker.com/118/" />
    <button id="btn">Change Title Color</button>
    <script src="manin.js"></script>
</body>
...
```

ボタンクリック -> アラート

```js
const btn = document.querySelector('#btn'); // ボタン要素の取得

// changeColor 関数を定義
const changeColor = () => {
    this.style.color = "red";
    console.log(this);
};

// changeBgColor 関数を定義
const changeBgColor = () => {
    this.style.BgColor = "blue";
};

btn.addEventListener('click', changeColor); // クリックされたら changeColor 関数を実行するイベントを登録
btn.addEventListener('click', changeBgColor); // クリックされたら changeColor 関数を実行するイベントを登録
btn.removeEventListener('click', changeColor); // クリックされたら changeColor 関数を実行するイベントを解除

// 非推奨ですがもしもの時のために覚えておきたいイベントハンドラによるイベント登録 注)イベントハンドラ経由だとイベントは1つしか登録できない。
btn.onclick = changeColor;
btn.onclick = changeBgColor;

```

DOMContentLoaded vs load

```HTML
...
<body>
    <h1 id="main-title">JavaScript基礎</h1>
    <h2 class="sub-title">DOMContentLoaded vs load</h2>
    <img src="https://images.unsplash.com/photo-xxxxxxxxxxxxxx" />
    <img src="https://images.unsplash.com/photo-xxxxxxxxxxxxxx" />
    <button id="btn">Change Title Color</button>
    <script src="manin.js"></script>
</body>
...
```

DOMContentLoaded: ブラウザが HTML を解釈し終わりDOMツリーが作成されたタイミングで発火するイベント

load: DOMツリー以外にも画像や動画など、全てのコンテンツをロードし終わったタイミングで発火するイベント

UX の観点から load より DOMContentLoaded の方が好ましい

```js
const dcl  = document.querySelector('.dcl');
const load = document.querySelector('.load');

document.addEventListner('DOMContentLoaded', function () {
    dcl.classList.add('done');
});

// loadイベント は document に登録できないので window に登録する
window.addEventListner('load', function () {
    load.classList.add('done');
});

```

ANIMATION の文字を分割しよう

```html
<body>
    <div id="container">
        <div class="animate-title inview">
            ANIMATION
        </div>
    ...
    </div>
    ...
</body>
```

```js
document.addEventListner('DOMContentLoaded', function () {
    const el = document.querySelecter('.animate-title'); // ANIMATION の文字を取得
    const str = el.innerHTML.trim(); // 前後のスペースを削除

    const concatStr = ''; // 分割された文字を格納するための文字列
    for(let c of str) {
        c = c.replace(' ', '&nbsp;') // 半角スペースを &nbsp; に変換
        // c = c.replace(/\s+/, '&nbsp;') // 正規表現バージョン: 一文字以上の半角スペース
        concatStr += `<span class="char">${c}</span>` // 文字を spanタグでラップ
    };
    el.innerHTML = concatStr;
});


// 上記のコードを reduce で書き換える----------------------------------
document.addEventListner('DOMContentLoaded', function () {
    const el = document.querySelecter('.animate-title'); // ANIMATION の文字を取得
    const chars = el.innerHTML.trim().split(''); // 前後のスペースを削除し、配列に変換

    el.innerHTML = chars.reduce((acc, curr) => {
        curr = curr.replace(' ', '&nbsp;') // 半角スペースを &nbsp; に変換
        return `${acc}<span class="char">${curr}</span>`
    }, "");
});


// 上記のコードを class で書き換える-------------------------------
class TextAnimation {
    constructor(el) {
        this.el = document.querySelector(el);
        this.chars = this.el.innerHTML.trim().split("");
        this.el.innerHTML = this._splitText();
    }
    _splitText() {
        return this.chars.reduce((acc, curr) => {
            curr = curr.replace(' ', '&nbsp;');
            return `${acc}<span class="char">${curr}</span>`
        }, "");
    }
}

document.addEventListner('DOMContentLoaded', function () {
    const ta = new TextAnimation('.animate-title');
    const ta2 = new TextAnimation('.animate-title2');
};

```
