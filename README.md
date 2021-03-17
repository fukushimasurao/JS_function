# JS_function

関数について。
内容は、https://jsprimer.net/basic/function-declaration/に準拠します。

## プログラムの処理の順番 - sync
プログラミングは基本は上から処理されます。

で、上から実行される処理のことを同期処理と呼びます。

英語だと、`sync`

カタカナで、シンクロとか、シンクロニティとか、書くあれです。

「同じ期」って書くけど、とにかく上から順番に、書いた順番に実行される感じです。

コードの例は↓

※blockTime関数というのがありますが、細かくわかる必要ないです。

blockTimeの役割は、１秒間待機（処理を止める）ものと思って下さい。

```javascript
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数
function blockTime(timeout) {
    const startTime = Date.now();
    // `timeout`ミリ秒経過するまで無限ループをする
    while (true) {
        const diffTime = Date.now() - startTime;
        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}
console.log("処理を開始");
blockTime(1000); // 他の処理を1000ミリ秒（1秒間）ブロックする
console.log("この行が呼ばれるまで処理が1秒間ブロックされる");
```

## プログラムの処理の順番 - async
ただし、上から順番ではなく意図した順番に処理させる方法もあります。

同期じゃない方法 = 上からの処理じゃない方法 = syncじゃない処理方法。

これが、**非同期処理**というものです。

英語だと`async`

先に以下コードを参照。

※ちなみに、、英語の接頭語`a`には否定(not)の意味がある。

左右非対称を左右非対称をアシンメトリ（`asymmetry`）というけれど、

これは、左右対称のシンメトリー(`symmetry`)否定のaをくっつけて非対称って意味にしている。

```javascript
// ーーーーーーーーーー処理その１
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数を、blockTimeに代入
function blockTime(timeout) {
    console.log('ブロック中');
    const startTime = Date.now();
    while (true) {
        const diffTime = Date.now() - startTime;

        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}


// ーーーーーーーーーー処理その２
// 上で用意したblockTime関数に引数1000ミリ秒を与えた関数
function doBlockTime() {
    console.log("3. ブロックする処理を開始します");
    blockTime(1000); // 他の処理を1秒間ブロックする
    console.log("4. ブロックする処理が完了しました");
}


// ーーーーーーーーーー処理その３
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
setTimeout(doBlockTime, 10);
// ブロックする処理は非同期なタイミングで呼び出されるので、次の行が先に実行される
console.log("2. 非同期的な処理を実行します");

```
コードの中身はざっくりと３ブロックの構成。
 - blockTime関数：引数に数字（ミリ秒）を与えると、その時間ブロック
 - doBlockTime関数：blockTime(1000);を実行する関数
 - setTimeout(doBlockTime, 10);により、doBlockTime関数が10ミリ秒後に実行される。

 もしこれが上から順番に処理される場合、
 
 処理の順番のイメージは以下のとおりです。
```
0. 第１，第２ブロックは、関数の宣言だけで実行はしていないので、第３ブロックへ移動
1. console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");が表示
2. setTimeout(doBlockTime, 10);が実行される。
→ doBlockTimeが10ミリ秒後に実行される。
→ console.log("3. ブロックする処理を開始します");と表示
3. doBlockTime関数の中のblockTime(1000);により１秒ときが止まる
4. console.log("4. ブロックする処理が完了しました");が表示
5. console.log("2. 同期的な処理を実行します");と表示
```

けど実際は、以下の順番で表示されます。

（実際にブラウザ等で実行して見よう！！）

1. setTimeoutのコールバック関数を10ミリ秒後に実行します
2. 同期的な処理を実行します
3. ブロックする処理を開始します
4. ブロックする処理が完了しました

これは、実際に上から順番に実行(同期処理 = sync)されるのではなく、

どこかで順番が入れ替わっている(非同期 = async)処理になっているということです。

その場所は、

`setTimeout(doBlockTime, 10);`

このsetTimeoutの特徴は、
 - 第１引数に関数を持つ
 - 第１引数の関数を`第２引数ミリ秒`後に実行する
という、非同期処理( =`第２引数ミリ秒`立たないと実行されない )をさせるものです。

だから、単純に上から処理が実行されるのではなくこのsetTimeoutによって時空が歪められているのだ!

ザ・ワールド！！！！


## プログラムの処理の順番 - callbackと高階関数
さて今回出てきた
`setTimeout(doBlockTime, 10);`
ですが、これらには名前がついている。

- 引数に関数を持つ関数 = 高階関数
- 引数に入れられる（引数として利用される）関数 = callback関数

今回で言えば、
- 高階関数 = setTimeout
- callback関数 = doBlockTime
です。

このコールバック関数はよく出てくる単語であるし、

「コールバック」という言葉から[後で呼ばれる]という意味合いを受けると思いますが、

要は「関数の引数に入れられる関数」のことです。

## ES2015と合体。
さて、ちょっと別の話をします。

JavaScriptはいつからか

「今の書き方めんどくね？？？？」と言われるようになりました。

だからEcma Internationalという偉い団体が

「ちょっと今までの書き方に加えて、もっと簡単でラクな書き方を追加するわ」と叫びました。

その追加された一つがが、アロー関数です。

### 今までの書き方(今も使えるよ！)
```javascript
function double(num) {
    return num * 2;
}
// `double`関数の返り値は、`num`に`10`を入れて`return`文で返した値
console.log(double(10)); // => 20
```

### 進化１ 
偉い団体「関数は、変数に代入できるようにしようぜ！ソッチのほうがいろいろ便利っしょ！」
```javascript

const makeDouble = function double(num) {
    return num * 2;
}
// `double`関数の返り値は、`num`に`10`を入れて`return`文で返した値
console.log(makeDouble(10)); // => 20
```

