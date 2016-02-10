---
layout: post
title: Phoenix Webpack React
date: 2016-02-10 22:11
tags: [Phoenix, React]
---
## Introduction
前回の記事でPhoenix + BrunchでReactをインストールする方法を説明したが、今回はBrunchではなく、Phoenix + WebpackでReactをインストール方法を紹介します。

## Version
Phoenix: v1.1.4

React: v0.14.6

ReactDOM: v0.14.6

## インストール
Webpackのインストール
{% highlight sh %}
npm install --save-dev webpack
{% endhighlight sh %}

Babelのインストール
{% highlight sh %}
npm install --save-dev babel-loader babel-core babel-preset-es2015
{% endhighlight sh %}

Reactのインストール
{% highlight sh %}
npm install --save-dev react react-dom
npm install --save-dev babel-preset-react
{% endhighlight sh %}

CSS/SASSのインストール
{% highlight sh %}
$ npm install --save-dev css-loader style-loader extract-text-webpack-plugin
$ npm install --save-dev bootstrap-sass node-sass sass-loader url-loader file-loader
{% endhighlight sh %}

copy-webpack-pluginインストール
{% highlight sh %}
$ npm install --save-dev copy-webpack-plugin
{% endhighlight sh %}





新しいプロジェクトを作る
{% highlight sh %}
$ mix phoenix.new phoenix_react
$ cd phoenix_react
{% endhighlight sh %}

## Webpackの設定
