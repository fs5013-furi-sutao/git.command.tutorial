# section.03: 誤ったコミットの解決とスタッシュ 

※チュートリアル中で使われている git コマンド以外のコマンドの説明は以下のファイルを参照  
[チュートリアル中で使った bash コマンドの説明](./explain_bash_command.md)

## master での pull
```console
git branch
```
実行結果：
```
  feature/change-read-me-cat
* feature/change-read-me-dog
  master
```

dog ブランチにいるので、master ブランチにスイッチする。

```console
git checkout master
git branch
```
実行結果：
```
  feature/change-read-me-cat
  feature/change-read-me-dog
* master
```

master で pull を実行する。
git pull を実行することで、master ブランチにおける、リモートとの差分をローカルに取り込む。

```console
cat ./README.md
```
実行結果：
```
# git.test.dir

私は猫です.

```
まだ、リモートのマージが反映されておらず「猫」となっているので、pull を実行することで「犬」になったことを確認する。

```console
git pull
cat ./README.md
```
実行結果：
```
# git.test.dir

私は犬です.

```
状況イメージ：
```
origin
               feature/change-read-me-dog  
                  E----F 
                 /    /　\
                /    /    \
               /    /      \
              /    /        \
master A-----B----D----------G 
              \  /
               C  
               feature/change-read-me-cat  
```
  
## master での変更
禁止された master への変更を検証する前に、以前に説明した git hook の追加作業を実施しておく。  
[master への push を禁止する共有 git hook の作成方法](./how_to_protected_from_pushing_origin_master.md)

```console
sed -i 's/犬/猿/' ./README.md
cat ./README.md
```
実行結果：
```
# git.test.dir

私は猿です.
```

「犬」から「猿」に書き変わったことを確認する。

