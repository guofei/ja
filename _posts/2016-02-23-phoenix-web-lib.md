---
layout: post
title: Phoenixのwebとlibの違い
categories: [Elixir, Phoenix]
---
Programming Phoenixという本を買って読んだら、webとlibの理解が間違ったことをわかりました。

railsでアプリを開発するとき、自作のクラスやモジュールをlibに入れるのが一般的です。Phoenixでウェブアプリを開発する時も、libとwebの二つのディレクトリーがあります。しかし、railsと同じように自作のクラスをlibに入れるのは間違いです。

> Lots of people have asked me what goes in each directory, expecting some complicated answer. It's really quite simple. When you have code reloading turned on, the code in web will be reloaded, and the code in lib won't, making lib the perfect place to put long-running services, like Phoenix's PubSub system, the database connection pool or your own supervised processes. Excerpt From: Chris McCord, Bruce Tate, and José Valim. Programming Phoenix.

webの中のコードがreloadされて、libの中のコードがreloadされない。そのため、webディレクトリーに入れるべきものはrequestするたびに変わるものです。libの中に入れるべきものは状態を保持するものです（例えば、GenServerやAgentなど）。


