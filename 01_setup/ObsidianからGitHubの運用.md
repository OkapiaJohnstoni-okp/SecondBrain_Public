---
date: 2023-03-30 10:09:42
aliases: []
---
tags: #2023-03 #GitHub #Obsidian #環境構築 

---
# 0. Overview
## 0.1 Motivation
ObsidianでGitHubを使いたい
## 0.2 Goal
GitHubからリポジトリをプル・プッシュ
## 0.3 Conclusion
Windows上にSSH鍵を作成してObsidian Gitプラグインから管理できた。
privateリポジトリとpublicリポジトリをサブモジュールにより分割できることも確認。

---
# 1. ssh鍵の設定（失敗したので無視でok）

wsl上でssh鍵を作成
```
ssh-keygen -t rsa
```
するとwslのルート直下に.sshディレクトリが出てくる。通常sshではこのディレクトリしか読まないので、別のディレクトリに鍵を移動する場合はconfigファイルの編集が必要
```
Host github github.com
	HostName github.com
	IdentityFile ~/.ssh/id_git_rsa #ここに自分の鍵のファイル名
	User git
```

ただしこの場合、wslでないディレクトリで権限の変更ができず、秘密鍵のアクセス権がガバいと怒られ接続できない
```
okapi@DESKTOP-7EQD042:/mnt/d/.ssh$ ssh -T git@github.com
The authenticity of host 'github.com (20.27.177.113)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,20.27.177.113' (ECDSA) to the list of known hosts.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for '/mnt/d/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/mnt/d/.ssh/id_rsa": bad permissions
git@github.com: Permission denied (publickey).
```

結局、元のwsl上の~/.ssh/id_rsaをアクセス権変更し、使用することに。
```
okapi@DESKTOP-7EQD042:/mnt/d/41_MyWiki$ ssh -T git@github.com
Hi OkapiaJohnstoni-okp! You've successfully authenticated, but GitHub does not provide shell access.
```

### 参考
[GitHubでssh接続する手順\~公開鍵・秘密鍵の生成から\~ - Qiita](https://qiita.com/shizuma/items/2b2f873a0034839e47ce)
[WSLではマウントしたドライブ上のファイルにchmod効かないから気をつけろって話 - Qiita](https://qiita.com/penguinz222/items/8b0f28aade7b3a4e050a)

# 2. Obsidian Gitでリポジトリをプル/プッシュ

## 2.1 問題発生
普通にやろうとしたらエラー。
![[Pasted image 20230330121357.png]]
調べたところ、鍵ではじかれてるみたい。ただ、wsl上からは問題なくプル・プッシュが通る。
→もしかして、wslの.sshをwindowsアプリから参照できない...？

## 2.2 windows上にssh鍵を置く
上の仮説から、Git Bashを使ってwindows上に鍵を置いてみる。
ちなみに、Obsidianでgit操作するときはパスワードを入力できないため、鍵作成のときにパスフレーズは空白にする。
```
$ git config --global user.name "UserName"
$ git config --global user.email "UserEmail"
$ ssh-keygen -t rsa
$ ssh -T git@github.com
Hi OkapiaJohnstoni-okp! You've successfully authenticated, but GitHub does not provide shell access.

```

後はいつも通りリモートリポジトリの設定とプル
```
$ git remote add origin git@github.com:OkapiaJohnstoni-okp/SecondBrain.git
$ git pull
$ git checkout master
```


## 2.3 ObsidianからGitHub
うまくいった。
![[Pasted image 20230330122829.png]]

### 参考
[【Windows】Gitの環境構築をしよう！ | プログラミングの入門なら基礎から学べるProgate[プロゲート]](https://prog-8.com/docs/git-env-win)


# 3. Submoduleの追加

公開/非公開リポジトリを一括管理するために非公開リポジトリ（親）に公開リポジトリ（子）をサブモジュールとして追加する。
![[Pasted image 20230330131432.png|500]]

## 3.1 Git Bashでsubmoduleの追加

1. GitHubでリポジトリ1とリポジトリ2を作成
   ![[Pasted image 20230330151125.png|200]]
   ここではPrivateを親としてリポジトリ1、publicをサブモジュールとしてリポジトリ2とする。

2. リポジトリ1をローカルリポジトリに落とす
	一連の操作。
	```
	$ ls
	 00_inbox/       'Pasted image 20230328223950.png'   XX_Templates/
	 01_DailyNotes/  'Pasted image 20230328224008.png'   YY_Excalidraw/
	```

3. ローカルリポジトリにリポジトリ2をサブモジュールとして追加
	サブモジュールを置くディレクトリを作成し、そこにサブモジュールを紐づける
	```
	$ git submodule add --force git@github.com:OkapiaJohnstoni-okp/SecondBrain_Public.git ./public
	Cloning into 'D:/41_MyWiki/public'...
	remote: Enumerating objects: 3, done.
	remote: Counting objects: 100% (3/3), done.
	remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
	Receiving objects: 100% (3/3), done.
	warning: LF will be replaced by CRLF in .gitmodules.
	The file will have its original line endings in your working directory
	$ git submodule status
	 5dd3b3b04b71f9cb10fa067d521e6ba9a1d2b932 public (heads/main)
	```
4. おしまい。

### 参考
[これならわかる！git submoduleでサブモジュールを追加する方法 | 侍エンジニアブログ](https://www.sejuku.net/blog/73151)
[transitive.info - git submodule 使い方](http://transitive.info/article/git/command/submodule/)