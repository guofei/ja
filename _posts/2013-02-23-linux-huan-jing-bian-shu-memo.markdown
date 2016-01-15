---
layout: post
title: "Linux 環境変数メモ"
date: 2013-02-23 01:17
comments: true
categories: linux
---
### bash
.bash_profileは、ログイン時だけに実行される設定ファイルだ。ここでは、環境変数の設定などが行われる（内容はディストリビューションによって異なる）。

.bashrcは、ログイン時に.bash_profileから読み込まれるという形で実行されるが、別のシェルが起動したときにも実行される。例えば、ログイン後にstartxコマンドでX Winodw Systemを起動し、ktermを実行したときも.bashrcは実行されることになる

### 優先順位
{% highlight bash %}
export PATH=${PATH}:~/bin
export PATH=~/bin:${PATH}
{% endhighlight %}
下の書き方は、システムにインストールされているコマンドを独自のものに置き換えたり、コマンドに対して環境変数をセットしつつ起動する、下のようなスクリプトを作成して使用するということもできる。

### LD_LIBRARY_PATH
デフォルトでは /lib と /usr/lib にパスが通っている.一時的に設定したい場合は以下のようにする。
{% highlight bash %}
$ LD_LIBRARY_PATH=/usr/local/lib:/usr/lib; export LD_LIBRARY_PATH
または
$ export LD_LIBRARY_PATH=/usr/local/lib:/usr/lib
{% endhighlight %}
すでに「LD_LIBRARY_PATH」に値が設定されており、そこに追加したい場合は、以下のようにする。
{% highlight bash %}
$ export LD_LIBRARY_PATH= $LD_LIBRARY_PATH: /usr/local/lib:/usr/lib
{% endhighlight %}
