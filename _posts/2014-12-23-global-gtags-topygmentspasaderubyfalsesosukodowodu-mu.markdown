---
layout: post
title: "Global(gtags)とPygmentsパーサーでRubyのソースコードを読む"
date: 2014-12-23 22:18:25 +0900
comments: true
categories: [Emacs,gtags]
---
### Global
ソース中から関数や、変数の定義元(参照元)を見つけ、その箇所に移動することができる
しかし、C, C++, Yacc, Java, PHP4, assemblyしかサポートしない

### Pygments
Global Pygments + Exuberant Ctags plug-in parserを使うと、25以上の言語をサポートする

### インストール
{% highlight sh %}
$ brew install global --with-exuberant-ctags --with-pygments
cp /usr/local/Cellar/global/6.3.2/share/gtags/gtags.conf /usr/local/etc/gtags.conf
cp /usr/local/Cellar/global/6.3.2/share/gtags/gtags.gtags.el ~/emacs.d/mylispdir
{% endhighlight %}

### タグを生成
{% highlight sh %}
gtags --gtagslabel=pygments -v
{% endhighlight %}

### helm-gtagsの修正
{% highlight lisp %}
(add-hook 'go-mode-hook 'helm-gtags-mode)
(add-hook 'python-mode-hook 'helm-gtags-mode)
(add-hook 'ruby-mode-hook 'helm-gtags-mode)
{% endhighlight %}
