---
layout: post
title: SentryでElixir(Phoenix)のエラーログを収集
---
## Sentryとは
リアルタイムなerror trackingサービス、Production環境でエラーが起こる場合、Sentryに投げて、Sentry側でエラーログを管理することができます。official Elixir SDKを提供しています。

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

これだけで、エラーが起こるとき、自動的にSentryに送ります。