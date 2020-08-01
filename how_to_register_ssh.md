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

`~/.ssh` に `config` ファイルがない場合は、次のコマンドで作成する。
```console
touch ./config
```

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

1.ブラウザを開き、GitHub の対象アカウントから [Settings] -> [SSH and GPG keys] を選択
2.SSH keys の New SSH keyをクリック
3.Title 欄に任意の名前を入力

ここで、Key 欄に公開鍵を貼り付けるので、Git Bash で以下のコマンドを実行する。
```console
clip < ~/.ssh/[ファイル名].pub
```

これでクリップボードに公開鍵がコピーされる。

ブラウザに戻り、Key 欄に公開鍵をペーストする。ペーストしたら、Add SSH key で保存。これで、GitHub の設定は終わり。

## ssh-agent 起動設定
clone や push の度に秘密鍵のパスフレーズを聞かれるのを省略するために `~/.bashrc` に設定を追記する。

`~/.bashrc` ファイルがない場合は新規作成する。
```console
touch ~/.bashrc
```

`~/.bashrc` には以下の記述をする。
```
#ssh-agent
eval $(ssh-agent -s)
ssh-add ~/.ssh/~/[ファイル名]
```

~/.bashrc は、Git Bash 起動時のみ実行されるファイル。

~/.bashrc ファイルに上記の記述をすることで、Git Bash 起動時に ssh-agent を起動し、ssh-agent リストに対象の秘密鍵を追加する。
追加時には、対象の秘密鍵のパスフレーズを聞かれる。

## ログアウト時の ssh-agent の終了設定
Git Bash を閉じた時に、`~/.bashrc` で起動した ssh-agent を終了させたい。

それには、`~/.bash_logout` ファイルへの記述が必要となる。

`~/.bash_logout` ファイルがない場合は新規作成する。
```console
touch ~/.bash_logout
```

`~/.bash_logout` には以下の記述をする。
```
ssh-agent -k
```

## ~/.bashrc の読み込み確認
Git Bash の起動時に、先ほど設定した `~/.bashrc` の設定が読み込まれ、ssh-agent に対象の秘密鍵が追加されるかを確認する。

Git Bash の起動時にパスフレーズを聞かれたら、設定、および読み込みが成功。

起動時のパスフレーズ要求:
```
Agent pid [プロセスID]
Enter passphrase for /c/Users/natsuki/.ssh/[対象秘密鍵のファイル名]:
```

パスフレーズを入力し、認証が成功すると以下のメッセージが表示される。

秘密鍵追加成功時のメッセージ:
```
Identity added: /c/Users/natsuki/.ssh/rsa_github_fs5013-furi-sutao (natsuki@MyComputer)
```

## SSH 接続できるかを確認
ssh コマンドで GitHub に SSH 接続できるかを確認する。

```console
ssh -T git@[~/.ssh/config ファイルに設定した Host 名]
```

接続が成功すれば、以下のメッセージが表示される。
```
Hi [ユーザ名]! You've successfully authenticated, but GitHub does not provide shell access.
```

## ログアウト時に ssh-agent がログアウトできているかを確認する
Git Bash の終了時に、`~/.bash_logout` が読み込まれ、先ほど記述した `ssh-agent kill` スクリプトが実行されていれば、以下のメッセージが表示される。
```
logout
unset SSH_AUTH_SOCK;
unset SSH_AGENT_PID;
echo Agent pid 2420 killed;
```

が、メッセージ表示後、瞬時にコンソールが閉じられるので、メッセージを確認するのは難しい。

Git Bash を一度、exit させてから、再度 Git Bash を起動させて、ssh-agent の起動、秘密鍵の追加が求められることを確認する。

## 終わりに
以上で、GitHub 複数アカウントの SSH 設定、確認は完了となる。  
  
