---
layout: post
title: PhoenixとbrunchでReact.jsを使う
date: 2016-01-25 19:11
tags: [Phoenix, React]
---
## Introduction
PhoenixとReact.jsを一緒に使ってみたいので、Phoenix＋Reactをインストールしました。今回はwebpackではなく、PhoenixのデフォルトのBrunchを使います。

## version
Phoenix: v1.1.3

React: v0.14.6

ReactDOM: v0.14.6

## インストール

Phoenixをインストール
{% highlight sh %}
$ mix local.hex
$ mix archive.install https://github.com/phoenixframework/archives/raw/master/phoenix_new.ez
{% endhighlight sh %}

新しいプロジェクトを作る
{% highlight sh %}
$ mix phoenix.new phoenix_react
$ cd phoenix_react
{% endhighlight sh %}

Reactをインストール
{% highlight sh %}
$ npm install --save react react-dom
$ npm install --save-dev babel-preset-react
{% endhighlight sh %}

## Branchの設定
brunch-config.javascriptを開いて、
{% highlight javascript %}
plugins: {
  babel: {
    // Do not use ES6 compiler in vendor code
    ignore: [/web\/static\/vendor/]
  }
},
{% endhighlight javascript %}
を以下のように修正
{% highlight javascript %}
plugins: {
  babel: {
    presets: ["es2015", "react"],
    // Do not use ES6 compiler in vendor code
    ignore: [/web\/static\/vendor/]
  }
},
{% endhighlight javascript %}

## Reactのコードを入れて確認する
web/templates/page/index.html.eexを開いて、以下のコードを追加
{% highlight html %}
<div id="hello-world"></div>
{% endhighlight html %}

web/static/javascript/app.javascriptに以下のコードを追加
{% highlight javascript %}
import React from "react"
import ReactDOM from "react-dom"

ReactDOM.render(
  <h1>Hello World!</h1>,
  document.getElementById("hello-world")
)
{% endhighlight javascript %}


