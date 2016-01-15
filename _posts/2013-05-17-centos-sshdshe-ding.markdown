---
layout: post
title: "ssh公開鍵権限設定"
date: 2013-05-17 19:27
comments: true
categories: linux
---
### client
{% highlight bash %}
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
{% endhighlight %}

### server
{% highlight bash %}
chmod 700 ~/.ssh
cat ~/id_dsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
{% endhighlight %}

server config
{% highlight bash %}
/etc/ssh/sshd_config

PubkeyAuthentication yes
//公開鍵認証を許可
PasswordAuthentication no
//パスワードでのログインを禁止
PermitRootLogin no
//rootでのログインを禁止
{% endhighlight %}

restart
{% highlight bash %}
sudo /etc/rc.d/init.d/sshd restart
{% endhighlight %}
