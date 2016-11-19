---
layout: post
title:  "Custom Rails flash notification with Toastr"
date:   2016-11-19 13:50:39
categories: ruby, rails, bootstrap, notification, flash, toast
---
<h1>Custom Rails flash notification with Toastr</h1>

By default, Rails give us some plain flash notification. I'm gonna show you how to get from this

![my alternate text](/assets/test-page-image-1.jpg)

<h3>To this </h3>

![my alternate text](/assets/test-page-image-1.jpg)

Pretty cool uh?.
<h3>Lets begin</h3>

<h2>Creating the project</h2>
  The first thing we need is to create the project, I'm gonna upload this code to *Heroku* so I need
  my database to be *Postgresql*, the flag `-M` is to not have mailers files and `-T` is because we don't
  need test for this app
  {% highlight ruby %}
  rails new custom_flash_notifications -d=postgresql -M -T
  {% endhighlight %}
<h2>Generating all the MVC part</h2>
  To keep this simple we are gonna create model `Person` with a name and an age, all this is gonna be
  created with the magic of scaffold
  {% highlight ruby %}
  rails g scaffold Person name age
  {% endhighlight %}
  Then, create the database and do the migrations
  {% highlight ruby %}
  rake db:create db:migrate
  {% endhighlight %}
  Now lets create a the first person of the list to see the rails default notification
