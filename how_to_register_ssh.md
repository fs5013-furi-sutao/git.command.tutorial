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

SSH 鍵が格納されているディレクトリに移動する。
```console
cd ~/.ssh
pwd
```
実行結果：
```
/c/Users/[ユーザ名]/.ssh
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

アカウント毎に SSH 鍵を使い分けるため、SSH 設定ファイルに Host（接続元）の登録を行う。

`~/.ssh/config` ファイルの内容:
```
Host github.com.fs5013-furi-sutao  # 1つ目のアカウント
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/rsa_github_fs5013-furi-sutao  # 1つ目のアカウント用の秘密鍵
  TCPKeepAlive yes
  IdentitiesOnly yes

Host github.com.fs5014-furi-sutao  # 2つ目のアカウント
  HostName github.com
  User git
  Port 22
  IdentityFile ~/.ssh/rsa_github_fs5014-furi-sutao　 # 2つ目のアカウント用の秘密鍵
  TCPKeepAlive yes
  IdentitiesOnly yes
```

## GitHub に公開鍵を登録する  
作成した公開鍵を GitHub に登録する。手順は以下の通り。

1. ブラウザを開き、GitHub の対象アカウントから [Settings] -> [SSH and GPG keys] を選択
2. SSH keys の New SSH keyをクリック
3. Title 欄に任意の名前を入力

ここで、Key 欄に公開鍵を貼り付けるので、Git Bash で以下のコマンドを実行する。
```console
clip < ~/.ssh/[ファイル名].pub
```
