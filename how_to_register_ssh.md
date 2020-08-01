# Git Bashで GitHub に SSH 接続する設定（複数アカウント版）
実際の開発で起こりえることとして、GitHub のアカウント（複数の SSH 鍵）を使い分ける場合がある。

ここでは、

## 

```console
ssh-keygen -t rsa -b 4096 -f ~/.ssh/[filename]
```
例:
```console
ssh-keygen -t rsa -b 4096 -f ~/.ssh/rsa_github_fs5013-furi-sutao
```
