## Git 環境の作成方法

Windows 10 での Git 環境の作成方法を説明する。

### Winget のインストール

Window で最も手軽に Git をインストールするには Winget を使う。

Winget のインストール方法はこちら:  
https://github.com/fs5013-furi-sutao/how-to-introduce-winget

### Git のインストール

PowerShell などで以下のコマンドを使って Git をインストールする

``` console
winget install -e --id Git.Git
```

### Windows Terminal のインストール

Windows で GitBash(Git コマンドを実行するコンソール)を使うには、Windows Terminal を利用することをお勧めする。

なぜなら、標準の GitBash は画面が貧弱だからである。

PowerShell などで以下のコマンドを使って Windows Terminal をインストールする

``` console
winget install -e --id Microsoft.WindowsTerminal
```

### Git Bash を Windows Terminal に追加する

Windows Terminal への GitBash の追加方法は以下を参考にする。

Git Bash を Windows Terminal に追加する:  
https://github.com/fs5013-furi-sutao/explain.how_to_add_git_bash.into_windows_terminal

ただし、上記の説明だと、パス設定が Scoop で Git をインストールにした場合のパスになっているので、
Winget で Git をインストールした場合は、以下のようにパス部分だけをファイルを参照できるように修正する。

``` json
{
    "commandline" : "\"C:\\Program Files\\Git\\bin\\bash.exe\" --login -i -l",
    "icon" : "C:\\Program Files\\Git\\mingw64\\share\\git\\git-for-windows.ico",
}
```

### 動作確認

Windows Terminal で Git が使えるか確認しよう。

``` console
git --version
```

結果:  
```
git version 2.33.1.windows.1
```
