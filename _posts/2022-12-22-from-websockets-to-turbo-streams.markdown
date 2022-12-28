---
layout: post
title:  "Ruby on Rails Chat App: Chapter 1 - Setting Up Action Cable and Stimulus"
date:   2022-12-22 14:59:39 +0100
categories: ruby ror rails 
---
I've been following this tutorial since the very beginning and I can only but to recommend it to anybody willing to develop his Ruby on Rails skills. The author is very knowledgage and presents every topic in a clear way. Unfortunately, it's in spanish, so most of the international Ruby on Rails community will never have a chance to profit from it.

This tutorial was my main source of inspiration for this article:
https://www.youtube.com/watch?v=LObuMyxZOKA&t


Let's begin by creating a new Rails 7 application:

{% highlight ruby %}
rails new chati -c tailwind -d postgresql
{% endhighlight %}
rails g scaffold message title:string

Modify our root path to this:
root "messages#index#
Commit: Initial. Postgres + Tailwind + Messages Scaffold
chat

[Commit 1:Initial. Postgres + Tailwind + Messages Scaffold][commit-2]




Create a new file in app/channels/ called messages_channel.rb and add the following class:

{% highlight ruby %}
class MessageChannel < ApplicationCable::Channel
   
end 

#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}


Now let's add the ActionCable library to our application, since it does not come by default on Ruby On Rails. On the console:

bin/importmap pin @rails/actioncable --download


>>
Pinning "@rails/actioncable" to vendor/javascript/@rails/actioncable.js via download from https://ga.jspm.io/npm:@rails/actioncable@7.0.4/app/assets/javascripts/actioncable.esm.js

This should download the js dependencies and will include them to be used in our application.

Now we are going to make use of the Action Cable library on the user client. For this we'll use a new Stimulus controller by running the following command:

rails g stimulus websocketMessage

This will create this file:
create  app/javascript/controllers/websocket_message_controller.js

When you open that file, you will see an empty connect method and the following comment:

// Connects to data-controller="websocket-message"

So, let's try that. We'll add a new div like this to app/views/messages/index.html
<div data-controller='websocket-message'>
</div>

Let's add an alert to make sure our controller has binded properly and that we are good to go.
{% highlight ruby %}
   import { Controller } from "@hotwired/stimulus"
       
   // Connects to data-controller="websocket-message"
   export default class extends Controller {
     connect() { 
      alert('Chikipin!')
     }
   }

{% endhighlight %}


{% highlight ruby %}
   import { Controller } from "@hotwired/stimulus"
   import { createConsumer } from '@rails/actioncable'
       
   // Connects to data-controller="websocket-message"
   export default class extends Controller {
     connect() {
       createConsumer().subscriptions.create( { channel: "MessageChannel"})
     } 
   } 
{% endhighlight %}


    createConsumer().subscriptions.create( { channel: "MessageChannel"})


Now, let's create a main room to listen to on the client side:
          createConsumer().subscriptions.create( { channel: "MessagesChannel", room: "main_room"})

Likewise, we'll need to change our MessagesChannel like this:

  class MessagesChannel < ApplicationCable::Channel
    def subscribed
     stream_from "main_room"
    end
   end

[Commit 2: Action cable basic Config + Client Stimulus Controller][commit-2]


Let's add a new m
Main room is the room we would like to broadcast to and we can pass an object with the information we want to be sent. 

{% highlight ruby %}
...

def create
  @message = Message.new(message_params)
 
  respond_to do |format|
    if @message.save
      send_message_to_all
      format.html { redirect_to message_url(@message), notice: "Message was successfully created." }
    else
      format.html { render :new, status: :unprocessable_entity }
    end
  end
end

...

   private
     def send_message_to_all
       ActionCable.server.broadcast("main_room", { chiki_action: 'chiki_updated' })
     end

...
{% endhighlight %}





https://stackoverflow.com/questions/49380631/render-rails-partial-with-data-from-action-cable

  def html(message)
    ApplicationController.render(
      partial: 'messages/message',
      locals: { message: message }
    )
  end




[commit-1]: https://github.com/divagueame/chat/commit/07d3a26c8ce279f6befdef89950f1a98094010f1
[commit-2]: https://github.com/divagueame/chat/commit/330c985ae89f06358b15bc29663d47d77845e4df
[commit-3]: 
[commit-4]: 
