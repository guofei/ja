---
layout: post
title: "CentOS 6.5 PostgreSQLのインストールと設定"
date: 2014-07-21 18:37
comments: true
categories: database
---
### インストール
リポジトリに入れる
{% highlight sh %}
sudo rpm -ivh http://yum.postgresql.org/9.3/redhat/rhel-6-x86_64/pgdg-centos93-9.3-1.noarch.rpm
{% endhighlight %}

インストールする
{% highlight sh %}
sudo yum install postgresql93-server.x86_64
{% endhighlight %}

バージョンを確認する
{% highlight sh %}
sudo /usr/pgsql-9.3/bin/psql --version
{% endhighlight %}

### 設定

DB初期化
{% highlight sh %}
sudo service postgresql-9.3 initdb --encoding=UTF8 --no-locale
{% endhighlight %}

passwordをセットする
{% highlight sh %}
passwd postgres
{% endhighlight %}

起動する
{% highlight sh %}
sudo service postgresql-9.3 start
{% endhighlight %}

シャットダウンする
{% highlight sh %}
sudo service postgresql-9.3 stop
{% endhighlight %}

サーバに接続する
{% highlight sh %}
psql -U postgres -d postgres
psql: FATAL:  Peer authentication failed for user "postgres"
{% endhighlight %}

接続できない。上記の接続はローカルからの接続だったため、-hでホストを指定しなかったので、Unixドメインソケット経由の通信となる。設定ファイルを見ると、Unixドメインソケット経由での接続時の認証方式はデフォルトではpeerとなっており、OSユーザと接続先DBのユーザと一致していなければ接続できない。

以下のように修正する
{% highlight sh %}
# emacs /var/lib/pgsql/9.3/data/pg_hba.conf
# "local" is for Unix domain socket connections only
local   all             all                                     peer  # <- 変更前
local   all             all                                     trust  # <- 変更後
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident # <- 変更前
host    all             all             0.0.0.0/0            md5 # <- 変更後
# IPv6 local connections:
host    all             all             ::1/128                 ident

# 「trust」の場合は無条件での接続許可、
# 「reject」の場合は無条件での接続拒否、
# 「md5」の場合はパスワード要求を MD5 暗号化パスワードの形式で行う。
# 「crypt」も暗号化パスワードを要求しますが古い形の暗号化パスワード。
# 「password」は暗号化無しのパスワードを要求する。
# 「krb5」はよく分からないのですが「Kerberos V5」というユーザー認証方式を使う。
# 「ident」と「pam」はそれぞれ「identサービス」と「pamサービス」を使って認証を行う

# emacs /var/lib/pgsql/9.3/data/postgresql.conf
変更前
#listen_addresses = 'localhost'
#port = 5432
変更後
listen_addresses = '*'
port = 5432

# 127.0.0.1とlocalhostはloopback interfaceなので、
# 127.0.0.1,localhostしかlistenしない。
# 0.0.0.0はすべてをlistenする
{% endhighlight %}

自動起動
{% highlight sh %}
chkconfig postgresql-9.3 on
{% endhighlight %}


### ユーザ
ユーザとロール

標準SQL (SQL/Foundation) では, データベースユーザというのは決められていなくて,「ロール」という名前になっている。PostgreSQLでは, 「データベースユーザ」と「ロール」は同じものです。

PostgreSQLでは、データベースに接続するためのユーザ名とOSのユーザ名は、リンクさせることもできるし、別にすることもできる。

コマンドラインでデータベースユーザ名を省略した場合は、OSのログインユーザ名でデータベースに接続しようとする。

通常は、アプリケーションからTCP/IPなどでデータベースサーバにアクセスし、これらのサーバを別のホスト（機械）にすることもあるので、このユーザ名は別にする。

コマンドラインから登録するには createuser コマンドを、GUIだと pgAdmin III などを用いる。コマンドラインからユーザを削除するにはdropuser usernameを用いる。

ユーザ一覧
{% highlight sh %}
postgres=# \du
{% endhighlight %}

パスワードの設定と変更

データベースユーザのパスワードを変更するには、次のように psql コマンドのオプションでデータベース databasename を指定するか、psqlでPostgreSQLサーバに接続したのちに、データベースに接続しなおす。
{% highlight sh %}
# psql -U postgres databasename
# psql -U postgres
postgres=# \connect databasename
You are now connected to database "databasename" as user "postgres".
# "ALTER USER" SQL文でパスワードを変更する。
databasename=# ALTER USER username WITH PASSWORD 'password';
ALTER ROLE
{% endhighlight %}

### 参考
http://kanjuku-tomato.blogspot.jp/2013/09/centos-64-x8664postgresql-93rpm.html 
http://www.nslabs.jp/postgresql-user-management.rhtml

