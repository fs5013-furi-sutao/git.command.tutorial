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

まずは hooks ファイルを格納するディレクトリとして、レポジトリ直下に `.githooks` というディレクトリを作る。

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

`.githooks/pre-push` ファイルの権限を確認する。
```console
ls -la ./.githooks/pre-push
```
実行結果:
```
-rw-r--r-- 1 natsuki 197121 0  8月  2 23:50 ./.githooks/pre-push
```

`.githooks/pre-push` ファイルに実行権限を与える
```console
chmod a+x ./.githooks/pre-push
ls -la ./.githooks/pre-push
```
実行結果:
```
-rw-r--r-- 1 natsuki 197121 0  8月  2 23:50 ./.githooks/pre-push
```

これでpushをすると、pre-pushに書いてあるスクリプトが実行される。

## pre-pushの設定
pre-push hook を以下のような感じにする。
```bash
#!/bin/bash

while read local_ref local_sha1 remote_ref remote_sha1
do
  if [[ "${remote_ref##refs/heads/}" = "master" ]]; then
    echo 'master branch への push は禁止です !!!' 
    echo 'pull request ベースの開発フローを厳守してください.'
    exit 1
  fi
done
```