# git fetch と git pull の違い
##### https://qiita.com/wann/items/688bc17460a457104d7d

### どちらもリモートリポジトリの最新upをローカルリポジトリに持ってくる操作

## git fetch

    git fetch origin main
#### リモートリポジトリのブランチ(GitHubでのmain) → ローカルリポジトリのorigin/mainブランチに写す操作
#### ローカルのmainブランチは作業ブランチであるインデックスと繋がっている
#### origin/mainブランチはローカルのmainブランチより1つリモート寄りでローカルに存在するブランチ(リモート追跡ブランチ)
![gitallstructure3](https://user-images.githubusercontent.com/121922228/215660740-17011c32-a15c-40ce-9402-c35dbf61a2c1.png)

#### git fetchでは main(GitHub) から origin/main(ローカル) に最新upを受け取る
![gitallstructure4](https://user-images.githubusercontent.com/121922228/215662394-047253c9-5ae6-46ae-9413-298278ca09c8.png)

## git merge

    git merge origin/main
##### https://www.sejuku.net/blog/71003
#### origin/mainからmainへ
![gitallstructure5](https://user-images.githubusercontent.com/121922228/215666222-f517ca26-35fe-4d50-b42a-fd86c3962feb.png)

## git pull

    git pull origin main
#### git fetchとgit mergeの合わせ技
#### コンクリフト注意
![gitallstructure6](https://user-images.githubusercontent.com/121922228/215666881-f9cdb7fd-5777-4c51-8607-a1917dceed57.png)
