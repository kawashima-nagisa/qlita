---
title: StimulusとFetch APIで簡単に動的なデータ表示を作成する方法
tags:
  - Rails
  - FetchAPI
  - Stimulus
private: false
updated_at: '2024-11-02T13:00:10+09:00'
id: 6b03a99ff7b329826384
organization_url_name: hab-co
slide: false
ignorePublish: false
---

#### Stimulus 導入方法は以下の記事が参考になります

https://blog.to-ko-s.com/stimlus-introduce/

#### 前提条件

Rails7 が導入されていること
users テーブルに必要なカラム：
name:string ユーザーの名前
description:text: ユーザーの説明（自己紹介など）

#### users コントローラを作成して以下の show アクションに特定のユーザーの詳細情報を JSON 形式で返す記述をする

index.html.erb で UI 構造を作成し、show アクションでデータのみを提供するという流れにします。

```ruby
class UsersController < ApplicationController
  def index
    @users = User.all
  end

  def show
    user = User.find(params[:id])
    render json: { name: user.name, description: user.description } #追記
  end
end
```

### Stimulus コントローラの作成

user_controller.js ファイルの作成をします

```sh
rails generate stimulus user
```

そして以下のように作成された app/javascript/controllers/user_controller.js を以下のように編集します。
エラーハンドリングも行っており、通信に失敗した場合はエラーメッセージが表示されます。

```javascript
// app/javascript/controllers/user_controller.js
import { Controller } from "@hotwired/stimulus";

export default class extends Controller {
	// ターゲット名を定義  Stimulusがこのコントローラの中でuserDropdownTargetとuserDetailsTargetというプロパティを作成
	static targets = ["userDropdown", "userDetails"];

	loadUserData() {
		// `userDropdown`ターゲットにアクセス

		const userId = this.userDropdownTarget.value;

		if (userId) {
			fetch(`/users/${userId}`)
				.then((response) => response.json())
				.then((data) => {
					// `userDetails`ターゲットにデータを表示
					this.userDetailsTarget.innerHTML = `<strong>${data.name}</strong><p>${data.description}</p>`;
				})
				.catch((error) => {
					console.error("データ取得エラー:", error);
					this.userDetailsTarget.innerHTML =
						"ユーザー情報を取得できませんでした。";
				});
		}
	}
}
```

以下のユーザー一覧ページに data 属性を追記する
`data-user-target`と書くことで user_contoller.js を使う、という意味になります
また、`data-action="change->user#loadUserData"` と書くことで
select の選択が変更されたときに、user という名前の Stimulus コントローラーにある loadUserData メソッドが実行される、という意味になります。

```erb
<!-- app/views/users/index.html.erb -->
<div data-controller="user">
	<label for="userDropdown">ユーザーを選択:</label>
	<select
		data-action="change->user#loadUserData"
		data-user-target="userDropdown"
		id="userDropdown"
	>
		<option value="">--ユーザーを選択--</option>
		<% @users.each do |user| %>
		<option value="<%= user.id %>"><%= user.name %></option>
		<% end %>
	</select>

	<div data-user-target="userDetails" class="mt-4">
		<!-- ユーザー情報がここに表示する -->
	</div>
</div>
```

### まとめ

これで Rails 7 環境で Stimulus.js と Fetch API を使った非同期通信の基本的な実装が完了です。
Stimulus.js と Fetch API を組み合わせることで、Rails アプリや HTML ベースのプロジェクトでも簡単に非同期通信を実装できます。
