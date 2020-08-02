# master への push を禁止するローカル git hook の正しい書き方

## GitHooksとは
ざっくり言うとcommit, pushなどをトリガーに何かアクションを走らせる設定のこと。

## GitHooksの設定共有
デフォルトだとレポジトリ直下の .git/hooks ディレクトリを読み込みに行く。

.git/hooks ディレクトリの中に各hooksファイルとサンプルがあり、これらが実行される。

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
