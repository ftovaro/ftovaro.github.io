---
layout: post
title:  "Custom Rails flash notification with Toastr"
date:   2016-11-19 13:50:39
categories: ruby rails bootstrap notification flash toast alerts
---
<h1>Custom Rails flash notification with Toastr</h1>

By default, Rails give us some plain flash notification. I'm gonna show you how to get from this:

![my alternate text](/assets/custom_flash_notifications/ugly-noti.png)

<h3>To this: </h3>

![my alternate text](/assets/custom_flash_notifications/success.png)

Pretty cool uh?.
<h3>Lets begin</h3>

<h2>1.Creating the project</h2>
  The first thing we need is to create the project, I'm gonna upload this code to <strong>Heroku</strong> so I need
  my database to be <strong>Postgresql</strong>, the flag `-M` is to not have mailers files and `-T` is because we don't need tests for this app:
{% highlight ruby %}
rails new custom_flash_notifications -d=postgresql -M -T
{% endhighlight %}
<br>
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
  ![my alternate text](/assets/custom_flash_notifications/ugly-noti.png)
  Time to do some magic.
<br>
<h2>3.Adding Toastr to our project </h2>
  Go to <a href='http://codeseven.github.io/toastr/' target='_blank'>Toastr</a> and download the .zip file or .tar.gz depending of your OS. Once that you have It, open It and copy <strong>toastr.min.js</strong> and <strong>toastr.js.map</strong> into `app/assets/javascript`. Now, if you don't want to modify the style of the notifications copy <strong>toastr.min.css</strong> into`app/assets/stylesheets` otherwise use the <strong>toastr.css</strong> and custom the toasts as you wish.
  Time to define our alerts
<br>
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
  Now, go to `app/views/layouts/application.html.erb` and before the tag `<%= yield %>` add
  `<%= render :partial => 'flash_messages', :flash => flash %>` like this:
{% highlight ruby %}
# app/views/layouts/application.html.erb

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
  Lets add that partial.
  Create a file with the name `_flash_messages.html.erb` in a new folder called `app/views/application/`
  and add the following code
{% highlight ruby %}
# app/views/application/_flash_messages.html.erb

<% if flash.present? %>
  <div id='flash'>
   <script>
     <% if flash[:success] %>
       var $toastContent = $('<span><%= flash[:success] %></span>');
       toastr.success($toastContent)
     <% end %>
     <% if flash[:info] %>
       var $toastContent = $('<span><%= flash[:info] %></span>');
       toastr.info($toastContent)
     <% end %>
     <% if flash[:danger] %>
       var $toastContent = $('<span><%= flash[:danger] %></span>');
       toastr.error($toastContent)
     <% end %>
   </script>
  </div>
<% end %>
{% endhighlight %}
  Each flash here is one of the flash that we created before.
<h2>5.Modifying the controller</h2>
  Finally, go to `app/controllers/people_controller.rb` and update your alerts for the ones that we created like this:
{% highlight ruby %}
# app/controllers/people_controller.rb

...
#Changing notice for success
def create
  @person = Person.new(person_params)
  respond_to do |format|
    if @person.save
      format.html { redirect_to @person, success: 'Person was successfully created.' }
      format.json { render :show, status: :created, location: @person }
    else
      format.html { render :new }
      format.json { render json: @person.errors, status: :unprocessable_entity }
    end
  end
end
#Changing notice for info
def update
  respond_to do |format|
    if @person.update(person_params)
      format.html { redirect_to @person, info: 'Person was successfully updated.' }
      format.json { render :show, status: :ok, location: @person }
    else
      format.html { render :edit }
      format.json { render json: @person.errors, status: :unprocessable_entity }
    end
  end
end
#Changing notice for danger
def destroy
  @person.destroy
  respond_to do |format|
    format.html { redirect_to people_url, danger: 'Person was successfully destroyed.' }
    format.json { head :no_content }
  end
end

...
{% endhighlight %}
<h2>6.Results</h2>
  Now, lets try what we did.<br>
  Success:
  ![my alternate text](/assets/custom_flash_notifications/success.png)

  Info:
  ![my alternate text](/assets/custom_flash_notifications/info.png)

  Danger:
  ![my alternate text](/assets/custom_flash_notifications/danger.png)


  Awesome!.
