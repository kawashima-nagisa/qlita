---
title: QiitaCLI で GitHub 管理してみた
tags:
  - Qiita
  - GitHub
  - QiitaCLI
private: false
updated_at: "2024-05-18T15:32:23+09:00"
id: 1810647fc4498c6d1da4
organization_url_name: hab-co
slide: false
ignorePublish: false
---

# QiitaCLI を使って記事を投稿したい

qlita に記事を真面目に投稿しようと考えたのだが
正直 qlita の記事の更新ボタンがどこにあるのかわからなくて手間取った。(今ならわかる)
調べてみると qlita CLI という手元の環境で記事の執筆・プレビュー・投稿ができるツールがあることがわかり、
qlita を使いこなせない自分に合っていると思ったので
導入したいと考えた。
また、github でも管理できるので
日々の勉強のついでに記事でも書こうかな〜
という気分になりやすいなと感じたので
導入しようと思った。

# 導入方法

他の qlita の記事にも詳細に書いてあるし
Qlita CLI の導入後に

```sh
npx qiita previewew
```

とすると http://localhost:8888 が自動で立ち上がって
QLita CLI の導入やコマンドについて記載があるのでこれを見れば良いと思う。
とはいえ、せっかく導入したので流れを軽く以下に記述する。

デスクトップに qlita 記事を書くフォルダを入れたかったので

```
cd Desktop
```

としてデスクトップまで行って

```
mkdir qlita
```

qlita ディレクトリを作成。

```
cd qlita
```

として qlita リポジトリまで行って
ここで

```
npm install @qiita/qiita-cli --save-dev
```

とする。
すると package.json ファイルや qlita.config.json などが作成される。
すると、以下の内容がターミナルに丁寧に表示されるので
"次のステップ" に書いてある通りに進めれば良い。

```
 qlita npm install @qiita/qiita-cli --save-dev
(#########⠂⠂⠂⠂⠂⠂⠂⠂⠂) ⠦ idealTree:ansi-align: timing idealTree:node_modules/ansi-align Completed in 95m

added 129 packages in 8s

36 packages are looking for funding
  run `npm fund` for details
➜  qlita npx qiita version
1.4.1
➜  qlita npm
npm <command>

Usage:

npm install        install all the dependencies in your project
npm install <foo>  add the <foo> dependency to your project
npm test           run this project's tests
npm run <foo>      run the script named <foo>
npm <command> -h   quick help on <command>
npm -l             display usage info for all commands
npm help <term>    search for help on <term>
npm help npm       more involved overview

All commands:

    access, adduser, audit, bugs, cache, ci, completion,
    config, dedupe, deprecate, diff, dist-tag, docs, doctor,
    edit, exec, explain, explore, find-dupes, fund, get, help,
    help-search, hook, init, install, install-ci-test,
    install-test, link, ll, login, logout, ls, org, outdated,
    owner, pack, ping, pkg, prefix, profile, prune, publish,
    query, rebuild, repo, restart, root, run-script, sbom,
    search, set, shrinkwrap, star, stars, start, stop, team,
    test, token, uninstall, unpublish, unstar, update, version,
    view, whoami

Specify configs in the ini-formatted file:
    /Users/nagisa/.npmrc
or on the command line via: npm <command> --key=value

More configuration info: npm help config
Configuration fields: npm help 7 config

npm@10.2.3 /Users/hoge/.nvm/versions/node/v20.10.0/lib/node_modules/npm
➜  qlita node -v
v20.10.0
➜  qlita npm install @qiita/qiita-cli@latest

up to date, audited 130 packages in 844ms

36 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
➜  qlita npx qiita version
1.4.1
➜  qlita npx qiita init
設定ファイルを生成します。

  Creating /Users/hoge/Desktop/qlita/.github/workflows/publish.yml
     Created!

  Creating /Users/hoge/Desktop/qlita/.gitignore
     Created!

  Creating /Users/hoge/Desktop/qlita/qiita.config.json
     Created!

Success! ✨

次のステップ:

  1. トークンを作成してログインをしてください。
    npx qiita login

  2. 記事のプレビューができるようになります。
    npx qiita preview

➜  qlita
```

次のステップとして

```
 npx qiita login
```

と打つとご丁寧に URL まで貼ってくれてトークンの発行を促されるので
言われた通りにリンク先に遷移してトークンを発行して
発行したトークンを入力:の箇所にトークンを打つ。

```
以下のURLにアクセスしてトークンを発行してください。（「read_qiita」と「write_qiita」にチェックを入れてください）
  https://qiita.com/settings/tokens/new?read_qiita=1&write_qiita=1&description=qiita-cli
  発行したトークンを入力: #ここにリンク先で作成したトークンを打つ
```

早速新規で記事の作成。

```
npx qiita new newArticle001
```

すると以下の画像のように public フォルダが自動作成され,
.remote フォルダと newArticle001.md が作成された。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/daa3a193-b69d-ce39-f238-64d939340831.png)

以前に投稿した記事がどんな感じでエディタで表示されるのか気になったので

```
npx qiita pull
```

として自分の qlita から記事を持ってくると以下の画像のように.remote フォルダ内と public フォルダは以下に同じものが作成された。
編集するのは public 配下の md ファイルで間違いないんだろうが
どっちを編集しても反映されるのかどうか
正直よくわかってないので
誰か教えて欲しい。
![image-1.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/e52498c5-80cb-fca3-2445-0c96c6bc3d1e.png)

publish コマンドを使ったらどうなるのか挙動を見たかったので

```
qlita npx qiita publish 38eb26f01ba1a0bcc5c8
```

とすると

```sh
Updated: 38eb26f01ba1a0bcc5c8 -> 38eb26f01ba1a0bcc5c8
Successful!
➜  qlita
```

となった。このような感じで記事の更新、作成をするのだとなんとなくわかった。

記事の preview は vsocode で
虫眼眼鏡みたいなアイコンで見れた。
![image-2.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/b6ad138d-53c1-c4b0-ce46-504c21f73205.png)
![image-3.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/c0a6977e-aa9f-85f2-f4ba-684bd712fe44.png)

ブラウザで確認したいのであれば
open in browser を押すようだ。
![image-4.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/8dc8f42b-8a34-8803-ced3-ac8498580bb7.png)

# github で管理したい

[ Qiita の記事を GitHub 管理する方法](https://qiita.com/ryocha12/items/e412306f9e8339d7cffe)

ひとまず git で commit までする。

```
qlita git  init
git add .
git ci -m "qlita記事用のリポジトリの作成"

```

そして github で public でリポジトリを作成する。
Actions secrets and variables の Repository secrets で
QIITA_TOKEN の値に qlita の最初に発行したトークンを入力して作成
![image-5.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3550857/098e8b41-5b1a-5bf6-92e7-3a2123189fec.png)

あとは main ブランチに push するだけで記事の投稿・更新ができるとのこと

push しなくても以下の記述の通り pubish コマンドで記事の公開と更新ができるよう。
せっかくなら push して更新した方がいいかな。

> push する以外にも以下コマンドや、Qiita Preview 上の「記事を投稿す る」ボタンからも記事の投稿・更新をする事が出来ます。

```
npx qiita publish 記事のファイルのベース名 # newArticle001.md だった場合は newArticle001
npx qiita publish --all # 全ての記事を反映
npx qiita publish 記事ファイルのベース名 --force # 強制的に記事ファイルの内容を Qiita に反映
```

今後は CLI を使って記事の投稿を行い
記事作成時間の削減に努めたい。
