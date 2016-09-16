<!--
#### 何でAngularやってんの？

<ul class="good">
  <li>元エンプラ系Java屋さんなのでDIには慣れてた</li>
  <li>Angularの作りがJava Server Facesと何となく似ていた(phaseが複雑な所とか)</li>
</ul>
-->

### Angularで何作ってんの？

---

<a href="https://wacul-ai.com" target="_blank">https://wacul-ai.com</a>

<img src="./resources/images/capt_aia.png" alt="" class="no-frame" />

---

### サービスの内容

<ul class="good">
  <li>Google Analyticsのデータを元にWebサイト解析を行うB2B SaaS</li>
  <li>登録サイト数: 約6,000(2016年9月現在)</li>
</ul>

---

### システム構成

<ul class="good">
  <li>フロントエンド: <span style="font-size:1.1em"> TypeScript, AngularJS(1.x) </span> </li>
  <li>バックエンド: golang, mongodb, elastic search</li>
  <li>解析基盤: Scala, Apache Cassandra</li>
</ul>

---

### フロントの規模

<ul class="good smaller">
  <li>Service | Component(Directive) | Filterの数:
<span style="font-size: 1.5em">
487
</span> files
</li>
  <li>TypeScriptのソースコード量:
    約<span style="font-size: 1.5em"> 28,000 </span>lines
  </li>
  <li>フロントエンジニアの数:
  <span style="font-size: 1.5em">2</span>
人</li>
</ul>

---

<ul class="good">
  <li style="font-size:larger">フロントエンジニアの数:
  <span style="font-size: 1.5em">2</span>
人</li>
</ul>

---

<img class="no-frame" src="resources/images/hiring.jpg" alt="">

WACULでは <span style="font-size:1.2em">Angular大好き！ DI無いと死ぬ! </span>
 <br>
っていうエンジニアを募集しています。

---

## Angular 1.xでの取り組み

---

<ul class="good" style="font-size: 1.2em">
  <li>Component</li>
  <li>コード自動生成</li>
  <li>エラー周り</li>
</ul>

---

### Component

---

AngularJS 1.5からは`.component`が利用可能に。 <br/>
Directiveは余程のことがないと利用しなくなった。

```js
class Ctrl {
  hoge = "HOGE";
  constructor(private $http: ng.IHttpService) {...}
  ...
}

anuglar.module("my-module").component("xxxYyyZzz", {
  bindings: {
    hoge: "<?",
  },
  template: `
    <div>{{ctrl.hoge}}</div>
  `,
  controller: Ctrl,
  controllerAs: "ctrl"
});
```

---

AngularJSといえばscopeとの2 way binding

---

### 正直どうでもよい

<p> 今は1 way or 2 way好きな方を選べるし...  </p>

---

### むしろCSSのscopeの方が問題

Component(Directive)数は100越えてる

どうやってstyle(CSS)を管理する？

---

### そこでBEM

```css
/* _xxxYyyZzz.component.scss */
.xxx-yyy-zzz {
  display: block;
  &__header {
    border-bottom: 1px solid #ddd;
    ...
  }
}
```

```
/* styles.css[compiled] */
.xxx-yyy-zzz { display: block; }
.xxx-yyy-zzz__header { border-bottom: 1px solid #ddd; }
```

```html
<!-- xxxYyyZzz.componentのtemplate -->
<div class="xxx-yyy-zzz">
  <div class="xxx-yyy-zzz__header">
    ...
  </div>
</div>
```

---

### class名長ぇわ！


```html
<!-- xxxYyyZzz.componentのtemplate -->
<div class="xxx-yyy-zzz">
  <div class="xxx-yyy-zzz__header">
    ...
  </div>
</div>
```

---

独自Directive(<a href="https://github.com/ukyo/angular-simple-bem" target="_blank">ukyo/angular-simple-bem</a>)で解決

```html
<!-- xxxYyyZzz.componentのtemplate -->
<div bem="xxx-yyy-zzz">
  <div bem="__header">
    ...
  </div>
</div>
```

<p>
<span class="fa fa-arrow-down"></span>
 &nbsp;E, MをBに付与して展開 &nbsp;
<span class="fa fa-arrow-down"></span>
</p>

```html
<div class="xxx-yyy-zzz">
  <div class="xxx-yyy-zzz__header">
    ...
  </div>
</div>
```

---

### コード自動生成

---

gulp + <a href="https://www.npmjs.com/package/inquirer" target="_blank">inquirer</a> + <a href="https://mustache.github.io/" target="_blank">mustache</a> を使って
<br> ComponentやServiceのscaffoldタスクを用意.

<img class="no-frame" src="resources/images/capt_gen.png" alt="">

---

Componentの場合、下記が生成される

* Component本体となる.ts
* Componentに対応するBEMの.scss
* Karma用のspec.ts

---

ex:

