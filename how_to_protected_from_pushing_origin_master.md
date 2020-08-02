# master への push を禁止する共有 git hook の作成方法  

## GitHooksとは
ざっくり言うと commit, push などをトリガーに何かアクションを走らせる設定のこと。

## GitHooksの設定共有
デフォルトだとレポジトリ直下の `.git/hooks` ディレクトリを読み込みに行く。

`.git/hooks` ディレクトリの中に各 hooks ファイルとサンプルがあり、これらが実行される。

```console
ls -la ./.git/hooks/
```
実行結果:
```
total 49
drwxr-xr-x 1 natsuki 197121    0  8月  2 23:30 ./
drwxr-xr-x 1 natsuki 197121    0  8月  2 23:33 ../
-rwxr-xr-x 1 natsuki 197121  478  8月  2 23:30 applypatch-msg.sample*
-rwxr-xr-x 1 natsuki 197121  896  8月  2 23:30 commit-msg.sample*
-rwxr-xr-x 1 natsuki 197121 4655  8月  2 23:30 fsmonitor-watchman.sample*
-rwxr-xr-x 1 natsuki 197121  189  8月  2 23:30 post-update.sample*
-rwxr-xr-x 1 natsuki 197121  424  8月  2 23:30 pre-applypatch.sample*
-rwxr-xr-x 1 natsuki 197121 1643  8月  2 23:30 pre-commit.sample*
-rwxr-xr-x 1 natsuki 197121  416  8月  2 23:30 pre-merge-commit.sample*
-rwxr-xr-x 1 natsuki 197121 1492  8月  2 23:30 prepare-commit-msg.sample*
-rwxr-xr-x 1 natsuki 197121 1348  8月  2 23:30 pre-push.sample*
-rwxr-xr-x 1 natsuki 197121 4898  8月  2 23:30 pre-rebase.sample*
-rwxr-xr-x 1 natsuki 197121  544  8月  2 23:30 pre-receive.sample*
-rwxr-xr-x 1 natsuki 197121 3635  8月  2 23:30 update.sample*
```

ところが、通常 `.git` ディレクトリは GitHub で共有されない。

そこで読み込みに行くディレクトリを変更する。

まずは、ディレクトリを作成する前に、git hook 追加作業用のブランチを切っておく（状況に応じて、適切にブランチを切っておく）。
```console
git checkout -b feature/add-git-hook-pre-push
git push -u origin feature/add-git-hook-pre-push
```

hooks ファイルを格納するディレクトリとして、レポジトリ直下に `.githooks` というディレクトリを作る。

```console
mkdir ./.githooks
```

この中に必要な hooks ファイルを作成する。今回は push の前にコマンドを走らせたいので、`pre-push` としてファイルを作る。

```console
touch ./.githooks/pre-push
```

この `.githooks/pre-push` ファイルを読み込むようにローカルの config ファイルに登録しておく。

```console
git config --local core.hooksPath .githooks
cat ./.git/config
```
実行結果:
```console
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
        hooksPath = .githooks
[user]
        name = fs5013-furi-sutao
        email = fs5013.furi.sutao@gmail.com
```

Windows 10 の場合は、これで push をすると、pre-pushに書いてあるスクリプトが実行される。

## pre-pushの設定
pre-push hook を以下のような感じにする。
```bash
#!/bin/bash

while read local_ref local_sha1 remote_ref remote_sha1
do
  if [[ "${remote_ref##refs/heads/}" = "master" ]]; then
    echo 'master branch への push は禁止です. Do not push to master branch!!!' 
    echo 'pull request ベースの開発フローを厳守してください.'
    exit 1
  fi
done
```

## 変更の add, commit, push, pull request, merge, pull
git hook を追加した変更をリモートへの反映まで済ませる。
```console
git add .
git commit -m 'add pre-push of git hook'
git push
```

GitHub では、プルリクエストを作成し、master へのマージまで済ませる。

最後に master ブランチに切り替え、リモートのマージも取り込んでおく。
```console
git checkout master
git pull
```
## git push が禁止できているかをチェック
git push origin master を防御できているか確認してみる。

試しに `aa.txt` というファイルを作成する。
```console
touch ./aa.txt
```

add, commit, push まで行う。
```console
git add .
git commit -m 'add aa.txt'
git push
```
実行結果:
```
master branch への push は禁止です. Do not push to master branch!!!
pull request ベースの開発フローを厳守してください.
error: failed to push some refs to 'git@github.com.fs5013-furi-sutao:fs5013-furi-sutao/git.test.dir.git'
```

ここでの目的である git hook が効いていることが確認できた。

コミットログを確認する。
```console
git log --oneline
```
実行結果:
```
2de97e5 (HEAD -> master) add aa.txt
cc62efd (origin/master) Merge pull request #3 from fs5013-furi-sutao/feature/add-git-hook-pre-push
6aa1fa2 (origin/feature/add-git-hook-pre-push, feature/add-git-hook-pre-push) add pre-push of git hook
5720fb3 Merge pull request #2 from fs5013-furi-sutao/feature/change-read-me-dog
20be75a (origin/feature/change-read-me-dog, feature/change-read-me-dog) Merge branch 'master' into feature/change-read-me-dog
a3e110b change the human to the dog in README.md
c90c600 Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
e66d12e (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
0cd37f9 first commit
```

状態を1つ前のコミットに戻し、変更も消去させておく。
```console
git reset --hard HEAD^
git log --oneline
```
実行結果:
```
cc62efd (HEAD -> master, origin/master) Merge pull request #3 from fs5013-furi-sutao/feature/add-git-hook-pre-push
6aa1fa2 (origin/feature/add-git-hook-pre-push, feature/add-git-hook-pre-push) add pre-push of git hook
5720fb3 Merge pull request #2 from fs5013-furi-sutao/feature/change-read-me-dog
20be75a (origin/feature/change-read-me-dog, feature/change-read-me-dog) Merge branch 'master' into feature/change-read-me-dog
a3e110b change the human to the dog in README.md
c90c600 Merge pull request #1 from fs5013-furi-sutao/feature/change-read-me-cat
e66d12e (origin/feature/change-read-me-cat, feature/change-read-me-cat) change the human to the cat in README.md
0cd37f9 first commit
```

`git reset` により、`aa.txt` ファイル追加のコミットがなくなっていることが確認できる。

```console
ls -la
```
実行結果:
```
total 21
drwxr-xr-x 1 natsuki 197121  0  8月  3 01:53 ./
drwxr-xr-x 1 natsuki 197121  0  8月  3 01:29 ../
drwxr-xr-x 1 natsuki 197121  0  8月  3 01:53 .git/
drwxr-xr-x 1 natsuki 197121  0  8月  3 01:42 .githooks/
-rw-r--r-- 1 natsuki 197121 42  8月  3 01:38 README.md
```

git reset の `--hard` オプションによって、直近の変更の消去までできたことも確認できた。
  
以上で、git hook の追加・確認の作業は、完了となる。  
  
