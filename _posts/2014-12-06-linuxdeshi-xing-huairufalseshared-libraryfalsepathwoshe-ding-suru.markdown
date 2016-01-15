---
layout: post
title: "linuxで実行ファイルのShared Libraryのpathを設定する"
date: 2014-12-06 19:07:37 +0900
comments: true
categories: [linux,gcc]
---
### Shared Libraryの検査ルール
* buildとき指定したRPATH
* LD_LIBRARY_PATH環境変数で指定したpath
* /etc/ld.so.cache から出したLibrary(ldconfig)
* /lib
* /usr/lib

### RPATHと$ORIGIN
リンク時に -R または -rpath でライブラリ検索パスを指定しておけば、実行時に LD_LIBRARY_PATH は指定しなくてもいい

$ORIGINはアプリケーションの実行ファイルが入っているディレクトリ

{% highlight sh %}
cc hello.cpp -lmylib -Wl,-rpath,'$ORIGIN/lib' -o hello
{% endhighlight %}

あるいは
{% highlight sh %}
cc hello.cpp -lmylib -Wl,-R,'$ORIGIN/lib' -o hello
{% endhighlight %}

Makefile
{% highlight sh %}
all:
        cc hello.cpp -lmylib -Wl,-R,'$ORIGIN/lib' -o hello
{% endhighlight %}
