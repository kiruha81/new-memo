# railsをcloud9にinstall

    wget https://wals.s3.ap-northeast-1.amazonaws.com/curriculum/rails/environment.sh
###### ↓

    sh environment.sh

###### ↓

    rails -v

# rails アプリ install

    rails new アプリ名

# ターミナルのフォルダとファイル削除

    rm -rf フォルダ名

# config/environments 誰でも見れるようにするやつ

    config.hosts.clear

# rails サーバー起動

    rails s
#### ブラウジング時左上のやつが邪魔なときは

    ?pp=disable
#### をurlの後ろにつける

# active_storage installコマンド マイグレーション(rails db:migrate)忘れずに

    rails active_storage:install

# ActiveStorageインストール後、画像が表示されるページのモデルにつけるやつ

    has_one_attached :image

# 使用する画像ファイルはapp/assets/imageの中へ

# 作る前に確認 アプリ2の2章

# rails側で画像サイズの管理をするときに使うgem image_processingをinstallしたときのエラー回避のための記述をconfig/environmentsで

    config.active_job.queue_adapter = :inline

#### gem image_processingはGemfileにコメントアウトで記述あり

# gemをinstallするときはGemfileにinstallしたいgemを書いてから

    bundle install

# ユーザー管理するときはdeviseのgemを使う

## Gemfileに

    gem 'devise'

###### ↓

    bundle install

###### ↓

    rails g devise:install

###### ↓

#### deviseを用いたときのUserモデル作成の記述は通常と異なる

    rails g devise User

###### ↓

#### nameカラムを使用したいときはテーブルに追加する

###### db/migrate/～.rb

    t.string :name

###### ↓

    rails db:migrate

#### ログイン時の認証方法を:emailから:name(ユーザー名)に変えたいとき
##### config/initializers/devise.rbの何も触っていなければ41行目～49行目にあり
##### (41行目 Configuration for any authentication mechanism → 任意の認証メカニズムの構成)
##### 49行目の[:email]を

    [:name]

##### に変更する
##### deviseのviewも変更すること(email_field → text_field)
##### devise用のルーティング：devise_for :users がconfig/routes.rbに入力されている
###### sign_up、sign_in、sign_outとかのpath

#### viewもdeviseでは作成方法が異なる

    rails g devise:views

#### deviseのviewファイルのform記述はform_forなので注意
##### 現在のrailsではform_with推奨

# モデル(app/models)作成　削除は g → d

    rails g model モデル名(単数形で単語の頭文字は大文字 例：User、PostImage)

#### テーブルは小文字の複数形になる(例：users)


# カラム追加はdb/migrateの中で
#### integer(整数)、string(短文)、text(長文)、time(時刻) など　詳しくはアプリ2-2

    t.データ型 :カラム名

###### ↓

## データベース(db/)に反映させる必要がある

    rails db:migrate

## db/schema.rbで反映を確認できる

## 後から追加する場合は

    rails g migration Addカラム名Toテーブル名 カラム名:型名

## 削除

    rails g migration Removeカラム名Fromテーブル名 カラム名:型名

##### もしくは
##### dbの稼働状況を確認

    rails db:migrate:status

##### dbをdownすることができるコマンド(戻す)

    rails db:rollback

###### ↓

    db/migrate内のテーブルファイルでカラム追加

###### ↓

    rails db:migrate


# コントローラー(app/controllers)

    rails g controller コントローラー名(小文字の複数形)

##### コントローラー名の後にアクション名をつけるとviewとアクションが追加される(複数可)

# ビュー(app/view)
コントローラー作成時に記述するかapp/view(/対象のフォルダ)に新しく作成

○○.html.erb

# ルーティング(config/routes.rb)

    root to: "homes#top"

#### /でhomes/topにアクセスできる

    resources: :コントローラー名
#### ルーティング自動生成
##### new show index edit create destroy update

## deviseのview
#### confirmations：確認
#### mailer：
#### passwords：
#### registrations：登録
#### sessions：セッション、サインイン関係？
#### shared：
#### unlocks：
### install時に追加されるdevise_for :usersは通常のuserルーティングより上に記述しないとルーティングがおかしくなる

# Bootstrap導入
#### 1⃣

    yarn add jquery bootstrap@4.5 popper.js

#### 2⃣
##### config/webpack/environment.js

    const webpack = require('webpack')
    environment.plugins.prepend(
      'Provide',
      new webpack.ProvidePlugin({
      $: 'jquery/src/jquery',
      jQuery: 'jquery/src/jquery',
      Popper: 'popper.js'
      })
    )

#### 3⃣
##### app/javascriptにstylesheetsフォルダ作成
##### app/javascript/stylesheetsフォルダにapplication.scssファイル作成
##### app/javascript/stylesheets/application.scss

    @import '~bootstrap/scss/bootstrap';

#### 4⃣
##### app/javascript/packs/application.js

    import "jquery";
    import "popper.js";
    import "bootstrap";
    import "../stylesheets/application"

#### 5⃣
##### app/views/layouts/application.html.erb
##### 10行目のlinkをpackに変更
##### link → app/assets
##### pack → app/javascript　を参照する


# FontAwesome導入
##### FontAwesomeはアイコンを挿入するサービス
##### https://fontawesome.com/
##### JavaScriptで動いている

#### 1⃣

    yarn add @fortawesome/fontawesome-free@5.15.4

#### 2⃣
##### app/javascript/packs/application.jsに追記

    import '@fortawesome/fontawesome-free/js/all'

#### 3⃣
##### app/javascript/stylesheets/application.scss

    @import '~@fortawesome/fontawesome-free/scss/fontawesome';



