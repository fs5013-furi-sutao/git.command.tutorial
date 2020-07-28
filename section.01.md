# ローカルリポジトリ・リモートリポジトリの作成からプッシュまで

Win キー を押して、`gitbash` と入力して Enter 

※コマンド実行には Powershell を使っても良いが、今回はファイルへの文字列の書き込みを行うため、
文字コードが UTF-8 での出力が容易にできない Powershell を使用しないことにする

Git Bash を起動

```console
pwd
```

現在地がユーザのディレクトリ直下なので、C: ドライブ直下に移動する

```console
cd c: 
pwd
```

C: ドライブ直下に移動できたことを確認する

```ls -a```

C: ドライブ直下のディレクトリ一覧を見て、今から作成するディレクトリ名とかぶる名前のフォルダがないことを確認する

```mkdir ./git.test.dir```

```ls -la```

C: ドライブ直下に git.test.dir フォルダが出来ていることを確認する

`cd ./git` までタイピングし、Tab キーを押すことで  `./git.test.dir` のパスが補完されることを確認する

以降は、タイピングミスを防ぐこと、作業効率性、この 2 つを目的として、なるべく Tab キーでの補完機能を利用するように心掛ける

```echo -e '# git.test.dir  \n  \n私は人間です.  ' >> ./README.md```

```cat ./README.md```

ファイル README.md が作成され、テキストが書き込まれていることを確認する

```git init```
```git add .```
```git commit -m 'first commit'```

グローバルに `git config` ファイルが作られていない場合は、「Please tell me who you are」と怒られる

git はメールアドレスで、コミットした個人を特定しているので、
`git config` に自分が誰かを設定する必要がある

開発では、複数アカウントで git 管理することがあり得るので、
グローバルに `git config` の設定をせずに、ローカルリポジトリごとの範囲で設定するようにする

```git config --local user.name fs5013-furi-sutao```
```git config --local user.email fs5013.furi.sutao@gmail.com```
```git commit -m 'first commit'```

ここで GitHub に以下の設定でリモートリポジトリを作成する
|項目|設定内容|
|:--|:--|
|Repository name: |git.test.dir|
|Description: |git flow のテストです|
|Public: |true|
|Initialize this repository with a README: |none|

本来は SSH キーでの認証が良いが、ここでは説明を簡略化するために、
リモートリポジトリの URI にユーザ情報を含めて、git config に登録する

```git remote add origin https://GitHubユーザ名:パスワード@github.com/GitHubアカウント名/git.test.dir.git```
```cat ./.git/config```

出力：
＝＝＝＝＝
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
        url = https://fs5013-furi-sutao:パスワード@github.com/GitHubアカウント名/git.test.dir.git
        fetch = +refs/heads/*:refs/remotes/origin/*
＝＝＝＝＝

`git config` に登録した `user` と `remote` が反映されていることを確認する

```git push -u origin master```

GitHub の該当リポジトリをブラウザで見て、push が反映されたことを確認する
