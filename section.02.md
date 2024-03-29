# section.02: master ブランチの保護とコンフリクトの解決

**NOTE:**  
> コマンドの実行環境は Windows 10 を想定
> 
> ※チュートリアル中で使われている git コマンド以外のコマンドの説明は以下のファイルを参照  
> [チュートリアル中で使った bash コマンドの説明](./explain_bash_command.md)  
  
## master ブランチを保護する  
```console
git branch
```

`* main` と表示され現在、main ブランチにいることが分かる。

ここで main ブランチの保護設定をしておく。

コンフリクトや不用意なバグの混入を防ぐために、実際の開発では main ブランチへのコミットを禁止している。

~~ただし、禁止ルールというだけでは、間違えて master にコミットしてしまうことは防げないので、以下の手順で master ブランチにコミットできないように、master ブランチの保護設定をしておく。~~

~~GitHub の Settings -> Branches -> Branch protection rules で Add rule ボタンを押下する。~~

~~Branch name pattern に master と入力。Include administrators（管理者を含む）のチェックボックスにはチェックを入れ、Create ボタンを押下することで、すべての push から master ブランチを保護することができる。~~

~~つまり以降、master ブランチへのコミットは不可能となる。~~

NOTE:  
> 2020年7月30日現在、上記取り消し線の内容で、強制的なプッシュ「git push -f」は防げても、通常の「git push origin master」は防げませんでした

代わりに、main への push を開発チームで機械的に防ぐために、git hook を走らせる仕組みを作っておきましょう。  

以下にその方法を説明しているが、このチュートリアルの流れの中でのちほど、この git hook 追加作業を実施することにする。  

現時点では実施しない（後ほど実施）:  
[main への push を禁止する共有 git hook の作成方法](./how_to_protected_from_pushing_origin_master.md)

## 新規 dog ブランチの作成
ここからローカルに戻る。 

```console
git checkout -b feature/change-read-me-dog 
git branch
```
実行結果：
```
* feature/change-read-me-dog
  main
```

状況イメージ：
```
local
             feature/change-read-me-dog  
             B'  
            /  
main A-----B  
```

新規ブランチ `feature/change-read-me-dog` を切って、そのブランチに移動したことが分かる。
master ブランチに戻る。

```console
git checkout main
git branch
```
実行結果：
```
  feature/change-read-me-dog
* main
```

現在地が master ブランチに戻ったことが分かる。

## 新規 cat ブランチの作成
```console
git checkout -b feature/change-read-me-cat
git branch
```
実行結果：
```
* feature/change-read-me-cat
  feature/change-read-me-dog
  main
```

状況イメージ：
```
local
             feature/change-read-me-dog  
             B'  
            /  
main A-----B  
            \  
             B''  
             feature/change-read-me-cat  
```

さらに、新規ブランチ `feature/change-read-me-cat` を切って、そのブランチに移動したことが分かる。ここからは `feature/change-read-me-cat` ブランチ上で作業をする。 

## cat ブランチをリモートへ反映
`git push -u origin feature/change-read-me-c` まで入力し、Tab キーを押すことで、Git Bash ではブランチ名も補完が効くことを確認する。

```console
git push -u origin feature/change-read-me-cat 
```

このブランチを反映するコマンド、`git push -u origin <指定ブランチ>` は、上流に origin （origin は、デフォルトのリモートリポジトリのこと）を指定して、ブランチを反映する。

※ push の `-u` オプションは、`--set-upstream` オプションの略で、git pull する際の上流を指定するオプション 

状況イメージ：
```
origin
  
main A-----B  
            \  
             B''  
             feature/change-read-me-cat  
```
  
`feature/change-read-me-cat` ブランチをリモートに反映できたことを GitHub で確認する。

## cat ブランチ上での変更

```console
cat ./README.md
sed -i 's/人間/猫/' ./README.md
cat ./README.md
```

README.md ファイル中の「人間」部分が「猫」に書き変わったことを確認する。

```console
git add .
git commit -m 'change the human to the cat in README.md'
git push
```
状況イメージ：
```
local
             feature/change-read-me-dog  
             B'  
            /  
main A-----B  
            \  
             C  
             feature/change-read-me-cat  
```
```
origin
  
main A-----B  
            \  
             C  
             feature/change-read-me-cat  
```

