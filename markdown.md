class: center, middle

# Backbone, Chaplin, Marionette そして React - Quipper における Single Page Application 開発の変遷

.right[Kensuke Nagae (@kyanny)]

.right[Aug 18 2015, Ginza.rb]

---

class: center, middle

あ…ありのまま 今　起こった事を話すぜ！

「おれは　Railsエンジニアとして転職したと  
思ったら　いつのまにかSPAばかり書いていた」

---

# お話すること

* おさらい: なぜSPAなのか、なぜJavaScriptフレームワークなのか 3m
* Quipperでこれまで採用してきたJavaScriptフレームワークと所感 6m
  * Backbone
  * Chaplin
  * Marionette
  * React
* RailsとSPAの共存 2m
* SPAに向いているAPIの設計と実装 2m

<!--
# 話せそうなネタ

* Chaplin.js
  * なぜ @reuse はイマイチか、そしてフロントエンド開発のアキレス腱 build/deploy ツール密結合のダメさ
* Marionette.js とコンポーネント志向
* React の素晴らしさはどこにあるか
  * △バーチャルDOMだからはやい
  * ○「毎回画面を全部書き換える」古きよき単純なアーキテクチャへ回帰したことがすごい
  * シンプルなアーキテクチャのパフォーマンス面での問題を実装で解決したのがバーチャルDOM（それ自体技術的には高度ですごいが、そもそものアーキテクチャ的な正しい判断があってこそ活きる）
-->

---

# おさらい: なぜSPAなのか

* モバイルネイティブアプリの速度にWeb技術で対抗するため

---

* モバイルWebの起動は遅い（3G/750Kbps）

<video src="regular-3g-load.mov" controls></video>

---

* タップごとに何秒も待てない、SPAの「のろさ」なら我慢できる

<video src="regular-3g-quiz.mov" controls></video>

---

# おさらい: なぜJavaScriptフレームワークなのか

* 素朴にjQueryとAjaxで込み入ったアプリケーションを作ると破綻する
* JavaScriptアプリケーションの世界にも複雑さにたえうるアーキテクチャが求められた

---

# QuipperとSPA

* Quipperの主力製品
  * QLink（先生向け 宿題配信 成績管理）
  * QLearn（生徒向け 学習アプリ モバイル対応）
  * QCreate（教材コンテンツ制作用CMS）
* いつのまにかすべてJavaScriptフレームワークによるSPAへと変貌
  * 内訳（昔）: Backbone x 1, Marionette x 2
  * 内訳（今）: Marionette x 3

---

# SPA採用の理由

* これといって特になし（実話）
  * ネイティブアプリ開発者が少なく、Web開発者が多かったので、ごく自然に
  * Webかネイティブか？という議論は多少あった
  * SPAか「古きよきウェブアプリケーション」か、は議論にすらならなかった

---

# Backbone

* 最初に採用されたJavaScriptフレームワーク
* モバイル対応必須のアプリだったためSPAとして実装
* 狙いどおり応答性の高いアプリケーションが実現できた（デモ動画がコレ）
* しかしコードベースはちょっと複雑

---

# Backbone最大のウソ

* x 背骨（骨格）
* o バラバラの骨
  * 正しい骨格の組み方を知らないと、いびつに

---

* [要出典] 困るパターン: initialize に詰め込みすぎ

```javascript
var AppView = Backbone.View.extend({
  initialize: function() {
    this.foo = new Foo();
    this.bar = new Bar();
    this.baz = new Baz();
    this.qux = new Qux();
    this.quux = new Quux();

    // どんだけいっぱい new してるんだよ...（何行も続く）

    this.render(); // で render かよ！テストしづらいからやめてほしい...
    return this;
  }
});
```

---

* [独自研究] 困るパターン: render に詰め込みすぎ

```javascript
var AppView = Backbone.View.extend({
  render: function() {
    this.$el.html(this.template(this.model.attributes));

    // Foo() インスタンスの後始末は誰が？
    this.$el.find('.selector').html((new Foo()).render().$el.html())

    (new Bar()).render(); // Bar() インスタンスは一体どこに何を render してるの？？

    return this;
  }
});
```

---

# Quipperにおけるクライアントサイドフレームワーク

* Chaplin
  * 過去に採用されアプリを二つ作ったが
* Marionette
  * 主力アプリ三つすべてこれ（Backboneからの移行含む）
* React
* Backbone/Marionetteアプリの一機能を別で実装し連携して動く

---

# Backbone（のウソ）

* 背骨ではなく、ただの骨
* 正しい骨格の組み上げ方を知らないと、いびつな形に

---

# Chaplin

* Backboneをベースとする
* 規約多め（Railsに少し似ている）
* ビルドまわりなど、不自由さ
* @reuse の扱いづらさ
* 実質キャッシュなので

---

# QuipperにおけるChaplin

* 2013年初夏から日本で小学生向けタブレット学習サービスを作る際に採用
* 過去にBackboneで大規模SPA開発を経験した人の知見
  * 「素のBackboneだけで開発すると破綻するので避けるべし」
* 開発チームは彼以外にSPAの知見がなかったので彼が見つけてきたChaplinを選んだ
  * 当時MarionetteはChaplinとどっこいのマイナーさで、Angularは流行り始めていたが避けた
* その後2014年に開始した別プロジェクトでも再び採用したがプロジェクト中止

---

# Marionette

* これもBackboneをベースとする
* LayoutViewのわかりやすさと汎用性
* 規約はゆるいがmoduleによってきれいなアーキテクチャを維持できる

---

# QuipperにおけるMarionette

* 主力アプリがBackboneで残りはRails+jQueryだったが、遅さが問題になった
* Chaplinを採用したチームから「次も選ぶほどではない」というフィードバック
* Backboneの経験値はたまったのでそれベースのMarionetteが選ばれた
* いろいろアドバンテージを活かせた結果、主力アプリすべてMarionetteを採用

---

# Chaplin vs Marionette

* 思想の違い
  * フレームワーク側で頑張ろうとするChaplin
  * 便利なパーツを用意して開発者に任せるMarionette
* リスク承知でレールを外れざるをえないのがフロントエンドの世界
* 無茶して規約を迂回するリスクを払わずに済むMarionetteのほうがよい

---

# QuipperにおけるReact

---

# SPA時代のAPIのあり方

* [例えば OSFA な API をやめる](http://blog.kyanny.me/entry/2014/03/06/%E4%BE%8B%E3%81%88%E3%81%B0_OSFA_%E3%81%AA_API_%E3%82%92%E3%82%84%E3%82%81%E3%82%8B)
* Railsのscaffoldで作ったような「きれいな」APIはSPAからは扱いづらい
  * 画面を組み立てるためには複数のリソースが必要になることがほとんど
  * それぞれ個別のエンドポイントを叩いてレスポンスを待っていたら応答性が犠牲になるしコードももろくなる
* SPA側に都合の良いデータを返すAPIを作るほうが結果的に楽
  * 単純なサーバサイド（Ruby）+込み入ったクライアントサイド（JavaScript） vs 込み入ったサーバサイド（Ruby）+単純なクライアントサイド（JavaScript）

