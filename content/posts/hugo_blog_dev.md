---
title: HugoとGithub Pagesで爆速ブログ開発
date: 2023-10-20T00:00:44+09:00
# weight: 1
# aliases: ["/first"]
categories: ["Go lang"]
tags: ["Go", "Programming", "Git"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "HugoとGithub Pagesを使って爆速でブログを公開するまでの手順"
canonicalURL: "https://canonical.url/to/page"
disableHLJS: false # to disable highlightjs
disableShare: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
UseHugoToc: true
ShowCodeCopyButtons: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
editPost:
    URL: "https://github.com/hirakawat-hmp/portfolio/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

静的サイトジェネレータである`Hugo`とホスティングサービスである`Github Pages`を使うことで、爆速でブログを構築して公開する。ターミナル操作に慣れていればおそらく5分もかからない。

## 環境
* Debian 12 (WSL2)

## 準備
`Hugo`をインストール
```bash
sudo apt update
sudo apt install hugo
hugo version
```

## サイト作成
以下コマンドで`example_site`という名前のプロジェクトフォルダを作成
```bash
hugo new site example_site
```
ここで、一度`git`リポジトリにプッシュしておく。
```bash
cd example_site
git init
git add .
git commit -m "init repo"
git branch -M main
git remote add origin git@github.com:[user-name]/[repository-name].git
git push -u origin main
```

## テーマを入れる
MITライセンス、かつ柔軟にサイトの見た目を変更できそうな、[`PaperMod`](https://github.com/adityatelange/hugo-PaperMod)というテーマを使用。

`example_site`配下で
```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```
基本的には`example_site/config.yml`を編集することでサイトの基本的な設定が可能。

[こちら](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation)のWikiの`config.yml`をベースに編集するとやりやすいかも。

下記のコマンドでローカルでサイトの見た目等、プレビューすることが可能。
```bash
hugo server
```
`ctrl + c`で実行を中断することが可能。

## 記事を書く
以下コマンドで`example_site/content/posts/first_article.md`が生成される。
このマークダウンが記事本体となる。
```bash
hugo new --kind post posts/first_article.md
```
[こちら](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation#sample-pagemd)のマークダウンを参考にマークダウンを編集する。
```bash
hugo server
```
でサーバーを立ち上げ、記事の見た目を見ながら編集していくことが可能。

## Github Pages にデプロイ
[こちら](https://gohugo.io/hosting-and-deployment/hosting-on-github/)を参考に`Github Pages`にデプロイできる。

注意すべき点として、`.github/workflows/hugo.yaml`中の`HUGO_VERSION`を手元のマシンのターミナル上で
```bash
hugo version
```
した際に表示されるバージョンに書き換える必要がある。

## 最後に
以上で、このサイト同様のサイトを作成することが可能。

最短で5分もかからない気がする。

テーマは、[公式サイト](https://themes.gohugo.io/)上で様々公開されているので、各テーマの`Installation`などを参考に様々なテーマでサイトが作れる。
カスタマイズ方法に関しても各テーマで異なるため、適宜テーマのドキュメントを参考にしながらカスタマイズする。

`layouts/`配下にあるテンプレートを変更することで、細かな見た目の変更が可能。