GitHub のリポジトリを見て、「Compare & pull request」のボタンが表示されていることを確認する。

ここで「Compare & pull request」ボタンを押下する。

差分が「人間」と「猫」部分であることを確認、マージ内容が「base:master <- compare:feature/change-read-me-cat」で Able to merge（マージ可能）となっていることを確認する。

コメント欄に「猫にしたので、コードレビューをお願いします」と入力し、Create pull request ボタンを押下する。

ここで `change the human to the cat in README.md` のコミットをクリック。

コードの差分を確認、「私は猫です」の行の「+」を押下して、出現するコメント欄に「猫への変更 OK です」と入力し Start a review ボタンを押下する。

Finish your review ボタンを押下し、Comment のラジオボタンが選択されていることを確認して、Submit review ボタンを押下。

画面でレビューが完了したことを確認して、Merge pull request ボタンを押下。

マージコメント欄に「change the human to the cat in README.md」が自動入力されたことを確認し、Confirm merge ボタンを押下する。

これでリモートでのマージが完了。
  
状況イメージ：
```
origin
  
main A-----B----D
            \  /
             C  
             feature/change-read-me-cat  
```
  
上部の Pull requests タブを押すと、`0 Open 1 Closed` となっていることを確認し、`1 Closed` をクリックする。

ここにプルリクエストの履歴が残っていることを確認する。

リポジトリのトップページを見て、README.md の内容が「猫」に変わっていることを確認するここからローカルに戻る。

## マージ結果を master に pull する  

```console
git branch
```
実行結果：
```
* feature/change-read-me-cat
  feature/change-read-me-dog
  main
```

`feature/change-read-me-cat` ブランチにいることを確認する。

```console
git checkout master 
git branch
```
実行結果：
```
  feature/change-read-me-cat
  feature/change-read-me-dog
* main
```

```console
cat ./README.md
```

ローカルの master ブランチはまだ、README.md ファイルの内容が「人間」となっていることを確認する。

```console
git pull
cat ./README.md
```
実行結果：
```
# git.test.dir

私は猫です.
```
状況イメージ：
```
local
             feature/change-read-me-dog  
             B'  
            /  
main A-----B----D
            \  /
             C  
             feature/change-read-me-cat  
```

pull によって、リモートでマージされた内容がローカルリポジトリに反映され、README.md の内容が「猫」になっていることを確認する。

## dog ブランチをリモートに登録

```console
git checkout feature/change-read-me-dog
git branch
```
実行結果：
```
  feature/change-read-me-cat
* feature/change-read-me-dog
  main
```

`feature/change-read-me-dog` ブランチに移動したことを確認する。

```console
git branch -a
```
実行結果：
```
  feature/change-read-me-cat
* feature/change-read-me-dog
  main
  remotes/origin/feature/change-read-me-cat
  remotes/origin/main
```

リモートにはまだ、`feature/change-read-me-dog` が反映されていないことを確認する。

```console
git push -u origin feature/change-read-me-dog  
git branch -a
```
実行結果：
```
  feature/change-read-me-cat
* feature/change-read-me-dog
  main
  remotes/origin/feature/change-read-me-cat
  remotes/origin/feature/change-read-me-dog
  remotes/origin/main
```

状況イメージ：
```
origin
             feature/change-read-me-dog  
             B'  
            /  
main A-----B----D
            \  /
             C  
             feature/change-read-me-cat  
```

## dog ブランチ上での変更
ブランチがリモートに反映できたことを確認する。

```console
cat ./README.md
```

main が「猫」でマージされる前にブランチを切ったので、まだ内容が「人間」となっていることを確認する。

```console
sed -i 's/人間/犬/' ./README.md
cat ./README.md
```

「犬」に書き変わったことを確認する。

## GitHub 上でのコンフリクト
```console
git add .
git commit -m 'change the human to the dog in README.md'
git push
```
状況イメージ：
```
origin
             feature/change-read-me-dog  
                E
               / 
              /   
             /     
            /       
main A-----B----D
            \  /
             C  
             feature/change-read-me-cat  
```
  
