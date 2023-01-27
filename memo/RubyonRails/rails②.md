😂

# deviseのサインアップ、サインイン時のカラム受け取りはデフォルトでemailとpasswordのみ
#### nameカラムを使うときは追記が必要(①で書いてる)
#### その後コントローラー(application_controller.rb)で許可しないといけない

    before_action :configure_permitted_parameters, if: :devise_controller?

    protected

    def configure_permitted_parameters

      devise_parameter_sanitizer.permit(:sign_up, keys: [:name])

    end

# form_with
#### 基本形

    <%= form_with model: , url: , method: :デフォルトはget, do |f| %>

      <%= f.text_field :カラム名, placeholder: "空白時になにか書ける" %>

      <%= f.text_area :カラム名 %>

      <%= f.submit '○○' %>

    <% end %>
#### 記述例

    <%= form_with model: @post_image do |f| %>
      <h4>画像</h4>
        <%= f.file_field :image, accept: "image/*" %>
      <h4>ショップ名</h4>
        <%= f.text_field :shop_name %>
      <h4>説明</h4>
        <%= f.text_area :caption %><br>
      <%= f.submit '投稿' %><br>
    <% end %>

#### file_fieldはデフォルトだと何でもアップロードできるのでaccept(受け入れる)でファイルの種類を絞る

# 変数 = モデル名.new
#### 格納するための箱のようなもの。箱がないとカラムたちも入れない
#### この記述でモデル名に箱ができて変数に格納できる状態
#### controllerの新規投稿(など)をするviewのアクションやcreateに書く
#### (newにモデル名.newを書いたら、createに、モデル名.new(引数))
#### (ここの引数はストロングパラメータの定義名になる)

# ストロングパラメーター
#### 記述例

    private

    def list(モデル名)_params

      params.require(:list(モデル名)).permit(:title, :body(カラム名))

    end


##### params：パラメータ
##### require：必須
##### permit：許可

#### 実装することによって脆弱性をなくしている。
### privateに対してprotectedがある。
#### 記述例(deviseのサインアップ、サインイン時のカラム受け取りの許可)

    protected

    def configure_permitted_parameters

      devise_parameter_sanitizer.permit(:sign_up, keys: [:name])

    end


#### protectedは他のコントローラーからも呼び出せるという効果がある


# インスタンス変数
#### @をつけることによってviewとやり取りできる
#### コントローラーの変数は定義された変数でしかやり取りできない
##### 例：コントローラーのshowで定義した変数はviewのshowでしか使えない

# eachメソッドを使用した一覧表示
#### 記述例

    <%= @lists.each do |変数| %>


      <%= 変数.id %>

      <%= 変数.title %>

      <%= 変数.body %>


    <% end %>


#### eachは順番に取得するメソッド

# モデル名.all
#### コントローラーのindexアクションなどに一覧表示するために使う

    @変数 = モデル名.all

# findメソッド
#### 記述例

    @list = List.find(params[:id])

#### findは引数を受け取るメソッド
#### params：パラメータ
#### [:id]はidのカラム
### Listモデルからidパラメータを取得して(探して、見つけて)いるインスタンス変数。

# link_toメソッド
#### 基本形

    <%= link_to 表示させるテキスト , リンク先URL [,オプション] %>
#### 記述例

    <%= link_to list.title, list_path(list.id) %>

#### methodも指定できる。デフォルトはGET

# 保存、更新、削除
#### GET　　　データの取得(ページ自体もデータ)
#### POST　　 新しいデータの作成
#### PUT　　　既存のデータの更新
#### PATCH　　既存のデータの一部更新
#### DELETE	　既存のデータを削除

#### POST   = create
#### PUT    = update
#### PATCH  = update
#### DELETE = destroy

# after_sign_in_path_forとafter_sign_out_path_for
#### 記述例

    def after_sign_in_path_for(resource)

      about_path

    end


#### deviseをinstallすると使えるメソッド
#### サインイン(サインアウト)した後にどこに移動するか設定する
#### controllers/application_controller.rbで記述する
#### デフォルト設定はroot_path

# before_actionについて
#### 定義されたアクション等の前に実行することができる？

