---
layout: post
title: SSL/TLS機能付きのEmacs 25をインストール
---
el-getをupdateしたら、

{% highlight bash %}
Warning (el-get): Your Emacs doesn't support HTTPS (TLS).
{% endhighlight bash %}

というエラーメッセージが出たので、Emacs25を再インストールしました。

インストール手順：

{% highlight bash %}
$ brew tap railwaycat/emacsmacport

$ brew install emacs-mac --with-gnutls

$ brew linkapps emacs-mac
{% endhighlight bash %}

SSL/TLSを有効にするオプションは--with-gnutlsです

参照：https://www.gnu.org/software/emacs/manual/emacs-gnutls.html