GitHub のリポジトリを見て、「Compare & pull request」のボタンが表示されていることを確認するここで「Compare & pull request」ボタンを押下。
  
状況イメージ：
```
origin
             feature/change-read-me-dog  
                E
               / \
              /   \
             /     \
            /       \
main A-----B----D--- Can’t automatically merge. 
            \  /
             C  
             feature/change-read-me-cat  
```

Can’t automatically merge. となっていて、自動マージができないことを確認する。

原因は、「人間」を「猫」に書き換えたものと「犬」に書き換えたものが同一箇所にあり、どちらを反映したら良いか自動判定できない状態になっていることです。

一度ローカルに戻る。

## master を取り込んでから競合を解決する
```console
git branch
```
実行結果：
```
  feature/change-read-me-cat
* feature/change-read-me-dog
  main
```

`feature/change-read-me-dog` ブランチにいることが分かる。

```console
git merge main 
```
実行結果：
```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```
日本語訳：
```
README.md を自動マージ 
内容がコンフリクトしています： README.md のマージで競合
自動マージに失敗しました。コンフリクトを修正してから結果をコミットしてください。
```
状況イメージ：
```
local
               feature/change-read-me-dog  
                E----@ CONFLICT.Automatic merge failed.
               /    /
              /    /
             /    /  
            /    /    
main A-----B----D 
            \  /
             C  
             feature/change-read-me-cat  
```

main ブランチの内容を `feature/change-read-me-dog` ブランチにマージした（取り込んだ）が、もちろん内容が競合（コンフリクト）した状態になる。

VSCode で README.md ファイルを見てみる。

コンフリクト部分：
＝＝＝＝＝
```console
<<<<<<< HEAD
私は犬です.  
=======
私は猫です.  
>>>>>>> main
```
＝＝＝＝＝

確かに機械的にどちらの変更が正しいかは自動的には判定できない。

では今回は、開発の要件上、後で変更した「犬」が最終的な変更とすることにする。

そこで VSCode 上で、コンフリクト部分に出現している Accept Current Change ボタンを押下して、最新の変更を採用し、ファイルを保存する。

![VSCode 上でコンフリクト部分に出現している Accept Current Change ボタン](./image/watch_conflict_with_vscode.png)  

```console
git add ./README.md
git commit
```

vi エディタが開くので、コンフリクトのコミットメッセージが出力されていることを画面で確認できれば、「:q」と入力して、コンフリクトのコミットメッセージをそのまま生かしてコミットを完了させる。

vi の表示内容（コミットメッセージ）:
```
Merge branch 'master' into feature/change-read-me-dog

# Conflicts:
#       README.md
#
# It looks like you may be committing a merge.
# If this is not correct, please remove the file
#       .git/MERGE_HEAD
# and try again.


# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch feature/change-read-me-dog
# Your branch is up to date with 'origin/feature/change-read-me-dog'.
#
# All conflicts fixed but you are still merging.
#
```

リモートに push する。

```console
git push
```
状況イメージ：
```
origin:local
             feature/change-read-me-dog  
                E----F 
               /    /
              /    /
             /    /  
            /    /    
main A-----B----D 
            \  /
             C  
             feature/change-read-me-cat  
```

これで GitHub を見て、コミットが Able to merge になったことを確認する。  
  
プルリクエスト画面では、コメント欄に「コンフリクトを解決し、『犬』に変更したのでレビューをお願いします」と入力して Create pull request ボタンを押下する。  
  
変更内容が「犬」であることを確認し、Merge pull request ボタンを押下。  
  
マージコメント欄に「Feature/change read me dog」が自動入力されたことを確認し、Confirm merge ボタンを押下する。  
  
状況イメージ：
```
origin
             feature/change-read-me-dog  
                E----F 
               /    /　\
              /    /    \
             /    /      \
            /    /        \
main A-----B----D----------G 
            \  /
             C  
             feature/change-read-me-cat  
```
  
リポジトリのトップページを見て、README.md の内容が「犬」に変わっていることを確認する。  
ここからローカルに戻る。  
  
最終章 section.03 につづく・・・  
[section.03: 誤ったコミットの解決とスタッシュ ](section.03.md)  
  
