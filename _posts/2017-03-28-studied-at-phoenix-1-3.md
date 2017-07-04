---
layout: post
title: Phoenix 1.3：boundaryを意識しての開発
categories: [Phoenix, Programming]
---
Phoenix 1.3ではBoundaryという概念がすごく強調されました。その結果として、ディレクトリの構成が変わりました。

PhoenixはもともとRailsを真似して作ったので、model，controller，view，templateのようなディレクトリ構成になっていました。しかし、この構成だと、ディレクトリ構成を見たら、これはrailsのアプリケーションだ、あるいはこれはSpring/Hibernateのアプリケーションだと変わるが、これはどんなアプリケーションか（チャットアプリケーションやアカウントアプリケーションなど）がわかりません。
詳しくはこbobおじさんの[Screaming Architecture](https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html)を参考してください。

そのため、Phoenix 1.3はディレクトリの構成が変更されました。

変更前
{% highlight sh %}
my_app
└── web
    ├── models
    ├── channels
    ├── controllers
    ├── templates
    └── views
{% endhighlight sh %}

変更後
{% highlight sh %}
lib/my_app
├── accounts
│   ├── user.ex
│   └── accounts.ex
├── sales
│   ├── ticket.ex
│   ├── manager.ex
│   └── sales.ex
├── repo.ex
└── web
    ├── channels
    ├── controllers
    ├── templates
    └── views
{% endhighlight sh %}

TODO


