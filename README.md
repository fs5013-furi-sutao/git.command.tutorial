# Git コマンド チュートリアル  
  
Git コマンドをまだ考えながら打っている方のための Git コマンドチュートリアル。   

次はこう、次はこう、こうなったら、これ確認してこう・・・と、感覚でバンバン Git コマンドが打てるように、とにかく回数多くサクサクとトレーニングを進める。以下のシナリオでは、実際のチーム開発を想定して、よく使う Git コマンドをひと通り網羅している。  
  
section.01 ～ section.03 までシナリオが一連の流れとなっている。  
  
## section.01  
[リポジトリの作成からプッシュまで](./section.01.md)  
  
## section.02  
[master ブランチの保護とコンフリクトの解決](./section.02.md)  
  
## section.03  
[誤ったコミットの解決とスタッシュ](./section.03.md)  
  
何度もコマンドを打って、サクサク操作できるようになってね～  

### その他: 補足事項  
[config ファイルにエイリアスを登録する方法](./how_register_arias_to_config.md)
  
# Git 用語チェックリスト  
  
覚えたいキーワードは以下の通り。日々、Git を使いながら、少しずつ理解を深めよう。  

- [x] ローカルリポジトリ  
- [x] リモートリポジトリ  
- [x] ベアリポジトリ  
- [x] git clone  
- [x] git config(--local user.name, user.email)  
- [x] git branch  
- [x] git checkout(-b)  
- [x] git add  
- [x] git commit -m  
- [x] git push(-u, --set-upstream)  
- [x] git status  
- [x] git log(--oneline)  
- [x] git stash  
- [x] git reset(--soft, --hard)  
- [x] git merge  
- [x] git fetch  
- [x] git pull  
- [x] HEAD^  
- [x] コンフリクト  
- [x] プルリクエスト（GitHub）  
- [x] マージリクエスト（GitLab）  
  
# 実際の大規模開発イメージ

大人数により短いサイクルで開発サイクルを回す際のベストプラクティスとして、Git Flow という開発フローが知られている。

以下はそのイメージである。  
[Git Flow イメージと各ブランチの役割](./git_flow.md)  
  
