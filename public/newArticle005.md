---
title: RailsとGSAPを連携する方法：StimulusとCDNでアニメーションを実現する
tags:
  - gsap
  - Stimulus
  - Rails7
private: false
updated_at: '2024-11-25T00:06:41+09:00'
id: d2684d6af33e6b047df2
organization_url_name: hab-co
slide: false
ignorePublish: false
---

# はじめに

Rails アプリケーションでアニメーションを実装する際に便利なライブラリとして、GSAP (GreenSock Animation Platform) を利用しようとしました。しかし、GSAP を Rails7 で利用しようとした際、以下のようなエラーに遭遇しました。

```bash
Cannot set property window of #<Window> which has only a getter
Uncaught SyntaxError: The requested module 'gsap' does not provide an export named 'gsap'

```

この記事では、これらのエラーが発生する原因と、Stimulus と CDN を組み合わせて GSAP を正常に動作させる方法を詳しく解説します。

### 前提条件

npm や yarn などを使わないで GSAP を導入しようとする場合

#### エラー発生の背景

1. Importmap での GSAP 読み込み
   Rails 7 では、JavaScript モジュールを管理するために Importmap が採用されています。
   しかし、GSAP はデフォルトで提供されるファイル形式が Rails の Importmap で期待される ES モジュール形式と一致しないことがあります。

以下のように Importmap で GSAP をピンして読み込もうとすると問題が発生します

[GSAP の install 方法](https://gsap.com/docs/v3/Installation?tab=yarn&module=esm&method=private+registry&tier=free&club=false&require=false&trial=true)

これは GSAP は元々 npm や yarn、または CDN 経由で利用することを想定したライブラリであるからか、ails 7 の Importmap で利用する際には利用するのは難しそうです。

```bash
bin/importmap pin gsap
```

この場合、次のエラーが発生します：

```bash
Cannot set property window of #<Window> which has only a getter
```

2. CDN で読み込む場合の問題
   CDN 経由で GSAP を読み込むと、グローバルスコープに直接 GSAP オブジェクトを登録します。しかし、Stimulus コントローラ内でモジュールとして扱おうとするとエラーが発生することがあります：

```sh
Uncaught SyntaxError: The requested module 'gsap' does not provide an export named 'gsap'
```

#### 解決方法

CDN と Stimulus の併用
GSAP を CDN 経由で読み込む
Rails の application.html.erb に CDN のスクリプトタグを追加します：

```html
<script src="https://cdn.jsdelivr.net/npm/gsap@3.12.5/dist/gsap.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/ScrollTrigger.min.js"></script>
```

Stimulus コントローラで GSAP を利用
CDN で読み込んだ GSAP はグローバルスコープに登録されるため、Stimulus コントローラ内で以下のように記述します

```javascript
import { Controller } from "@hotwired/stimulus";

export default class extends Controller {
	connect() {
		console.log("GSAP Stimulus Controller connected!");

		// ScrollTriggerの登録
		gsap.registerPlugin(ScrollTrigger);


```

#### 補足

- GSAP と ES モジュール形式について
  GSAP は ES モジュール形式（ESM）や UMD 形式のファイルを提供しています。しかし、Rails の Importmap は特定のファイル形式（ESM）を期待して動作します。問題は、GSAP が CDN で提供しているファイルがこの形式に完全に適合しないことです。

- CDN ファイル:
  多くの場合、UMD 形式が提供され、ブラウザのグローバルスコープに依存しています。
  Rails の Importmap が期待する ES モジュール形式ではないため、import 文を使うとエラーになります。
- npm や yarn でインストールする場合:
  ESM 形式が提供されており、おそらく使用が容易です。

#### 結論

GSAP は Rails の Importmap との相性が悪く、サポート対象外と考えるのが妥当です。
[以前に rails7 と importmap について質問されていた投稿](https://gsap.com/community/forums/topic/42758-gsap-with-rails-7-and-stimulus/)

### まとめ

GSAP は Rails アプリにモダンなアニメーションを導入するのに最適なツールですが、設定に注意が必要です。
初心者には CDN と Stimulus の併用がおすすめです。
しかしながらパフォーマンスとしてはよくないので、その場合は npm や yarn を使うしかないかもしれません。
