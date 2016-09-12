## はじめに

---

お前ら、そんなにAngularが嫌いか...

<img src="./resources/images/capt_tl.png" alt="" class="no-frame" />

---

### 俺 with Angular

AngularJSに初めて出会ったのは、遡ること3年以上前、2013年春のことであった...

---

### 面白くないので以下略

---

#### 何でAngularやってんの？

<ul class="good">
  <li>元エンプラ系Java屋さんなのでDIには慣れてた</li>
  <li>Angularの作りがJSFと似てるところも結構あった(phaseが複雑な所とか)</li>
</ul>

---

## SPAで何を作っているのか

---

<a href="https://wacul-ai.com" target="_blank">https://wacul-ai.com</a>

<img src="./resources/images/capt_aia.png" alt="" class="no-frame" />

---

### サービスの内容

<ul class="good">
  <li>Google Analyticsのデータを元にWebサイト解析を行うB2B SaaS</li>
  <li>登録サイト数: 約6,000(2016.09.01現在)</li>
</ul>

---

### システム構成

<ul class="good">
  <li>フロントエンド: TypeScript, AngularJS(1.x)</li>
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

## We're hiring!

WACULでは <span style="font-size:1.3em">Angular大好き！ DI無いと死ぬ! </span>
 <br>
っていうエンジニアを随時募集しています。

---

## Angular 1.xでの取り組み

---

<ul class="good" style="font-size: 1.7em">
  <li>Component 指向</li>
  <li>コード自動生成</li>
  <li>エラー周り</li>
</ul>

---

### Component 指向

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

Angularのscopeや2 way bindingはどうでもいい。

<p style="font-size:larger">
  むしろCSSのscopeの方がよっぽど問題。
</p>

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

## class名長ぇわ！

```html
<!-- xxxYyyZzz.componentのtemplate -->
<div class="xxx-yyy-zzz">
  <div class="xxx-yyy-zzz__header">
    ...
  </div>
</div>
```

---

独自Directive(<a href="https://github.com/ukyo/angular-simple-bem" target="_blank">ukyo/angular-simple-bem</a>)で解決。

```html
<!-- xxxYyyZzz.componentのtemplate -->
<div bem="xxx-yyy-zzz">
  <div bem="__header">
    ...
  </div>
</div>
```

って書くと、 `class="xxx-yyy-zzz__header"` に展開してくれる。

---

### コード自動生成

---

#### 自動生成 ~Component, etc編~

gulp + <a href="https://www.npmjs.com/package/inquirer" target="_blank">inquirer</a> + <a href="https://mustache.github.io/" target="_blank">mustache</a> を使って
<br> ComponentやServiceのscaffoldタスクを用意.

例(Componentの場合):

* Component本体の.ts
* Karma用Componentのspec.ts
* Componentに対応するBEMの.scss

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

---

#### コード自動生成 ~API~

1. yamlでAPI定義書く
1. <a href="https://github.com/interagent/prmd" target="_blank">prmd</a>でJSON Hyper SchemaのJSONを生成
1. jsonから、TypeScript interfaceコードを生成
1. HTTPクライアントコードやモックサーバで生成されたinterfaceを利用

---

interfaceのコード生成は独自にツールを実装したものの、メンテナンスがしんどい。

今ならswagger使うと思う

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
  <li>CSSをコンポーネントに閉じ込める仕組みは必須</li>
  <li>コード生成でよい進捗。No残業</li>
  <li>それでもエラーは起きる。通知機能用意しておけ</li>
</ul>

---

## Angular2の取り組み

---

### 心構え編

<ul class="good smaller">
  <li>「Breaking Changeが多いんだけど」<br><span class="fa fa-arrow-right"></span> まだRCだから許したげて。年内には...!</li>
  <li>「xxxのblog記事読んだけど動かないんですけど...」<br /><span class="fa fa-arrow-right"></span> 本家のAPIリファレンスかソース読んだ方が確実</li>
  <li>「`@Component()`とかキモいんだけど」<br /><span class="fa fa-arrow-right"></span> Class Decoratorsはstage 2だ。慣れろ</li>
</ul>

---

<ul class="good smaller">
  <li>「何かHTTPのI/F大分違うんだけど？」<br /><span class="fa fa-arrow-right"></span> Fetch APIのpolyfillだ。慣れろ</li>
  <li>「Observable、何それ?」<br /><span class="fa fa-arrow-right"></span> 頑張れ。慣れろ</li>
  <li>「Zone.js、何それ?」<br /><span class="fa fa-arrow-right"></span> 闇だから暫く気にするな</li>
  <li>「Template Syntaxキショいんだけど」<br /><span class="fa fa-arrow-right"></span>そうだね</li>
</ul>

---

精神論はさておき、Angular2にも取り組んでいます。

<ul class="good smaller">
  <li>新規のSPAプロジェクトをAngular2で立ち上げ</li>
  <li>現在はUI Componentの整理中</li>
</ul>

---

### チラ見せタイム

<p style="font-size:0.6em"> ※ 画面は開発中の物です</p>

---

Angular2の今んとこ所感:

<ul>
  <li>表記は別物感が相当強い</li>
  <li>DIや変更検知等の根本的な思想は1.xと同じ</li>
  <li>慣れてくるとあまり1.xと2.xの違いは気にならない</li>
</ul>

---

Angular2で期待していること:

<ul class="good">
  <li>Lazy module loading</li>
  <li>Offline pre-compilation(Checking template syntax)</li>
  <li class="no-mark">etc...</li>
</ul>

---

## Thank you!


