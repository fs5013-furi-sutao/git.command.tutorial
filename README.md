# Git コマンド チュートリアル  
  
Git コマンドをまだ考えながら打っている方のための Git コマンドチュートリアルです。   

次はこう、次はこう、こうなったら、これ確認してこう・・・と、感覚でバンバン Git コマンドが打てるように、とにかく回数多くサクサクとトレーニングを進めよう。以下のシナリオは実際のチーム開発を想定して、よく使う Git コマンドをひと通り網羅したシナリオです。  
  
section.01 ～ section.03 までシナリオが一連の流れになっています。  
  
## section.01  
[リポジトリの作成からプッシュまで](./section.01.md)  
  
## section.02  
[master ブランチの保護とコンフリクトの解決](./section.02.md)  
  
## section.03  
[誤ったコミットの解決とスタッシュ](./section.03.md)  
  
何度もコマンドを打って、サクサク操作できるようになってね～  
  
# Git 用語チェックリスト  
  
覚えたいキーワードは以下の通り。日々、Git を使いながら、少しずつ理解を深めよう。  

☑ ローカルリポジトリ  
☑ リモートリポジトリ  
☑ ベアリポジトリ  
☑ git clone  
☑ git config(--local user.name, user.email)  
☑ git branch  
☑ git checkout(-b)  
☑ git add  
☑ git commit -m  
☑ git push(-u, --set-upstream)  
☑ git status  
☑ git log(--oneline)  
☑ git stash  
☑ git reset(--soft, --hard)  
☑ git merge  
☑ git fetch  
☑ git pull  
☑ HEAD^  
☑ コンフリクト  
☑ プルリクエスト（GitHub）  
☑ マージリクエスト（GitLab）  
  
# 実際の大規模開発イメージ

大人数により短いサイクルで開発サイクルを回す際のベストプラクティスとして、Git Flow という開発フローが知られている。

以下はそのイメージです。  
[Git Flow イメージと各ブランチの役割](./git_flow.md)  
  
