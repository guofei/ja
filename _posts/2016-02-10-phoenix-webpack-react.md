---
layout: post
title: Phoenix Webpack React
date: 2016-02-10 22:11
categories: [Phoenix, React]
---
## Introduction
前回の記事でPhoenix + BrunchでReactをインストールする方法を説明したが、今回はBrunchではなく、Phoenix + WebpackでReactをインストール方法を紹介します。

## Version
Phoenix: v1.1.4

React: v0.14.7

ReactDOM: v0.14.7

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

## 新しいプロジェクトを作る
{% highlight sh %}
$ mix phoenix.new phoenix_react
$ cd phoenix_react
$ rm brunch-config.js
{% endhighlight sh %}

## 設定ファイル
package.jsonに
{% highlight js %}
    "phoenix": "file:deps/phoenix",
    "phoenix_html": "file:deps/phoenix_html",
{% endhighlight js %}
と
{% highlight js %}
  "scripts": {
    "compile": "webpack -p"
  }
{% endhighlight js %}
を追加、完成したファイルは以下のようになる：
{% highlight js %}
{
  "repository": {},
  "dependencies": {
    "phoenix": "file:deps/phoenix",
    "phoenix_html": "file:deps/phoenix_html",
  },
  "devDependencies": {
    "babel-core": "^6.5.1",
    "babel-loader": "^6.2.2",
    "babel-preset-es2015": "^6.5.0",
    "babel-preset-react": "^6.5.0",
    "bootstrap-sass": "^3.3.6",
    "copy-webpack-plugin": "^1.1.1",
    "css-loader": "^0.23.1",
    "extract-text-webpack-plugin": "^1.0.1",
    "file-loader": "^0.8.5",
    "node-sass": "^3.4.2",
    "react": "^0.14.7",
    "react-dom": "^0.14.7",
    "sass-loader": "^3.1.2",
    "style-loader": "^0.13.0",
    "url-loader": "^0.5.7",
    "webpack": "^1.12.12"
  },
  "scripts": {
    "compile": "webpack -p"
  }
}
{% endhighlight js %}

config/dev.exsにwatchersの部分を以下のように変更：
{% highlight exs %}
watchers: [node: ["node_modules/webpack/bin/webpack.js",
                   "--watch-stdin", "--progress", "--colors"]]
{% endhighlight exs %}

webpack.config.js
{% highlight js %}
var ExtractTextPlugin = require('extract-text-webpack-plugin');
var CopyWebpackPlugin = require('copy-webpack-plugin');
module.exports = {
  devtool: 'source-map',
  entry: [
    './web/static/js/app.js',
    './web/static/css/app.scss',
  ],
  output: {
    path: './priv/static',
    filename: 'js/app.js',
  },

  resolve: {
    modulesDirectories: [
      './web/static/js',
      './node_modules',
    ],
    extensions: ['', '.js', '.jsx'],
  },

  module: {
    loaders: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'react'],
        },
      },
      {
        test: /\.jsx$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'react'],
        },
      },
      {
        test: /\.css$/,
        loader: ExtractTextPlugin.extract('style', 'css'),
      },
      {
        test: /\.scss$/,
        loader: ExtractTextPlugin.extract('style', 'css!sass'),
      }
    ],
  },

  plugins: [
    new ExtractTextPlugin('css/app.css'),
    new CopyWebpackPlugin([{ from: './web/static/assets' }]),
  ],
};
{% endhighlight js %}

## cssの設定
web/static/js/app.jsの中に、以下のコードを追加：
{% highlight js %}
require('../css/app.scss');
{% endhighlight js %}


web/static/css/app.cssのファイル名をapp.scssに変更

app.scssの中身を以下のように修正：
{% highlight scss %}
$icon-font-path: "~bootstrap-sass/assets/fonts/bootstrap/";
@import "~bootstrap-sass/assets/stylesheets/_bootstrap.scss";
{% endhighlight scss %}


