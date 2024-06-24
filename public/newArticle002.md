---
title: dockerを使ったrails環境でのletter_opnerのtimezoneの設定
tags:
  - Rails
  - Docker
  - Letter opner
  - 備忘録
  - 自己満
  - 小ネタ
private: false
updated_at:
id:
organization_url_name: hab-co
slide: false
ignorePublish: false
---

# letter_opner でテスト送信をした際に

letter_opner でテスト送信をした際に送られてくるメールの
送信日時が UTC（協定世界時）でした。

## 早速プロジェクトの imtezone を日本時間に設定する

```config/application.rb

config.time_zone = 'Tokyo'
#Railsアプリケーション内部でのデフォルトのタイムゾーンを設定
config.i18n.default_locale = :ja
#Railsはデータベースに保存される時刻を日本時間で保存
```

としてコンソールで確認
きちんと日本時間になっている

```
Time.now  #出力結果 =>2024-05-22 14:16:09.609170085 +0900
```

再度 letter_opner でテスト送信をしてみる
なぜか送信時間は UTC のまま。
Docker コンテナのタイムゾーンを確認してみる

```
docker-compose exec web date #出力結果=>Thu Jun 20 00:51:34 UTC 2024
```

docker コンテナのシステムタイムゾーンが UTC に設定されてしまっている

# Docker コンテナのタイムゾーン設定と letter_opener でのメール表示における問題の解決

Docker を使用してアプリケーションを開発していると、コンテナ内のシステムタイムゾーンがデフォルトで UTC（協定世界時）に設定されていることがあります。
これにより、letter_opener などのツールで表示されるメールのタイムスタンプが、日本の標準時（JST、Japan Standard Time、Tokyo タイムゾーン）とは異なることがあります。

# タイムゾーンの設定を Tokyo に変更する

## Dockerfile でタイムゾーンを設定する

下記のコマンドを dockerfile に追記します

```Dockerfile

RUN ln -sf /usr/share/zoneinfo/Asia/Tokyo /etc/localtime && \
    echo "Asia/Tokyo" > /etc/timezone
```

### 補足

下記の方法を最初は試していましたが、docker のシステムタイムゾーンは変わりませんでした。

```Dockerfile
version: '3'
services:
  web:
    image: myapp_image
    environment:
      - TZ=Asia/Tokyo #追記


```

# letter_opener でのメール時間表示の確認

タイムゾーンの設定を変更したら、letter_opener で表示されるメールのタイムスタンプが日本時間に合わせて表示されるか確認します。
再度コンテナを起動し、メールを送信して letter_opener で確認すると、
タイムスタンプが期待通り JST になりました。

```sh
docker-compose up -d --build
```

# まとめ

Docker を使った rails 環境での timezone には、コンテナ内のシステムタイムゾーンと application 内部での timezone(config.time_zone, config.active_record.default_timezone)があることがわかりました。
Docker コンテナ内のタイムゾーンが UTC に設定されていると、letter_opener などのツールでの時間表示がずれてしまうことがあります。この記事では、Dockerfile または docker-compose.yml でタイムゾーンを Asia/Tokyo に変更する方法を紹介しました。

この設定を行うことで、開発中のコンテナ内での時間表示の不一致を解消することができました。
