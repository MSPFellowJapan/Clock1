ステップ１　時計を作ろう！
==================
このステップでは簡単な時計アプリケーション作成をとおして以下の項目を学習します．

 * Javascriptでのアプリの作成方法
 * WinJS.UI.ViewBox

##プロジェクトの作成
------------------
ファイル->新規作成->プロジェクトを選択してプロジェクトを作成します．

![new_project](https://dl.dropboxusercontent.com/u/59753988/StoreappSampleThumb/new_project.png)

固定レイアウトアプリケーションでプロジェクト名をClock１とし，作成します．

##構造の作成
------------------
ソリューションエクスプローラからdefault.htmlを開きます．

```html:default.html
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
    <meta charset="utf-8" />
    <title>Clock1</title>

    <!-- WinJS 参照 -->
    <link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet" />
    <script src="//Microsoft.WinJS.1.0/js/base.js"></script>
    <script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

    <!-- Clock1 参照 -->
    <link href="/css/default.css" rel="stylesheet" />
    <script src="/js/default.js"></script>
</head>
<body>
    <div data-win-control="WinJS.UI.ViewBox">
        <div class="fixedlayout">
            <p>コンテンツをここに挿入</p>
        </div>
    </div>
</body>
</html>
```

`<p>コンテンツをここに挿入</p>`を以下のように書き換えます

```html:
<p><span id="hours">12</span>:<span id="minutes">00</span>:<span id="seconds">00</span></p>
```

次にソリューションエクスプローラ->css->default.cssを開き，次のように書き足します

```css:default.css
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
}

body {
    -ms-flex-align: center;
    -ms-flex-direction: column;
    -ms-flex-pack: center;
    display: -ms-flexbox;
}

.fixedlayout {
    -ms-grid-columns: 1fr;
    -ms-grid-rows: 1fr;
    display: -ms-grid;
    height: 768px;
    width: 1024px;
}

.fixedlayout {
    -ms-grid-columns: 1fr;
    -ms-grid-rows: 1fr;
    /*display: -ms-grid;*//*削除*/
    height: 768px;
    width: 1024px;
    /* ----以下追加コード---- */
	display: -ms-flexbox;
	-ms-flex-align:center;
	-ms-flex-pack:center;
    font-size: 200px;
	/* ----以上追加コード---- */
}
@media screen and (-ms-view-state: fullscreen-landscape) {
}

@media screen and (-ms-view-state: filled) {
}

@media screen and (-ms-view-state: snapped) {
}

@media screen and (-ms-view-state: fullscreen-portrait) {
}
```

実行すると以下のように中央に大きく表示されます．

![実行画面](https://dl.dropboxusercontent.com/u/59753988/StoreappSampleThumb/1.png)

これで見た目が完成しました．

##ロジックの作成
--------------------------------
ソリューションエクスプローラからjs->default.jsを開き，以下のようにコードを追加します．

```js:default.js
// 固定レイアウト テンプレートの概要については、次のドキュメントを参照してください:
// http://go.microsoft.com/fwlink/?LinkId=232508
(function () {
    "use strict";

    WinJS.Binding.optimizeBindingReferences = true;

    var app = WinJS.Application;
    var activation = Windows.ApplicationModel.Activation;

    /*----追加コード----*/
    function update() {
        var current, h, m, s;
        current = new Date();
        h = current.getHours();
        m = current.getMinutes();
        s = current.getSeconds();
        hours.innerText = h;
        minutes.innerText = ((m < 10)? "0" : "" ) + m;
        seconds.innerText = ((s < 10) ? "0" : "") + s;
        setTimeout(update, 1000);
    }
    /*----追加コード----*/
    app.onactivated = function (args) {
        if (args.detail.kind === activation.ActivationKind.launch) {
            if (args.detail.previousExecutionState !== activation.ApplicationExecutionState.terminated) {
                // TODO: このアプリケーションは新しく起動しました。ここでアプリケーションを
                // 初期化します。
            } else {
                // TODO: このアプリケーションは中断状態から再度アクティブ化されました。
                // ここでアプリケーションの状態を復元します。
            }
            /*----追加コード----*/
            update();
            /*----追加コード----*/
            args.setPromise(WinJS.UI.processAll());
        }
    };

    app.oncheckpoint = function (args) {
        // TODO: このアプリケーションは中断しようとしています。ここで中断中に
        // 維持する必要のある状態を保存します。中断中に自動的に保存され、
        // 復元される WinJS.Application.sessionState オブジェクトを使用
        // できます。アプリケーションを中断する前に非同期操作を完了する
        // 必要がある場合は、args.setPromise() を呼び出して
        // args.setPromise().
    };

    app.start();
})();
```
追加コードを追加したら実行してみましょう．時計が1秒ごとに動作するはずです．

今回はWinJSのライブラリをほとんど使わす，Webアプリのようにストアアプリを作成しました.Webアプリを作る感覚でストアアプリを作ることができましたね．この要領で自作のWebアプリがあったらどんどんストアアプリ化してみましょう．

##解説
----------------------------
それではコードの解説をします．まず，アプリケーションの骨格となるHTMLファイルから解説します．

```
<p><span id="hours">12</span>:<span id="minutes">00</span>:<span id="seconds">00</span></p>
```

時間：分：秒のようにコロンでくぎって表示する仕様にしたので`p`タグの中に`span`タグで時,分,秒の各コンテナを作成し，その間にコロンを挟みます．

次にこの構造をCSSで見た目を整えます．

```
display: -ms-flexbox;
-ms-flex-align:center;
-ms-flex-pack:center;
font-size: 200px;
```

これは先ほどの`p`タグを内包している`div.fixedlayout`のパラメータを設定しています．
`display: -ms-flexbox`,`-ms-flex-align:center`,`-ms-flex-pack:center`によって，画面中央に配置されます．`font-size`で文字を多くします．
[-ms-flexbox](http://msdn.microsoft.com/ja-jp/library/ie/hh673531(v=vs.85).aspx)


さて，ストアアプリが実行される画面サイズは各デバイスで異なります．これを緩和するために`WinJS.UI.ViewBox`を使います．HTMLの`body`直下を見てみましょう．

```
<div data-win-control="WinJS.UI.ViewBox">
    ...
</div>
```

`data-win-control="WinJS.UI.ViewBox"`が`div`タグの属性に設定されています．
WinJSのライブラリをHTML要素に実装する場合`data-win-control`属性を使用します．
`WinJS.UI.ViewBox`は内容物の要素，ここでは`div.fixedlayout`の`width`，`height`を保ったまま, `WinJS.UI.ViewBox`を保持しているコンテナ，ここでは`body`の`width`，`height`まで拡大し，中央に揃えます．

これで中央にどんな画面サイズでも比率の固定された時計を表示できます．

次に，時計の動作を見ていきます．

```
function update() {
    var current, h, m, s;
    current = new Date();
    h = current.getHours();
    m = current.getMinutes();
    s = current.getSeconds();
    hours.innerText = h;
    minutes.innerText = ((m < 10)? "0" : "" )　+ m;
    seconds.innerText = ((s < 10) ? "0" : "") + s;
    setTimeout(update, 1000);
}
```
`current`に現在の`Date`を取得します．取得した`Date`オブジェクトから時間，分，秒を取得します．
取得した時間をhoursに設定します．WinJSによるストアアプリではHTMLの`id`に設定するとそのDOMオブジェクトが自動生成されます．`hours`はHTMLの`div#hours`のDOMオブジェクトです

同様に分，秒を設定します．ここでは必ず2桁になるように調整しています．

最後に`update`を1000ミリ秒後に起動します．

この`update`を次の場所で呼び出します．

```
app.onactivated = function (args) {
    if (args.detail.kind === activation.ActivationKind.launch) {
        if (args.detail.previousExecutionState !== activation.ApplicationExecutionState.terminated) {
            // TODO: このアプリケーションは新しく起動しました。ここでアプリケーションを
            // 初期化します。
        } else {
            // TODO: このアプリケーションは中断状態から再度アクティブ化されました。
            // ここでアプリケーションの状態を復元します。
        }
        /*----追加コード----*/
        update();  //ここで呼び出す
        /*----追加コード----*/
        args.setPromise(WinJS.UI.processAll());
    }
};
```
`update`を呼ぶと1000ミリ秒後に`update`を呼びだし，さらに，1000ミリ秒後に`update`を呼び出し...と永久に実行されます．

これで最大遅延1秒弱の時計が出来上がりました！














