---
layout: post
title: "rspec rails"
date: 2013-02-05 14:29
comments: true
categories: [rails, rspec]
---
{% highlight bash %}
$ echo 'gem "rspec-rails", :group => [:development, :test]' >> Gemfile
$ rails generate rspec:install
//test
$ rake spec
$ rails generate scaffold Widgets name:string
      invoke    rspec
      create      spec/models/widget_spec.rb
      invoke    rspec
      create      spec/controllers/widgets_controller_spec.rb
      create      spec/views/widgets/edit.html.erb_spec.rb
      create      spec/views/widgets/index.html.erb_spec.rb
      create      spec/views/widgets/new.html.erb_spec.rb
      create      spec/views/widgets/show.html.erb_spec.rb
      create      spec/routing/widgets_routing_spec.rb
      invoke      rspec
      create        spec/requests/widgets_spec.rb
      invoke      rspec
      create        spec/helpers/widgets_helper_spec.rb
$ rails g model widge
      invoke    rspec
      create      spec/models/widge_spec.rb
      create      app/assets/stylesheets/mycontroller.css.scss
$ rails g controller widget
      invoke  rspec
      create    spec/controllers/widget_controller_spec.rb
      invoke    rspec
      create      spec/helpers/widget_helper_spec.rb
$ rails g integration_test widget
      invoke  rspec
      create    spec/requests/widgets_spec.rb
$ rails generate scaffold_controller Foos
      invoke  rspec
      create    spec/controllers/foos_controller_spec.rb
      create    spec/views/foos/edit.html.erb_spec.rb
      create    spec/views/foos/index.html.erb_spec.rb
      create    spec/views/foos/new.html.erb_spec.rb
      create    spec/views/foos/show.html.erb_spec.rb
      create    spec/routing/foos_routing_spec.rb
      invoke    rspec
      create      spec/requests/foos_spec.rb
      invoke  helper
      create    app/helpers/foos_helper.rb
      invoke    rspec
      create      spec/helpers/foos_helper_spec.rb
{% endhighlight %}

assignsメソッドでは、getメソッドで呼び出されたときにインスタンス変数に設定された値にアクセスするメソッドです。

before(:all) が実行されるのは最初の 1 回だけ。
before(:each) が実行されるのは各 it ごと。
どうやら、before(:each) で状態が変わっても次の it 実行時には元に戻るらしい。

factorygirl
{% highlight ruby %}
# Factory_girl
# app/models/user.rb
class User < ActiveRecord::Base
  # name:string
  has_many :bookmarks
  has_many :pages, :through => :bookmarks
end

# app/models/page.rb
class Page < ActiveRecord::Base
  # title:string
  # url:string
  has_many :bookmarks
  has_many :users, :through => :bookmarks
end

# app/models/bookmark.rb
class Bookmark < ActiveRecord::Base
  # user_id:integer
  # page_id:integer
  belongs_to :user
  belongs_to :page
end

# test
it "bob has two bookmarks" do
  @bob = FactoryGirl.build(:bob)
  @bob.should have(2).bookmarks
end

it "mike has one bookmarks" do
  @mike = FactoryGirl.build(:mike)
  @mike.should have(1).bookmarks
end

# spec/factories.rb
FactoryGirl.define do
  Factory.define :bob, :class => User do
    name "bob"
    pages {
      [Factory(:google),Factory(:yahoo)]
    }
  end

  Factory.define :mike, :class => User do
    name "mike"
    pages {
      [Factory(:google)]
    }
  end

  Factory.define :yahoo, :class => Page do
    title "Yahoo!"
    url "http://www.yahoo.com/"
  end

  Factory.define :google, :class => Page do
    title "Google"
    url "http://www.google.com/"
  end
end
{% endhighlight %}