偉い団体「そもそもfunctionて書くの糞めんどいやんけ。記号にしようぜｗ　矢印とかカッケーっしょｗｗ」
### 新しい書き方
```javascript
const makeDouble2 = (num) => {
    return num * 2;
}
// `double`関数の返り値は、`num`に`10`を入れて`return`文で返した値
console.log(makeDouble2(20)); // => 20
```

この様に、
 - functionという文字を消して、
 - その代わり矢印(アロー / Arrow)にする
という関数をアロー関数(Arrow Function)と呼ぶようにしました。

# 非同期処理を今どき風に書く。
さて、上記の通りJSの関数は、
 - 変数に代入することが出来る。
 - functionを消して矢印で書くことが出来る
様になりました。

これが出来ると、JS業界では「短く書けるからうれしー」となったわけです。

と同時に「短く書けるんだから、短く書けるものは短くかけや！！！」と「短く書けよ自警団」が現れてきました。

短く書かないと、この自警団によってボコボコにされます。

（※ただし昨今のJS業界ではこの自警団の考えが普通になってきました。）

ということで、上記したsetTimeoutをアロー関数で表してみます。

まずは、もともとのコードを再掲。

```javascript
// ーーーーーーーーーー処理その１
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数を、blockTimeに代入
function blockTime(timeout) {
    console.log('ブロック中');
    const startTime = Date.now();
    while (true) {
        const diffTime = Date.now() - startTime;

        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}

// ーーーーーーーーーー処理その２
// 上で用意したblockTime関数に引数1000ミリ秒を与えた関数
function doBlockTime() {
    console.log("3. ブロックする処理を開始します");
    blockTime(1000); // 他の処理を1秒間ブロックする
    console.log("4. ブロックする処理が完了しました");
}


// ーーーーーーーーーー処理その３
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
setTimeout(doBlockTime, 10);
// ブロックする処理は非同期なタイミングで呼び出されるので、次の行が先に実行される
console.log("2. 非同期的な処理を実行します");
```

## アロー関数にする。
```javascript
// ーーーーーーーーーー処理その１
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数を、blockTimeに代入
const blockTime = (timeout) => {
    console.log('ブロック中');
    const startTime = Date.now();
    while (true) {
        const diffTime = Date.now() - startTime;

        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}

// ーーーーーーーーーー処理その２
// 上で用意したblockTime関数に引数1000ミリ秒を与えた関数
const doBlockTime = () => {
    console.log("3. ブロックする処理を開始します");
    blockTime(1000); // 他の処理を1秒間ブロックする
    console.log("4. ブロックする処理が完了しました");
}


// ーーーーーーーーーー処理その３
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
setTimeout(doBlockTime, 10);
// ブロックする処理は非同期なタイミングで呼び出されるので、次の行が先に実行される
console.log("2. 非同期的な処理を実行します");
```

このままでも処理されるのですが、ここで新たに

「無駄な代入するな警察」が現れます。

例えば、`const doBlockTime`って関数を代入している変数あるけど、これ変数に入れる必要ある？？っという主張です。

確かに、この関数をいろんなところでたくさん利用するならば変数に代入して使いまくると良さそうですが今回はそれがありません。

よって、代入せずにsetTimeout関数の引数にそのまま利用します。
```javascript
// ーーーーーーーーーー処理その１
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数を、blockTimeに代入
const blockTime = (timeout) => {
    console.log('ブロック中');
    const startTime = Date.now();
    while (true) {
        const diffTime = Date.now() - startTime;

        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}

// ーーーーーーーーーー処理その2
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
setTimeout(doBlockTime = () => {
    console.log("3. ブロックする処理を開始します");
    blockTime(1000); // 他の処理を1秒間ブロックする
    console.log("4. ブロックする処理が完了しました");
}, 10);
// ブロックする処理は非同期なタイミングで呼び出されるので、次の行が先に実行される
console.log("2. 非同期的な処理を実行します");
```
更に、setTimeoutの引数doBlockTimeは名前をつける必要もないので消します。

※通常、関数は宣言→それを利用するという流れなのですが、もはや速攻で利用するから

名前もつける必要なくね？となります。

これを即時関数と呼びます。（本当はスコープの話とかあるんですが、余裕があれば自分で調べて下さい）

```javascript
// ーーーーーーーーーー処理その１
// 指定した`timeout`ミリ秒経過するまで同期的にブロックする関数を、blockTimeに代入
const blockTime3 = (timeout) => {
    console.log('ブロック中');
    const startTime = Date.now();
    while (true) {
        const diffTime = Date.now() - startTime;
        if (diffTime >= timeout) {
            return; // 指定時間経過したら関数の実行を終了
        }
    }
}

// ーーーーーーーーーー処理その2
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
// ↓doBlockTimeを消した。
setTimeout(() => {
    console.log("3. ブロックする処理を開始します");
    blockTime3(1000); // 他の処理を1秒間ブロックする
    console.log("4. ブロックする処理が完了しました");
}, 10);
// ブロックする処理は非同期なタイミングで呼び出されるので、次の行が先に実行される
console.log("2. 非同期的な処理を実行します");
```

**更に省略して以下の様にもかけたりするのですが、よくわからないので、これはやりすぎ。**

```javascript
console.log("1. setTimeoutのコールバック関数を10ミリ秒後に実行します");
setTimeout(() => {
    console.log("3. ブロックする処理を開始します");
    (function(timeout){
        console.log('ブロック中');
        const startTime = Date.now();
        while (true) {
            const diffTime = Date.now() - startTime;
            if (diffTime >= timeout) {
                return; // 指定時間経過したら関数の実行を終了
            }
        }
    }(1000))
    console.log("4. ブロックする処理が完了しました");
}, 10);

```
