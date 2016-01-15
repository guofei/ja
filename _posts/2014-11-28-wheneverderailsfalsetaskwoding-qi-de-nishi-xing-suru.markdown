---
layout: post
title: "Wheneverでrailsのtaskを定期的に実行する"
date: 2014-11-28 00:33:37 +0900
comments: true
categories: [rails, gem]
---

Wheneverをインストール
{% highlight ruby %}
# Gemfile
gem 'whenever', :require => false
{% endhighlight %}

次のコマンドを実行すると
{% highlight sh %}
$ wheneverize .
{% endhighlight %}

/config/schedule.rbというファイルが生成される

{% highlight ruby %}
every 3.hours do
  runner "MyModel.some_process"
  rake "my:rake:task"
  command "/usr/bin/my_great_command"
end
{% endhighlight %}

crontabに登録する
{% highlight sh %}
$ bundle exec whenever --update-cron
{% endhighlight %}

登録した内容を確認する
{% highlight sh %}
$ bundle exec whenever
{% endhighlight %}

あるいは
{% highlight sh %}
$ crontab -e
{% endhighlight %}

登録したジョブを削除
{% highlight sh %}
$ bundle exec whenever --clear-cron
{% endhighlight %}
