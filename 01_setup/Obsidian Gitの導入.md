2023/03/28
#Obsidian #環境構築 

# Goal
* Obsidian Gitの導入

# Condition

- Obsidianがインストールされていること

# Contents

1. [ここ](https://zenn.dev/ayumukob/articles/3b034fcb6874d2)を参考に進める
2. pushインターバルを適当に設定したところ、push時にエラーが出た
![[Pasted image 20230328223950.png]]
3. エラーを見るとディレクトリパスが違う[らしい](https://ja.stackoverflow.com/questions/57780/git-push%E6%99%82%E3%81%ABerror-failed-to-push-some-refs-to%E3%81%A8%E3%82%A8%E3%83%A9%E3%83%BC%E3%81%8C%E3%81%A7%E3%82%8B)。確かによく見るとパスが"Files/Git~"となっているが、実際はFドライブ直下の99_Git~に保存したい。
4. WSLにてremoteリポジトリのパスを書き換えたところ、上手くいった。
```
okapi@DESKTOP-7EQD042:/mnt/d/41_ObsidianGit/PrivateWorkspace$ git remote set-url origin F:/99_GitRepository/41_ObsidianGit/
```