# 動的ヘッダー
#### 記入例

    <header>
      <% if user_signed_in? %>
        <li>
          <%= link_to "ログアウト", destroy_user_session_path, method: :delete %>
        </li>
      <% else %>
        <li>
          <%= link_to "新規登録", new_user_registration_path %>
        </li>
        <li>
          <%= link_to "ログイン", new_user_session_path %>
        </li>
      <% end %>
    </header>

#### ifを用いて user_signed_in? メソッドを使う。deviseをinstallすることで使用可能になるヘルパーメソッド。
#### ログイン済ならtrue、未ログインならfalse

# アソシエーション
#### https://www.youtube.com/watch?v=iJkg9OuYfYk
#### モデル間との繋がりを持たせること
#### 1：N の関係
##### 1がuser、Nが複数投稿するものだとして、
##### 複数投稿するもの、Nに番号をつける。Nに新しいカラム(外部キーという)を作成する(ここではuser_idとする)
##### そうすることにより複数投稿するものに番号が割り振られ
##### 誰がどの投稿をしたかわかるようになる。
##### また、ソートできるようにもなる(id1の投稿とuser_id1の(複数の)投稿が紐づくため)。
#### 利点
##### 構築さえすればコード操作がシンプルになる
#### 設定方法
##### 1：Nの
##### 1のモデル(app/models)に

    has_many :モデル名の複数形(例：post_images), dependent: :destroy
##### dependent: :destroy は1が消えるとNも消えるという設定

##### Nに

    belongs_to :モデル名の単数形(userなど)

#### controllerへの記述例

##### Nのコントローラー

    @Nの変数 = Nのモデル.all

##### 1のコントローラー

    @1の変数 = 1のモデル.find(params[:id])
    @Nの変数 = @1の変数.カラム名

##### アソシエーションを設定しているとコントローラーやアクションが異なっていても行き来できる
##### 1の1つの1dが持っているカラムを全て呼び出しNの変数に渡す


# current_user
#### 現在ログイン中のユーザーに関する情報を取得できる

# current_（user.id）
#### currentは現在という意味
#### 記述例(controllerでcreateアクション)

    <!-- １ -->
    @変数 = モデル名.new(ストロングパラメータの引数)
    <!-- ２ -->
    @変数.（user_id） = current_（user.id）（）はわかりやすくするために書いているだけ、いらない
    <!-- ３ -->
    @変数.save
#### @変数.カラム名で１のNのモデルの中のカラムを引き出すことができる
#### １で変数を定義し、２で１で記述したモデルの中のuser_idを抽出し、user_idとcurrent_user(現在のユーザー)のidが同じであることを記しており
#### 同じなら３で@変数にsave(保存)をする
##### むずいならアプリ2の10章 登録機能＞Controllerを見る

#### 記述例2

    <% if @post_image.user == current_user %>
      <%= link_to "削除", post_image_path(@post_image), method: :delete %>
    <% end %>

##### もし、投稿者 == 現在ログインしているユーザ だったら
#####   削除ボタン
##### 終わり

# 画像表示(gem image_processingを使用して)
#### 記述例(models/～.rb)

    def get_image
      if image.attached?
        image
      else
        'no_image.jpg'
      end
    end

#### モデルに書くことによって、get_imageを呼び出せる
##### 例

    @user = User.find(1)
    @user.get_image

#### 記述例(さらにActiveStorageを使う記述)

    def get_image
      unless image.attached?
        file_path = Rails.root.join('app/assets/images/任意のimage')
        image.attach(io: File.open(file_path), filename: '任意のimage', content_type: 'image/png')
      end
      image
    end

##### unlessはifの逆！
##### ifはtrueのとき、unlessはfalseのときの処理

##### view

    <% @post_images.each do |post_image| %>
      <div>
        <%= image_tag post_image.get_image %>
        <p>投稿ユーザー画像：<%= image_tag 'sample-author1.jpg' %></p>
        <p>ショップ名：<%= post_image.shop_name %></p>
        <p>説明：<%= post_image.caption %></p>
        <p>ユーザーネーム：<%= post_image.user.name %></p>
      </div>
    <% end %>

###### アプリ2 11章

    def get_profile_image(width, height)
      unless profile_image.attached?
        file_path = Rails.root.join('app/assets/images/aokami.png')
        profile_image.attach(io: File.open(file_path), filename: 'aokami.png', content_type: 'image/png')
      end
      profile_image.variant(resize_to_limit: [width, height]).processed
    end

##### アプリ2 14章
