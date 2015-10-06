---
title: The introduction to Ruby on Rails
---

# The introduction to Ruby on Rail


{:toc}


## ここで学ぶこと

* 動くウェブアプリケーションを作成


## ここで学ばないこと

* Railsについて


## やること

* 簡易Blogアプリを作る。


## 簡易Blogアプリできること

* 記事を投稿できる。
* 記事にコメントを投稿できる。


## 開発環境

Ruby
: v2.2.3

Rails
: v4.2.4 

OS
: Unix系


## Railsをインストール

    $ gem install rails
    $ rails -v
    Rails 4.2.4


## Rails アプリを新規作成

    $ rails new my_blog


## 実行時の出力

アプリケーションの雛形を作成

          create  README.rdoc
          create  Rakefile
          create  config.ru
          create  .gitignore
          create  Gemfile
          create  app
          create  app/assets/javascripts/application.js
          create  app/assets/stylesheets/application.css
          ...
          create  vendor/assets/stylesheets/.keep


## 実行時の出力

ライブラリパッケージのインストール

             run  bundle install
    Fetching gem metadata from https://rubygems.org/............
    Fetching version metadata from https://rubygems.org/...
    Fetching dependency metadata from https://rubygems.org/..
    Resolving dependencies.......
    Using rake 10.4.2
    Using i18n 0.7.0
    Using json 1.8.3
    ....


## 記事の投稿画面を作る。

`scaffold` コマンド:
モデル、コントローラー、ビューを一気に作る。


## 使い方

    $ ./bin/rails generate scaffold モデル名 [column:type ...]


## 記事の投稿画面を作る。

    $ ./bin/rails generate scaffold post title:string body:text


## 実行時の出力

      invoke  active_record
      create    db/migrate/20150927155025_create_posts.rb
      create    app/models/post.rb
      invoke    test_unit
      create      test/models/post_test.rb
      create      test/fixtures/posts.yml
      invoke  resource_route
       route    resources :posts
      invoke  scaffold_controller
      create    app/controllers/posts_controller.rb
      invoke    erb
      create      app/views/posts
      create      app/views/posts/index.html.erb
      create      app/views/posts/edit.html.erb
      create      app/views/posts/show.html.erb
      create      app/views/posts/new.html.erb
      create      app/views/posts/_form.html.erb
      invoke    test_unit
      create      test/controllers/posts_controller_test.rb
      invoke    helper
      create      app/helpers/posts_helper.rb
      invoke      test_unit
      invoke    jbuilder
      create      app/views/posts/index.json.jbuilder
      create      app/views/posts/show.json.jbuilder
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/posts.coffee
      invoke    scss
      create      app/assets/stylesheets/posts.scss
      invoke  scss
      create    app/assets/stylesheets/scaffolds.scss


## Railsの理念

Convention over Configuration
: 設定より規約。

DRY( Don't Repeat Yourself)
: 同じことを繰り返さない。

## CoC

ディレクトリ毎に意味が決まっている。

    app/models/post.rb  # => モデル
    app/controllers/posts_controller.rb  # => コントローラー
    app/views/posts/  # => ビュー


## データベースの準備

    $ ./bin/rake db:create   # => DBを作成
    $ ./bin/rake db:migrate  # => テーブルを作成


## Railsアプリケーションを起動

    $ ./bin/rails server


## アクセスしてみよう

ブラウザから `http://localhost:3000/posts` でアクセス可能。


# どう動いているか

設定ではなく、規約どおりに動く。

* パスが`posts` なので、`PostController#index` だ！
* `PostController#index` のテンプレートは、 `app/views/posts/index.html.erb` だ！

## routing 

  アプリケーションが受け付けるURLを定義

    Rails.application.routes.draw do
      resources :posts
{: lang="ruby" }

## RESTful な定義

Resource(ここではPost) を操作するURLの規約

|     Request         | Controller#action |
|---------------------+-------------------|
| GET /posts          | posts#index   |
| POST /posts         | posts#create  |
| GET /posts/:id      | posts#show    |
| GET /posts/:id/edit | posts#edit    | 
| PUT /posts/:id      | posts#update  |
| DELETE /posts       | posts#destroy |


## actionの役割

| Action | 役割 |
|--------+------|
| index | Resourceの一覧を表示 |
| create | 新規Resourceを作成 |
| show | idを持つResource の表示 |
| edit | idを持つResourceの編集画面 |
| update | idを持つResourceを更新 |
| destroy | idを持つResourceを削除 |

## まとめると

|     Request         | 役割 |
|---------------------+-------------------|
| GET /posts          | 一覧   |
| POST /posts         | 新規作成  |
| GET /posts/:id      | 個別に表示    |
| GET /posts/:id/edit | 編集画面    | 
| PUT /posts/:id      | 更新  |
| DELETE /posts       | 削除 |


----

# コメントを投稿できるようにしよう

上では単に記事を投稿できるだけであった。
今度は、個別にモデルとコントローラーを作成し、記事ごとにコメントを投稿できるようにする。

## Commentモデルを作る

`generator model` コマンドで、モデルを作成。

    $ ./bin/rails g model comment email:string body:text post:belongs_to timestamp


## CommentsController を作る

`generator controller` コマンドで、モデルを作成。
今回は、コメントを投稿（作成）できれば良いので、必要なアクションは`create`のみ。

    $ ./bin/rails g controller comments create


## Routing にコメント投稿用のパスを追加

`config/routes.rb` を開き、下記のように編集。

    resources :posts do
      resources :comments, only: [:create]
    end

`resources` をネストさせることで、特定の記事に限定したコメントを追加するためのURLを定義できる。具体的には、

    POST /posts/:id/comments

を受け付けるようになる。

## Association: PostとCommentを結びつける

ブログの場合、記事は複数のコメントを持つことができる。
このように、モデル間の関係を定義をAssociationと良い、主に

* 一対一
* 一対多
* 多対多

の関係がある。これを定義するための記法をRailsは提供している。
ブログの場合、記事1つに対して複数のコメントなので一対多の関係を持っている。
それを定義するために、`belogns_to` と `has_many` を使う。

具体的には、 `app/models/post.rb` を下記のように編集し、

    class Post < ActiveRecord::Base
      has_many :comments
    end

`app/models/comment.rb` も下記のように編集する。

    class Comment < ActiveRecord::Base
      belogns_to :post
    end


