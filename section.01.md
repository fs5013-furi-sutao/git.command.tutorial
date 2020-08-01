# section.01: リポジトリの作成からプッシュまで  
 
**NOTE:**  
> コマンドの実行環境は Windows 10 を想定
> 
> ※チュートリアル中で使われている git コマンド以外のコマンドの説明は以下のファイルを参照  
> [チュートリアル中で使った bash コマンドの説明](./explain_bash_command.md)  
  
## Git Bash の起動  
Win キー を押して、`gitbash` と入力して Enter キーを押下する。  
  
**NOTE:**  
> コマンド実行には Powershell を使っても良いが、今回はファイルへの文字列の書き込みを行うため、文字コードが UTF-8 での出力が容易にできない Powershell を使用しないことにする

Git Bash が起動する。  
  
## 作業フォルダの作成  
```console
pwd
```

現在地がユーザのディレクトリ直下なので、C: ドライブ直下に移動する。

```console
cd c: 
pwd
```

C: ドライブ直下に移動できたことを確認する。

```console
ls -a
find -maxdepth 1 -type d -name '[a-g]*'
```

C: ドライブ直下のディレクトリ一覧を見て、今から作成するディレクトリ名とかぶる名前のフォルダがないことを確認する。

```console
mkdir ./git.test.dir
```

これで C: ドライブ直下に `git.test.dir` フォルダが出来ているはずなので、再度カレントディレクトリを確認したい。
先ほど打った find コマンドを打ちたいが、再度、長いオプションを記述するのは生産性が悪い。

そこでコンソールのコマンド履歴の機能を使う。
カーソルキーの「↑」ボタンを押すと、自身が実行したコマンドの履歴が [スタック](https://ja.wikipedia.org/wiki/スタック) 状に表示されるはず。

直前の履歴の find コマンドを表示させて実行する。

```console
find -maxdepth 1 -type d -name '[a-g]*'
```

続いて今、作成した `git.test.dir` フォルダ内に移動したい。
`cd ./git` までタイピングし、Tab キーを押すことで  `./git.test.dir` のパスが補完されることを確認する。

```console
cd ./git.test.dir/
```

以降は、タイピングミスを防ぐこと、作業効率性、この 2 つを目的として、なるべく Tab キーでの補完機能を利用するように心掛ける。
  
## master ブランチへのコミット、プッシュまで  
  
### 新規ファイル README.md の作成  
```console
echo -e '# git.test.dir  \n  \n私は人間です.  ' > ./README.md
cat ./README.md
```
実行結果：
```
# git.test.dir

私は人間です.

```

ファイル README.md が作成され、テキストが書き込まれていることを確認する。
  
### ローカルリポジトリの初期化 / ステージング / コミット失敗  
```console
git init
git add .
git commit -m 'first commit'
```

グローバルに `git config` ファイルが作られていない場合は、「Please tell me who you are」と怒られる。

git はメールアドレスによりコミットした個人を特定しているので、`git config` に自分が誰かを設定する必要がある。

開発では、複数アカウントで git 管理することがあり得るので、グローバルに `git config` の設定をせずに、ローカルリポジトリごとの範囲で設定するようにする。
  
### ローカル config にユーザ設定
```console
git config --local user.name <ユーザ名>
git config --local user.email <メールアドレス>
```
例）：
```console
git config --local user.name fs5013-furi-sutao
git config --local user.email fs5013.furi.sutao@gmail.com
```
  
git を使い慣れてきたら、git のログを見やすくする設定もしておきましょう。

git のログを見やすくするには、長い記述のオプションを打つ必要が出てきてしまいます。
毎回長いオプションを打たなくても、短いコマンドで済むように、コマンドにエイリアス（別名）を登録する機能が git にある。
[config ファイルにエイリアスを登録する方法](./how_register_arias_to_config.md)

### master ブランチへの初回コミット

では改めて、初回コミットを行う。

```console
git commit -m 'first commit'
```
  
### GitHub リポジトリの作成  
ここで GitHub に以下の設定でリモートリポジトリを作成する。

|項目|設定内容|
|:--|:--|
|Repository name: |git.test.dir|
|Description: |git flow のテストです|
|Public: |true|
|Initialize this repository with a README: |none|

### config ファイルへのリモート origin の追加  
**セキュリティ上、本来は SSH キーでの認証がベストだが、** ここでは説明を簡略化するために、リモートリポジトリの URI にユーザ情報を含めて、git config に登録する。

```console
git remote add origin https://<GitHubユーザ名>:<パスワード>@github.com/<GitHubアカウント名>/<リポジトリ名>.git
cat ./.git/config
```
例）：
```console
git remote add origin https://fs5013-furi-sutao:D#8h5k!b@github.com/fs5013-furi-sutao/git.test.dir.git
cat ./.git/config
```

origin というのが、デフォルトのリモートリポジトリの名前になる。origin 

出力：
＝＝＝＝＝
```console
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[user]
        name = fs5013-furi-sutao
        email = fs5013.furi.sutao@gmail.com
[remote "origin"]
        url = https://<ユーザ名>:<パスワード>@github.com/<GitHubアカウント名>/git.test.dir.git
        fetch = +refs/heads/*:refs/remotes/origin/*
```
＝＝＝＝＝

`git config` に登録した `user` と `remote` が反映されていることを確認する。

ローカルリポジトリの config へのパスワードの記載は、セキュリティの観点上、良くないので、コマンド類や仕組みの扱いに慣れてきたら SSH 鍵での認証を行うようにする。

[](./how_to_register_ssh.md)
  
### リモートリポジトリへのプッシュ  
```console
git push -u origin master
```

GitHub の該当リポジトリをブラウザで見て、push が反映されたことを確認する。

section.02 につづく・・・  
[section.02: master ブランチの保護とコンフリクトの解決](section.02.md)  
  
