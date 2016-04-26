---
layout: post
title: PhoenixのGettextの多言語化対応I18nのやり方
---
## RailsなどのI18nのやり方
Railsのアプリケーションを多言語化する場合、keyと定義する必要があります。翻訳文を探すとき、keyを与えて、翻訳文が返されます。例えば、config/locales/ja.ymlファイルで
{% highlight yml %}
ja:
  view:
    welcome: 本サイトへようこそ
{% endhighlight yml %}
のように定義して、

view側で
{% highlight rb %}
I18n.t 'view.welcome'
{% endhighlight rb %}
を呼ぶと、ちゃんと"本サイトへようこそ"が返されます。

## PhoenixとGettext
Phoenixの場合はrailsのやりかとと少し異なります。Phoenixのアプリケーションを英語化や多言語化するとき、デフォルトではGettexを使います。Gettextはrailsのi18nと違って、keyを自分で定義する必要がありません。もしrailsのようlに使いたいなら、linguistというパケッジを入れる必要があります。

## Gettextの使い方
### gettext()関数を入れる
まず、多言語化したいところにgettext関数を入れる

例えば、テンプレート側で
{% highlight eex %}
<%= "welcome" %>
{% endhighlight eex %}
があります。

このwelcomeを日本語化したい場合、以下のように修正する必要があります。
{% highlight eex %}
<%= gettext("welcome") %>
{% endhighlight eex %}

### potファイルを生成する
次は、gettextのテンプレートファイルを自動作成します。以下のコマンドを実行すると、
{% highlight sh %}
$ mix gettext.extract
{% endhighlight sh %}
priv/gettext/default.potというテンプレートファイルが作成されます(potはPortable Object Template略)。

ファイルの中身は以下のような感じです。
{% highlight txt %}
#: web/views/page_view.ex:2
msgid "welcome"
msgstr ""
{% endhighlight txt %}

### poファイルを生成する
次は、テンプレートファイルからPOファイルを生成します。以下のコマンドを実行すると、
{% highlight sh %}
$ mix gettext.merge priv/gettext
{% endhighlight sh %}

初めて日本語のPOファイルを作りたい場合は
{% highlight sh %}
$ mix gettext.merge priv/gettext --locale ja
{% endhighlight sh %}
を実行する必要があります。

上のコマンドを実行すると、/priv/gettext/ja/LC_MESSAGES/default.poなどのファイルが生成されます。
ファイルの中身は以下のような感じです。
{% highlight txt %}
#: web/views/page_view.ex:2
msgid "welcome"
msgstr ""
{% endhighlight txt %}

### poファイルを手動で修正
/priv/gettext/ja/LC_MESSAGES/default.poファイルを以下のように日本語の翻訳を追加、他の言語の場合は/priv/gettext/xxx/LC_MESSAGES/default.poを修正
{% highlight txt %}
#: web/views/page_view.ex:2
msgid "welcome"
msgstr "ようこそ"
{% endhighlight txt %}

### plugを追加する
多国言語の翻訳ファイルを追加した後、localeの設定が必要です。ここではplugを利用してlocaleの設定を行います。

サポートする言語をconfig/config.exsに追加します。
{% highlight ex %}
config :myapp, Myapp.Gettext,
  locales: ~w(en ja)
{% endhighlight ex %}

web/controllers/locale.exというファイルを追加します。ファイルの中身は以下のようになります。
{% highlight ex %}
defmodule Myapp.Locale do
  import Plug.Conn

  def init(_default), do: nil

  def call(conn, default) do
    case extract_param_locale(conn) do
      nil ->
        locale = List.first(extract_locale(conn)) || default
        set_locale conn, locale
      locale ->
        set_locale conn, locale
    end
  end

  defp set_locale(conn, locale) when is_nil(locale) do
    conn
  end

  defp set_locale(conn, locale) do
    Gettext.put_locale(Myapp.Gettext, locale)
    conn |> put_session(:locale, locale)
  end

  defp extract_param_locale(conn) do
    case conn.params["locale"] do
      nil ->
        case get_session(conn, :locale) do
          nil ->
            nil
          locale ->
            locale_check locale
        end
      locale ->
        locale_check locale
    end
  end

  defp locale_check(locale) do
    if Enum.member?(Myapp.Gettext.supported_locales, locale) do
      locale
    else
      nil
    end
  end

  defp extract_locale(conn) do
    # Filter for only known locales
    extract_accept_language(conn)
    |> Enum.filter(fn locale -> Enum.member?(Myapp.Gettext.supported_locales, locale) end)
  end

  defp extract_accept_language(conn) do
    case conn |> get_req_header("accept-language") do
      [value|_] ->
        value
        |> String.split(",")
        |> Enum.map(&parse_language_option/1)
        |> Enum.sort(&(&1.quality > &2.quality))
        |> Enum.map(&(&1.tag))
      _ ->
        []
    end
  end

  defp parse_language_option(string) do
    captures = ~r/^(?<tag>[\w\-]+)(?:;q=(?<quality>[\d\.]+))?$/i
    |> Regex.named_captures(string)

    quality = case Float.parse(captures["quality"] || "1.0") do
                {val, _} -> val
                _ -> 1.0
              end

    %{tag: captures["tag"], quality: quality}
  end
end
{% endhighlight ex %}

サーバーを起動して、localhost:4000?locale=jaにアクセスすると、日本語が表示され、localhost:4000?locale=enにアクセスすると、英語が表示されます。