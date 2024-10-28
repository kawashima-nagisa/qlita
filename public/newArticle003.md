---
title: web制作アニメーション備忘録　パート①
tags:
  - Wordpress
  - CSS
  - Web制作
  - Web制作アニメーション
  - Sass
  - GSAP
private: false
updated_at: ""
id: null
organization_url_name: hab-co
slide: false
ignorePublish: false
---

## web 制作アニメーション備忘録 ①

最近は仕事で web 制作をすることはほとんどありませんが<br>

自身の記録として残すことにしました。　以下はサイト制作にあたりこんな animation あるかもと思い調べて実装したものです。<br>

ぜひ参考にしてください

### 完成イメージ

![movie.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/928c1bae-a7d7-a966-f617-43554f4c7420.gif)

hover すると中央の画像が丸くなり左右に小さな画像が現れるアニメーションです。<br>
GSAP（GreenSock Animation Platform）を使用して実装してみました。<br>
具体的には、mouseenter と mouseleave イベントを使って、ホバー中とホバー解除後にそれぞれ異なる動作を設定します。

[該当コードはこちら](https://github.com/kawashima-nagisa/web-/blob/main/html6.html)

### コードの解説

主な流れ<br>

- メイン画像のホバーアニメーション：ホバー時にメイン画像を丸くして、少し大きく表示します。
- 上下の小さな画像をフェードイン：メイン画像のホバー中、上下に配置した小さな画像をフェードインさせ、上下に移動させます。
- ホバー解除アニメーション：ホバーが外れたときに画像を元の状態に戻します。

GSAP の基本設定<br>
まず、GSAP を使ってアニメーションを実装するため、次の CDN スクリプトを HTML の <head> タグ内に追加します。

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.2/gsap.min.js"></script>
```

このスクリプトを読み込むことで、GSAP の`gsap.to()` 関数を使用できるようになります。

ホバーアニメーションの実装
[GSAP](<https://gsap.com/docs/v3/GSAP/gsap.to()>)

まず、HTML のメイン画像と小さな上下の画像を querySelector で選択し、それぞれ image, topImage, bottomImage という変数に格納します。

```Javascript
var image = document.querySelector('.image');
var topImage = document.querySelector('.top-image');
var bottomImage = document.querySelector('.bottom-image');

```

次に、mouseenter イベントと mouseleave イベントを使って、マウスが画像上に乗ったときのアニメーションと離れたときのアニメーションを設定します。
<br>ポイントは３つの画像の要素は最初横並びで上下中央揃えになっているということです。左右の小さな画像は mouseenter イベントによってそれぞれ上下に移動します。

```
image.addEventListener('mouseenter', function () {
  gsap.to(image, {
    duration: 0.5, // アニメーションの実行時間
    borderRadius: "50%", // 画像を丸く
    transform: "scale(1.1)", // 拡大
    ease: "power2.out" // 滑らかなアニメーション
  });

  // 上部の小さな画像をフェードインして下に移動
  gsap.to(topImage, {
    duration: 0.5,
    opacity: 1, // フェードイン
    y: 100, // 下方向に移動
    ease: "power2.out",
    delay: 0.5 // メイン画像のアニメーション後に表示
  });

  // 下部の小さな画像をフェードインして上に移動
  gsap.to(bottomImage, {
    duration: 0.5,
    opacity: 1, // フェードイン
    y: -100, // 上方向に移動
    ease: "power2.out",
    delay: 0.5 // メイン画像のアニメーション後に表示
  });
});
image.addEventListener('mouseleave', function () {
  gsap.to(image, {
    duration: 0.5, // アニメーションの実行時間
    borderRadius: "0%", // 画像を四角に戻す
    transform: "scale(1)", // 元のサイズ
    ease: "power2.out"
  });

  // 上部の小さな画像をフェードアウトして元の位置に戻す
  gsap.to(topImage, {
    duration: 0.5,
    opacity: 0, // フェードアウト
    y: 0, // 元の位置に戻す
    ease: "power2.out"
  });

  // 下部の小さな画像をフェードアウトして元の位置に戻す
  gsap.to(bottomImage, {
    duration: 0.5,
    opacity: 0, // フェードアウト
    y: 0, // 元の位置に戻す
    ease: "power2.out"
  });
});

```

## web 制作アニメーション備忘録 ②

### 完成イメージ

![スポットライト](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/aed749cf-b51f-73b1-7263-93ffe33ffd62.gif)

clip-path を利用して、背景画像の一部をスポットライトのように切り抜き、アニメーションでその切り抜き範囲を動かします。

[該当コードはこちら](https://github.com/kawashima-nagisa/web-/blob/main/index7.html)

### コードの解説

背景画像とグラデーションの設定
.container に、背景画像とグラデーションを重ねています。グラデーションが上部、画像が下部に配置されることで、柔らかい背景効果を作り出しています。

```css
.container {
	background: linear-gradient(
			to bottom,
			rgba(212, 212, 107, 1) 10%,
			rgba(212, 212, 107, 0.094) 100%
		), url(https://picsum.photos/600/400);
	background-size: 100% auto, auto 100px;
	background-position: top, bottom;
	background-repeat: no-repeat, no-repeat;
}
```

background: グラデーションと画像の順に重ね合わせて背景を構成。
linear-gradient(...)：背景に黄系のグラデーションを設定し、ページ上部で色が濃く、下に行くにつれて薄くなるようにしています。
url(...)：背景画像として、外部画像を指定。
background-size: グラデーションを背景全体に広げ、画像は高さ 100px に設定。
background-position: グラデーションを上に、画像を下に配置。
background-repeat: 背景を繰り返し表示しない設定

スポットライト効果の設定

spotlight クラスの要素 では、clip-path で多角形の形にクリッピングし、アニメーションを使ってこのクリッピング範囲を動かしています。

```css
.spotlight {
	background: white; /*スポットライトの外側の背景色 */
	clip-path: polygon(
		50% 100%,
		50% 1em,
		50% 100%,
		100% 100%,
		100% 0,
		0 0,
		0 100%
	);
	animation: spotlight-init 1s ease-out forwards, spotlight-anim 5s ease
			infinite forwards 1s;
}
```

background: 白の背景で、クリッピングの形に合わせて外側の背景が表示されます。
clip-path: polygon()を使って多角形を描画し、スポットライトの形を作成します。
polygon の各座標で、クリッピングの形状を定義しています。

キーフレームアニメーションの設定

以下のキーフレームに分けて作成します
流れとしては
中央から細長いスポットライトがゆっくりと広がり、ライトが左右に繰り返し移動していくという流れになります。

- spotlight-init: 初期のアニメーション。
- spotlight-anim: スポットライトを動かすアニメーション。

```css
@keyframes spotlight-init {
	0% {
		clip-path: polygon(
			50% 100%,
			50% 1em,
			50% 100%,
			100% 100%,
			100% 0,
			0 0,
			0 100%
		);
	}
	100% {
		clip-path: polygon(
			35% 100%,
			50% 1em,
			65% 100%,
			100% 100%,
			100% 0,
			0 0,
			0 100%
		);
	}
}
```

0%: 最初の形状は中央で縦に細長い多角形です。
100%: 最終的な形状はやや横幅が広くなり、スポットライトの広がりが表現されます。

```css
@keyframes spotlight-anim {
	25% {
		clip-path: polygon(
			-40% 100%,
			50% 1em,
			20% 100%,
			100% 100%,
			100% 0,
			0 0,
			0 100%
		);
	}
	75% {
		clip-path: polygon(
			80% 100%,
			50% 1em,
			140% 100%,
			100% 100%,
			100% 0,
			0 0,
			0 100%
		);
	}
	50%,
	100% {
		clip-path: polygon(
			35% 100%,
			50% 1em,
			65% 100%,
			100% 100%,
			100% 0,
			0 0,
			0 100%
		);
	}
}
```

25%: 画面左側に移動します。
75%: 画面右側に移動します。
50%および 100%: 中央に戻ります。

---

## まとめ

GSAP の `gsap.to()` を使用して、各要素にアニメーションにフェードイン・フェードアウト効果を設定。
duration でアニメーションの速度、opacity で表示・非表示の切り替えを行い、 y プロパティで位置の調整を行っています。

また、CSS の clip-path と@keyframes を活用して、スポットライトの位置や形状が変化す流動きを CSS だけで実装しました。
少しでも参考になれば幸いです。
