---
title: "複数バージョンのCUDAを管理するオレオレ環境構築"
date: 2023-10-20T23:59:17+09:00
# weight: 1
# aliases: ["/first"]
categories: ["Python"]
tags: ["Python", "pyenv", "conda", "cuda"]
author: "Me"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: "大昔の公開されているコードを動かす際や、最新のコードを動かす際には求められるcudaバージョンが違ったりします。今回はcondaを使って複数バージョンのcudaを管理するオレオレPython環境構築法を紹介します。"
canonicalURL: "https://canonical.url/to/page"
disableShare: false
disableHLJS: false
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
    URL: "https://github.com/<path_to_repo>/content"
    Text: "Suggest Changes" # edit text
    appendFilePath: true # to append file path to Edit link
---

前提として、私の場合は`pyenv`上に`conda`環境を作る方式で長い間、環境管理をしてきました。
できれば`conda`を使いたくないプロジェクトの場合には、`pyenv`上で`pip`のみで構築する環境を作る、みたいなことをすれば`pip`環境と`conda`環境を容易に使い分けることができます。
今回紹介するのは`conda`を使った複数バージョンの`cuda`の管理方法です。

## 環境
* OS: Debian 12(WSL2)
* GPU: NVIDIA GTX4060Ti 16GB

## 依存関係のインストール
```bash
sudo apt update; sudo apt install build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev curl \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

## pyenv のインストール

```bash
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```
`~/.bashrc`にパスを通しておく。

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

以下のコマンドでバージョンが出てくればOK。
```bash
pyenv version
```

pyenv上で仮想環境を作ることもあるので、`pyenv-virtualenv`もインストールしておく。
```bash
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```
`~/.bashrc`にパスを通しておく。
```bash
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
source ~/.bashrc
```

## miniconda on pyenv
以下コマンドでインストール可能な`Python`バージョンが一覧表示される。
```bash
pyenv install --list
```
今回は`miniconda3-latest`をインストールする。
```bash
pyenv install miniconda3-latest
```
基本的に`miniconda3-latest`を使うので、
```bash
pyenv global miniconda3-latest
```
を実行することで、グローバルで使用するバージョン（環境）を`conda`に指定する。

もう`conda`しか使わないよって猛者は
```bash
conda init
```
しておくとよいかも。

## cuda-toolkit のインストール
`conda`を使って仮想環境を作る。

`Python`は`3.11`、`cuda`は`11.8`の環境を作るため、環境名は`py311cu118`としておく。
これは自身で判別できるものなら何でもよい。
```bash
conda create -n py311cu118 python=3.11
conda activate py311cu118
```

作った環境をアクティベートした上で
```bash
conda install -c "nvidia/label/cuda-11.8.0" cuda-toolkit
```
を実行。`y`を入力することで、インストールが進む。

他の`cuda`バージョンをインストールする場合は[こちら](https://anaconda.org/nvidia/cuda-toolkit)を参照して、入れたいバージョンのインストールコマンドを実行する。

これで、`Python==3.11`に`cuda==11.8`を入れた環境ができた。

## cudaバージョンの切り替え
上記の方法でいくつか環境ができている状態であれば、
```bash
conda activate [環境名]
```
をすれば自動で`cuda`バージョンが切り替わる。

きちんとバージョンが切り替わっているか確認するには、
```bash
nvcc -V
```
で出てきた出力を確認すればよい。

## 最後に
`Ubuntu`/`Debian`を使っていると`apt update alternatives`みたいなものを使ってcudaのバージョンを切り替えることもできます。

ただ、`conda`や`pyenv`などを使い慣れている人にとっては、環境を切り替えると自動で使うcudaバージョンが切り替わるのは便利です。

`conda`で`cuda`を入れる場合には、[cuda-toolkit](https://anaconda.org/nvidia/cuda-toolkit)と[cudatoolkit](https://anaconda.org/anaconda/cudatoolkit)の選択肢が存在する。

前者は`nvcc`なども含む、いわばフルパッケージ的なもので後者は、`pytorch`などで最低限必要なライブラリを入れるもの。

個人的には前者が好き、というのも、`nvcc`など動かせないと`ninja`でのビルドなどに失敗することもあるため。

詳しくは延べませんでしたが、私の場合は`pip`のみで環境を作りたいこともあったりするので、`conda`の下に`pyenv`/`pyenv-virtualenv`を敷く形で構築しています。

---

## Reference
* https://github.com/pyenv/pyenv
* https://github.com/pyenv/pyenv-virtualenv
* https://anaconda.org/nvidia/cuda-toolkit
* https://anaconda.org/anaconda/cudatoolkit