

---
date: 2023-03-30 07:45:40
aliases: []
---
tags: #2023-03, #Obsidian 

---
# 1. 概要

## 1.1 Motivation

先日から使い始めたObsidianだが、結構良さげなので運用について考える。
というのも、秘密文書の切り分けが大変そうだったのでローカルに構成したは良いものの家からアクセスできずに不便だったからである。

## 1.2. Goal

- [x] 秘密文書を含めたObsidianのオンライン運用について検討
- [x] 実装

## 1.3. Summarization

# 2. オンラインストレージサービスの選択

自作サーバーも手だが、手間なのでオンラインストレージサービス（dropbox, onedrive, githubなど）を使うことにした。

| Requirements               | GitHub                              | OneDrive                                                      |
| -------------------------- | ----------------------------------- | ------------------------------------------------------------- |
| 同期できるか？             | O                                   | O                                                             |
| バージョン管理可能？       | O（元々の機能）                     | O（ドライブ内にローカルリポジトリとリモートリポジトリを作成） |
| 外部へ一部を公開できるか？ | O （submodule使ってそれのみを公開） | N                                                             |
| 使い勝手                   | N（あんまり使ったことない）         | O（いつも通り）                                                              |

[Gitで一部ディレクトリをprivate化する | mktia's note](https://blog.mktia.com/usage-of-git-submodule/)
[Obsidianの複数端末同期方法まとめ (Mac/Windows/iOS/Android)](https://pouhon.net/obsidian-sync/6796/)

公開を考慮して、GitHubを使うことに。（SubModuleの設定やPagesの使い方が全くわからんが、なんとかなるやろ）
