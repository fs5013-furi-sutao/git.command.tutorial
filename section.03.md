# コミットの解決とスタッシュ 

```console
git branch
```

`feature/change-read-me-dog` ブランチにいるので、master にスイッチする

```console
echo -e '# git.test.dir  \n  \n私は猿です.  ' > ./README.md
cat ./README.md
```

「猿」に書き変わったことを確認する

```console
git add .
git commit -m 'change the dog to the ape in README.md'
git push
```

禁止されたローカルの master からリモートの master への push だったので、GitHub で行ったブランチの保護設定により、push が失敗したことを確認する

```console
git log --oneline
```

履歴ログ
＝＝＝＝＝
```console
e4efe2f (HEAD -> master) change the dog to the ape in README.md
4386f2c Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
fa19500 (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
554ce21 first commit
```
＝＝＝＝＝

直前のコミットを取り消したいので、上記のログの場合 1 つ前のコミットを指す 4386f2c を指定しても良いが、今回は HEAD を使って、「1つ前のコミット」を指定する

```console
git reset --soft HEAD^
git log --oneline
```

これでコミットが 1 つ前に戻ったことが確認できる

履歴ログ
＝＝＝＝＝
```console
4386f2c (HEAD -> master) Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
fa19500 (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
554ce21 first commit
```
＝＝＝＝＝

```console
cat ./README.md
```

ところが、ファイルに掛けた「猿」の変更は残っている

つまり、git reset の `--soft` オプションは、コミットは指定の位置に戻すものの、ファイル自体の変更は残したままにするオプションとなる

反対に、`--hard` オプションを指定した場合は、ファイル自体の変更も元に戻すこととなる

ここで master にはコミットできないので、ブランチを切り替えたいのだが、
ファイルに変更が掛かった状態になっている

この変更を一時退避させることにする

```console
git stash save
```

これで変更が退避されたので、退避リストを確認する

```console
git stash list
```

スタッシュが保存されていることが分かる

```
cat ./README.md
git checkout -b feature/change-read-me-ape
git push -u origin feature/change-read-me-ape
git stash apply stash@{0}
```

このコマンドでスタックの一番上のスタッシュが新規ブランチ上に戻される

```console
cat ./README.md
```

「猿」の変更がスタッシュから戻されたことが分かる

```console
git stash drop stash@{0}
git stash list
```

これで使い終わったスタッシュを削除できた

```console
git add .
git commit -m 'change the dog to the ape in README.md'
git push
```

これでリモートに push できたので、プルリクエスト、マージまで行う

```console
git checkout master
git pull
git log --oneline
```

今回は git status を使わなかったが適宜、git status で状態を確認しながら
コミットやブランチの操作を行っていく  

あとは、このリポジトリを git clone してみよう。  
  
以上。  
  