```js
module {{modName}} {

    const template = `
    <div bem="{{nameChainCase}}"></div>
    `;

    const bindings: {[bindings: string]: string} = {
    };

    class {{nameCap}}Ctrl {
        /* @ngInject */
        constructor() { }
    }

    angular.module('{{pkg}}').component('{{name}}', {
        template,
        bindings,
        controller: {{nameCap}}Ctrl,
        controllerAs: 'ctrl'
    });
}
```

<!--

#### コード自動生成 ~API~

1. yamlでAPI定義書く
1. <a href="https://github.com/interagent/prmd" target="_blank">prmd</a>でJSON Hyper SchemaのJSONを生成
1. jsonから、TypeScript interfaceコードを生成
1. HTTPクライアントコードやモックサーバで生成されたinterfaceを利用


interfaceのコード生成は独自にツールを実装したものの、メンテナンスがしんどい。

今ならswagger使うと思う

-->

---

### エラー周り

---

.coffeeから.tsに乗り換えた当初は...


<p style="font-size: larger">
  「TypeScriptで型のある生活を手に入れた！<br />これでエラーとはオサラバだね！」
</p>

---

現実は...
<ul class="good">
  <li class="no-mark">
    誰か：「なんかエラー起きてるんだけど...」 <br />
  </li>
  <li class="no-mark">
    おれ：<span style="font-size: 1.5em">「はうあ！」</span>
  </li>
</ul>

---

~~仕方ないので~~ エラーモニタリングサービスの<a href="https://rollbar.com/?dr" target="_blank">Rollbar</a>を利用することに．

Angularの `ExceptionHanlder` をhook、 <br>
例外発生時にスタックトレースをRollbarへ投げている

---

こんな感じ:

<img class="no-frame" src="./resources/images/capt_rollbar.png" alt="">

---

Rollbarを選んだ理由：

<ul class="good">
  <li>source mapを公開する必要がない</li>
  <li>事前に.js.mapをPOSTしておくことで、Rollbarの画面上でstack traceとsource mapが紐付けられる</li>
</ul>

サービス選定してた当時、Raygun, Sentry等の類似サービスにはsource map連携の機能がなかった
(今はあるかも？)

---

### 今日覚えて帰って欲しいこと

<ul class="good">
  <li>CSSをコンポーネントに閉じ込める仕組みがあると良い</li>
  <li>コード生成でよい進捗。No残業</li>
  <li>それでもエラーは起きる。通知機能用意しておけ</li>
</ul>

---

## Angular 2の取り組み

---


<!--
### 心構え編

<ul class="good smaller">
  <li>「xxxのblog記事読んだけど動かないんですけど...」<br /><span class="fa fa-arrow-right"></span> 本家のAPIリファレンスかソース読んだ方が確実</li>
  <li>「`@Component()`とかキモいんだけど」<br /><span class="fa fa-arrow-right"></span> Class Decoratorsはstage 2だ。慣れろ</li>
</ul>

<ul class="good smaller">
  <li>「何かHTTPのI/F大分違うんだけど？」<br /><span class="fa fa-arrow-right"></span> Fetch APIのpolyfillだ。慣れろ</li>
  <li>「Observable、何それ?」<br /><span class="fa fa-arrow-right"></span> 頑張れ。慣れろ</li>
  <li>「Zone.js、何それ?」<br /><span class="fa fa-arrow-right"></span> 闇だから暫く気にするな</li>
  <li>「Template Syntaxキショいんだけど」<br /><span class="fa fa-arrow-right"></span>そうだね</li>
</ul>

-->

WACULではAngular 2にも取り組んでいます。

<ul class="good smaller">
  <li>新規のSPAプロジェクトをAngular 2で立ち上げ</li>
  <li>現在はUI Componentの整理中</li>
</ul>

---

Angular 2の所感:

<ul>
  <li>書いているコードは別物感が相当強い</li>
  <li>DIや変更検知等の根本的な思想は1.xと同じ</li>
  <li>慣れてくるとあまり1.xと2.xの違いは気にならない</li>
</ul>

---

### ...でもAngularって遅いんじゃない？

---

### ComponentのCompile問題

Angular 2でも、1.xと同様にComponentのテンプレートは実行時にコンパイルされる

---

馬鹿にならないオーバーヘッド

<img class="no-frame" src="resources/images/capt_rtcompile.png" alt="" width="700px">

<p class="smaller">※ Component数: 60程度</p>

---

### Solution: AoT compile

Componentのテンプレートを事前に変換して <br />
実行時オーバーヘッドを削減する仕組み.

Angularが公式にcompiler CLI(`ngc`)を提供している。

---

#### ngcのイメージ

<img class="no-frame" src="resources/images/ngc_output.png" alt="">

<p class="smaller">
template(HTML)からはTypeScriptのコードが生成されるため、<br>
事前にtemplateの記述をチェックすることも可能
</p>

---

### チラ見せタイム

<p style="font-size:0.6em"> ※ 画面は開発中の物です</p>

---

その他のAngular 2性能改善テーマ:

<ul class="good">
  <li>Lazy module loading</li>
  <li>Server Side Rendering</li>
  <li class="no-mark">etc...</li>
</ul>

---

## Thank you!


