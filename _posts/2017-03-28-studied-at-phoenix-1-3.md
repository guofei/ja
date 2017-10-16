---
layout: post
title: Phoenix 1.3：boundaryを意識しての開発
categories: [Phoenix, Programming]
---
Phoenix 1.3ではBoundaryという概念がすごく強調されました。その結果として、ディレクトリの構成が変わりました。

PhoenixはもともとRailsを真似して作ったので、model，controller，view，templateのようなディレクトリ構成になっていました。しかし、この構成だと、ディレクトリ構成を見たら、これはrailsのアプリケーションだ、あるいはこれはSpring/Hibernateのアプリケーションだと変わるが、これはどんなアプリケーションか（チャットアプリケーションやアカウントアプリケーションなど）がわかりません。
詳しくはこbobおじさんの[Screaming Architecture](https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html)を参考してください。

この構成だと、web interfaceを中心として考えるので、開発が進むと、boundaryがはっきり分別できなりなります。例えば、業務のロジックとウェブのロジックを混ぜってしまいがちです。さらに、model間のロジックもだんだん混ぜってしまい、お互いの詳細を知ることになります。controllerもmodelの詳細を理解しないとうまく処理できません。

この問題を解決するため、Phoenix 1.3はディレクトリの構成が変更されました。

以下は変更前のディレクトリ構成
{% highlight sh %}
my_app
└── web
    ├── models
    ├── channels
    ├── controllers
    ├── templates
    └── views
{% endhighlight sh %}

以下は変更後のディレクトリ構成
{% highlight sh %}
lib/my_app
├── accounts
│   ├── user.ex
│   └── accounts.ex
├── blogs
│   ├── post.ex
│   ├── comment.ex
│   └── blogs.ex
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

変更後、modelsディレクトリがなくなり、代わりに、webの外で機能ごとにディレクトリが作られます。一見そんなに大きな変更ではないが、考え方が大きく変わります。

それは、開発の時、どんなmodelを作るのではなく、どんな機能をどう分けるかを先に考えます。つまり、`boundary`を常に先に考えます。例えば、blog機能を追加したい場合、blogsというディレクトリを作って、その下にpostとcommentというものを作ります。このpostとcommentのpublic APIをblogs.exで用意すれば、詳細をsalesやweb/controllersにバレる必要がありません。Elixir/Phoenixでは、blogs.exのことをcontextと言います。blogs.exの中に、Blog.create、Blog.create_comment、Blog.list_commentsのようなAPIが定義されています。controllerで直接のAPIを呼びます。APIの中で、データベースを使っているのか、RPCを読んでいるのかを関心する必要がありません。

さらに、blog記事にlikeするような機能を追加するとき、LikeやReactionのようなモデルだけ追加して、controllerでよぶのではなく、blogsにlikeを追加して、Blog.likeで呼んだ方が良いのか、reactions contextを追加して、Reaction.likeで読んだほうが良いのかを考える必要があります。つまり、自然に`boundary`を考えることになります。

この考え方はmicro serviceとかなり近いですね。

