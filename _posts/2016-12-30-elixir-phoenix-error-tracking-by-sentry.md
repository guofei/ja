---
layout: post
title: SentryでElixir(Phoenix)のエラーログを収集
categories: [Elixir]
---
## Sentryとは
リアルタイムなerror trackingサービス、Production環境でエラーが起こる場合、Sentryに投げて、Sentry側でエラーログを管理することができます。official Elixir SDKを提供しています。

## サービスへの登録
[https://sentry.io](https://sentry.io)

sentry自体は[オープンソース](https://github.com/getsentry/sentry)なので、外部サービスを使いたない場合、自分のサーバーに入れることができます。

## インストール
mix.exs
{% highlight elixir %}
defp application do
  [applications: [:sentry, :logger]]
end

defp deps do
  [{:sentry, "~> 2.0.2"}]
end
{% endhighlight elixir %}

config.exs
{% highlight elixir %}
config :sentry,
  dsn: "https://yourdsn",
  use_error_logger: true
{% endhighlight elixir %}

{% highlight sh %}
$ mix.deps.get
{% endhighlight sh %}

これだけで、インストールが完了です。エラーが起こるとき、自動的にSentryに送ります。

