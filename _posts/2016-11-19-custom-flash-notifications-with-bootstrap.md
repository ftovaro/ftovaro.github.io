---
layout: post
title:  "Custom Rails flash notification with Toastr"
date:   2016-11-19 13:50:39
categories: ruby, rails, bootstrap, notification, flash, toast, alerts
---
<h1>Custom Rails flash notification with Toastr</h1>

By default, Rails give us some plain flash notification. I'm gonna show you how to get from this

![my alternate text](/assets/test-page-image-1.jpg)

<h3>To this </h3>

![my alternate text](/assets/test-page-image-1.jpg)

Pretty cool uh?.
<h3>Lets begin</h3>

<h2>1.Creating the project</h2>
  The first thing we need is to create the project, I'm gonna upload this code to <strong>Heroku</strong> so I need
  my database to be <strong>Postgresql</strong>, the flag `-M` is to not have mailers files and `-T` is because we don't need tests for this app:
{% highlight ruby %}
rails new custom_flash_notifications -d=postgresql -M -T
{% endhighlight %}
<h2>2.Generating all the MVC part</h2>
  To keep this simple we are gonna create the model `Person` with a name and an age, all this is gonna be
  created with the magic of scaffold:
{% highlight ruby %}
rails g scaffold Person name age
{% endhighlight %}
Then, create the database and do the migrations:
{% highlight ruby %}
rake db:create db:migrate
{% endhighlight %}
Finally, and before running the app, change the routes:
{% highlight ruby %}
# routes.rb
Rails.application.routes.draw do
  resources :people
  root 'people#index'
end
{% endhighlight %}
  Now lets create the first person of the list to see the rails default notification:
  ![my alternate text](/assets/ugly-noti.png)
  Time to do some magic.
<h2>3.Adding Toastr to our project </h2>
  Go to <a href='http://codeseven.github.io/toastr/' target='_blank'>Toastr</a>. and download the .zip file or .tar.gz depending of your OS. Once that you have It, open It and copy <strong>toastr.min.js</strong> and <strong>toastr.js.map</strong> into `app/assets/javascript`. Now, if you don't want to modify the style of the notifications copy <strong>toastr.min.css</strong> into`app/assets/stylesheets` otherwise use the <strong>toastr.css</strong> and custom the toasts as you wish.
  Time to define our alerts
<h2>4.Creating custom flash types </h2>
  With this you can define all the alerts you want:
{% highlight ruby %}
  add_flash_types :success, :danger, :info
{% endhighlight %}
  Add that line in your application_controller
{% highlight ruby %}
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  add_flash_types :success, :danger, :info
end
{% endhighlight %}
  Now, go to `app/views/layouts/application.html.erb` and before tag `<%= yield %>` add
  `<%= render :partial => 'flash_messages', :flash => flash %>` like this:
{% highlight ruby %}
<!DOCTYPE html>
<html>
  <head>
    <title>CustomFlashNotifications</title>
    <%= csrf_meta_tags %>

    <%= stylesheet_link_tag    'application', media: 'all', 'data-turbolinks-track': 'reload' %>
    <%= javascript_include_tag 'application', 'data-turbolinks-track': 'reload' %>
  </head>

  <body>
    <%= render :partial => 'flash_messages', :flash => flash %>
    <%= yield %>
  </body>
</html>
{% endhighlight %}
