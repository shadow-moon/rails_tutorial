# 開発用VMの準備

`rbevn`を利用した`ruby`のインストールを実行できる環境の作成手順について説明する。

## VirtualBox のインストール

[VirtualBoxの公式サイト](https://www.virtualbox.org/)からインストーラーをダウンロードし、インストールを実行してください。

## Vagrant のインストール

[Vagrantの公式サイト]()からインストーラーをダウンロードし、インストールを実行してください。

## Vagrantfile をダウンロード

`git` コマンドを用いて、このリポジトリをチェックアウトします。

```
git clone https://github.com/shadow-moon/rails_tutorial.git
cd rails_tutorial/vagrant
```

または、[githubからzipでダウンロード](https://github.com/shadow-moon/rails_tutorial/archive/master.zip)し、展開します。


## `vagrant` コマンドを使いVMを作る

チェックアウトまたはzipを展開したフォルダに移動し、`vagrant` コマンドを使いVMを作成します。

```
cd rails_tutorial/vagrant
vagrant run
```

## rbenvとruby のインストール

[事前準備](https://github.com/shadow-moon/rails_tutorial/issues/1) の手順を参照。

