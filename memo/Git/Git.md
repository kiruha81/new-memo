# Gitの仕組み
<img width="298" alt="gitallstructure" src="https://user-images.githubusercontent.com/121922228/215035266-fa213272-bca0-4755-8fc8-338f8086ba36.png">

# GitHubとCloud9の連携
##### Cloud9で鍵の作成

    ssh-keygen
##### Enter3回

#### 鍵の確認

    ls ~/.ssh
##### 公開鍵(id_rsa.pub)：連携先(GitHubなど)の鍵
##### 秘密鍵(id_rsa)：Cloud9の鍵
##### (authorized_keys：認可されたキー)

#### 公開鍵の取得

    cat ~/.ssh/id_rsa.pub
##### ssh-rsa ～ ip-数字までをコピー

##### https://github.com/settings/keys → [New SSH key] → 貼付

#### 接続テスト

    ssh -T git@github.com
##### なんか出たらyesを入力(一致の確認？)

#### Gitのブランチ名はデフォルトでmainだがCloud9はmasterなのでCloud9のブランチ名を変更し一致させる

    git config --global init.defaultBranch main

# Cloud9でGitを使う

    git init
##### .gitフォルダが作成される

###### Gitの状態確認ができる

    git status

#### Cloud9からインデックスに追加

    git add [ファイル名]
##### ファイル名に.(ドット)でフォルダ内全て追加可能

#### インデックスからローカルリポジトリへコミット

    git commit -m "[メッセージ]"
##### -m はメッセージが追加できるようになるコマンド

###### インデックスとワークツリー(Cloud9)の差分表示

    git diff

###### コミット履歴確認

    git log

##### 間違えてコミットしたときの削除方法

    git reset コミット番号
##### コミット番号に HEAD^ と入力すれば直近のコミットを削除できる
##### HEAD^ は一つ前のコミットを指す

#### コミットしたローカルリポジトリをリモートリポジトリへ
##### https://github.com/new → リポジトリ名(Repository name)を入力して
![create_repository](https://user-images.githubusercontent.com/121922228/215036202-4391c495-714d-4d9b-a845-ea1b7f5b0fe7.PNG)
##### 作成したリモートリポジトリページに出てきたURLをSSHを選択してコピー

    git remote add origin コピーしたURL
###### 間違った場合の変更方法

    git remote set-url origin リポジトリURL
###### リポジトリURL確認コマンド

    git remote -v
#### ローカルリポジトリからリモートリポジトリ(GitHub)へアップロード

    git push origin main
##### -u コマンドを使うと次回からアクセス先とブランチ名を省略できる

    git push -u origin main