## push の失敗
NOTE:  
> 2020年7月30日現在、GitHub Branch Protection の機能では、強制的なプッシュ「git push -f」は防げても、通常の「git push origin master」は防げませんでした  
[GitHub Branch Protection](https://docs.github.com/ja/github/administering-a-repository/configuring-protected-branches)  

```console
git add .
git commit -m 'change the dog to the ape in README.md'
git push
```
実行結果:
```
master branch への push は禁止です. Do not push to master branch!!!
pull request ベースの開発フローを厳守してください.
error: failed to push some refs to 'git@github.com.fs5013-furi-sutao:fs5013-furi-sutao/git.test.dir.git'

```
状況イメージ：
```
local
                                  feature/add-git-hook-pre-push
   feature/change-read-me-dog      H
                  E----F          / \
                 /    /　\       /   \
                /    /    \     /     \
               /    /      \   /       \
              /    /        \ /         \
master A-----B----D----------G-----------I----J <--Stop pushing to master by git 'pre-push' hook! 
              \  /
               C  
               feature/change-read-me-cat  
```

~~禁止されたローカルの master からリモートの master への push だったので、GitHub で行ったブランチの保護設定により、push が失敗したことを確認する。~~  

禁止されたローカルの master からリモートの master への push だったので、git hook として設定した pre-push スクリプトにより、git push が中止されたことを確認する。

## git ログの確認
```console
git log --oneline
```

履歴ログ
＝＝＝＝＝
```console
6a34b60 (HEAD -> master) change the dog to the ape in README.md
cc62efd (origin/master) Merge pull request #3 from fs5013-furi-sutao/feature/add-git-hook-pre-push
6aa1fa2 (origin/feature/add-git-hook-pre-push, feature/add-git-hook-pre-push) add pre-push of git hook
5720fb3 Merge pull request #2 from fs5013-furi-sutao/feature/change-read-me-dog
20be75a (origin/feature/change-read-me-dog, feature/change-read-me-dog) Merge branch 'master' into feature/change-read-me-dog
a3e110b change the human to the dog in README.md
c90c600 Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
e66d12e (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
0cd37f9 first commit
```
＝＝＝＝＝

## コミットの取消（soft モードで変更は残す）
直前のコミットを取り消したいので、上記のログの場合 1 つ前のコミットを指す 4386f2c を指定しても良いが、今回は HEAD を使って、「1つ前のコミット」を指定する。

```console
git reset --soft HEAD^
git log --oneline
```

これでコミットが 1 つ前に戻ったことが確認できる。

履歴ログ
＝＝＝＝＝
```console
cc62efd (HEAD -> master, origin/master) Merge pull request #3 from fs5013-furi-sutao/feature/add-git-hook-pre-push
6aa1fa2 (origin/feature/add-git-hook-pre-push, feature/add-git-hook-pre-push) add pre-push of git hook
5720fb3 Merge pull request #2 from fs5013-furi-sutao/feature/change-read-me-dog
20be75a (origin/feature/change-read-me-dog, feature/change-read-me-dog) Merge branch 'master' into feature/change-read-me-dog
a3e110b change the human to the dog in README.md
c90c600 Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
e66d12e (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
0cd37f9 first commit
```
＝＝＝＝＝

```console
cat ./README.md
```

ところが、ファイルに掛けた「猿」の変更は残っている。

つまり、git reset の `--soft` オプションは、コミットは指定の位置に戻すものの、ファイル自体の変更は残したままにするオプションとなる。

反対に、`--hard` オプションを指定した場合は、ファイル自体の変更も元に戻すこととなる。

ここで master にはコミットできないので、ブランチを切り替えたいのだが、ファイルに変更が掛かった状態になっている。

この変更を一時退避させることにする。

```console
git stash save
```
状況イメージ：
```
local
                                  feature/add-git-hook-pre-push
   feature/change-read-me-dog      H
                  E----F          / \
                 /    /　\       /   \
                /    /    \     /     \
               /    /      \   /       \
              /    /        \ /         \
master A-----B----D----------G-----------I  
              \  /
               C  
               feature/change-read-me-cat  
               
stash@{0} ->J
```

これで変更が退避されたので、退避リストを確認する。

```console
git stash list
```

スタッシュが保存されていることが分かる。

```console
cat ./README.md
git checkout -b feature/change-read-me-ape
git push -u origin feature/change-read-me-ape
git stash apply stash@{0}
```
実行結果イメージ：
```
local
                                  feature/add-git-hook-pre-push
   feature/change-read-me-dog      H
                  E----F          / \
                 /    /　\       /   \
                /    /    \     /     \
               /    /      \   /       \
              /    /        \ /         \
master A-----B----D----------G-----------I  
              \  /                        \
               C                           \
               feature/change-read-me-cat   \
                                             J
stash@{0} ->                                feature/change-read-me-ape
```

このコマンドでスタックの一番上のスタッシュが新規ブランチ上に戻される。

```console
git branch
cat ./README.md
```

「猿」の変更がスタッシュから戻されたことが分かる。

```console
git stash drop stash@{0}
git stash list
```

これで使い終わったスタッシュを削除できた。

```console
git add .
git commit -m 'change the dog to the ape in README.md'
git push
```

これでリモートに push できたので、プルリクエスト、マージまで行う。

```console
git checkout master
git pull
git log --oneline
```
実行結果イメージ：
```
origin:local
               feature/change-read-me-dog  
               B'----E----F 
              /          / \  
master A-----B----------D---G------I  
              \        /     \    /
               B''----C       \  /
  feature/change-read-me-cat   \/
                                H  
                              feature/change-read-me-ape
```

## 今後の特訓 
今回は git status を使わなかったが適宜、git status で状態を確認しながらコミットやブランチの操作を行っていく。  

あとは、このリモートリポジトリをローカルの別の場所に git clone してみよう。  

## リモートリポジトリの削除方法  
対象リモートリポジトリの [Settings] -> [Danger Zone] 欄の [Delete this repository] にある Delete this repository ボタンをクリックする。

クリック後にダイアログが出現するので、フォームに `[GitHub アカウント名/リポジトリ名]` を入力して、「I understand consequences, delete this repository.」ボタンをクリックする。

## ローカルリポジトリの削除方法
ローカルリポジトリ直下にある git オブジェクト（.git フォルダ）を削除すれば、ローカルの git 追跡は無くなる。
.git フォルダ内に、すべてのローカルのバージョン管理の履歴が残っているためである。

## チュートリアルまとめ
以上。  
  
一連の git コマンドのチュートリアルは終わり。  
お疲れ様でした～
