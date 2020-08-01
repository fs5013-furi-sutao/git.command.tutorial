# Git Bashで GitHub に SSH 接続する設定（複数アカウント版）
実際の開発で起こりえることとして、GitHub のアカウント（複数の SSH 鍵）を使い分ける場合がある。

ここでは、

## SSH 鍵の作成
使用したいアカウント毎に、以下のコマンドで秘密鍵と公開鍵のペアを作成する。

実行コマンドの書式:
```console
ssh-keygen -t [暗号方式] -b [ビット長] -f ~/.ssh/[ファイル名]
```
例:
```console
ssh-keygen -t rsa -b 4096 -f ~/.ssh/rsa_github_fs5013-furi-sutao
```

パスフレーズを 2 回聞かれるので、2 回ともに同じ「設定したいパスワード」を入力する。
```console
Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

2 回入力したパスフレーズが一致していたら、鍵の作成が実行される。
実行結果：
```
Your identification has been saved in /c/Users/natsuki/.ssh/rsa_github_fs5013-furi-sutao
Your public key has been saved in /c/Users/natsuki/.ssh/rsa_github_fs5013-furi-sutao.pub
The key fingerprint is:
SHA256:M+j8SIEPKNS66YkGF5kv+c4qGhZfB2VMoRXNbOcs7Jw natsuki@MyComputer
The key's randomart image is:
+---[RSA 4096]----+
|      oB*        |
|  .   =. = .     |
| . + o  o +      |
|. =. ... o o     |
|.o.+o.o.S o      |
|..O o=.. E       |
|o= +  =          |
|=o.... o         |
|=+.oo . .        |
+----[SHA256]-----+
```


秘密鍵と公開鍵ができていることを確認する。

```console
ls -la
```
実行結果：
```
-rw-r--r-- 1 natsuki 197121 3434  8月  2 05:28 rsa_github_fs5013-furi-sutao
-rw-r--r-- 1 natsuki 197121  744  8月  2 05:28 rsa_github_fs5013-furi-sutao.pub
```

## SSH 設定ファイルの編集
SSH 接続の設定ファイルである `~/.ssh/config` を編集する。
