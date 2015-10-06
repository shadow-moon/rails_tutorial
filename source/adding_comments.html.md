---
title: コメントを投稿できるようにしよう
---

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


