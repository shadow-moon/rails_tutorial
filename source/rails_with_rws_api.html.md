---
title: 楽天ウェブサービスAPIを使ったアプリケーションを作る
---

# 楽天ウェブサービスAPIを使ったアプリケーションを作る

* TOC
{:toc}

[楽天ウェブサービスAPI](http://webservice.rakuten.co.jp/)とは、楽天が提供するAPI郡です。
これをRubyから利用するためのSDKとして、[`rakuten_web_service`](https://github.com/rakuten-ws/rws-ruby-sdk)がある。
今回は、このgemを使い、キーワードにマッチする商品を一覧表示するRailsアプリケーションを作ります。


## Railsアプリケーションの雛形を作ろう

~~~
  $ rails new rws_rails
~~~


## `rakuten_web_service` をインストール

Railsはbundlerというライブラリ依存解決ツールを利用し、アプリケーションで利用するライブラリ(gem)の管理を行う。
アプリケーションで利用するgem（とバージョン）の一覧を`Gemfile`というファイルに記載する。

ここでは、`rakuten_web_service` を使うので、下記の1行を`Gemfile` に追記します。

~~~ruby
  gem 'rakuten_web_service'
~~~


## 楽天ウェブサービスAPIを利用する準備をしよう

楽天ウェブサービスAPIを利用するには、事前に[アプリケーションの登録](https://webservice.rakuten.co.jp/app/create)を行う必要があり、
登録後に発行されるアプリケーションIDをライブラリに設定しなければならない。

## Railsの起動時に楽天ウェブサービスAPIを初期化する

Railsを起動した時に、先ほど取得したアプリケーションAPIをライブラリに設定できるように、初期化スクリプトを書く。

`config/initializers` 下に、Rubyファイルを置くと、Railsは起動時（`./bin/rails server` 実行時）にそれらを事前に実行してくれる。
今回は、`rakuten_web_service` のアプリケーションIDを設定するためのクラス `RakutenWebService.configuration` メソッドを使う。

~~~ruby
  RakutenWebService.configuration do |c|
    c.application_id = 'YOUR_APPLICATION_ID'
  end
~~~

`config/initializers/rws.rb` として保存する。

`YOUR_APPLICATION_ID` は実際のアプリケーションIDに置き換えてください。


## 商品を検索するコントローラーを作る

`rails generator` コマンドで雛形を作る。

~~~
  $ ./bin/rails g controller items index
~~~


~~~ruby
class ItemsController < ApplicationController
  def index
    @items = RWS::Ichiba::Item.
      search(keyword: params[:keyword], imageFlag: 1).first(10)
  end
end
~~~


## 一覧を表示する画面

`app/views/items/index.html.erb` を下記のように編集する。

~~~eruby
<h1>商品一覧</h1>

<%= form_tag(items_path, method: :get) do %>
  <%= search_field_tag :keyword %>
  <%= submit_tag '検索' %>
<% end %>

<table>
  <thead>
  <tr>
    <th></th>
    <th>商品名</th>
    <th>価格</th>
    <th>説明</th>
  </tr>
  </thead>
  <tbody>
  <% @items.each do |item| %>
    <tr>
      <td><%= image_tag item.medium_image_urls[0]['imageUrl'] %></td>
      <td><%= item.name %>
      <td><%= item.price %>
      <td><%= item.caption %>
    </tr>
  <% end %>
  </tbody>
</table>

~~~

## routing を設定

~~~ruby
Rails.application.routes.draw do
  # get 'items/index'
  resources :items, only: [:index]
~~~


## 課題

### お気に入り機能を作ろう。

検索した結果をデータベースに保存し、お気に入り一覧を作ってみよう。


### 見た目をかっこよくしよう。

[`twitter-bootstrap-rails`](https://github.com/seyhunak/twitter-bootstrap-rails) というgemがあり、Bootstrapというデザインフレームワークを利用して、見た目をかっこよくしてみよう。


