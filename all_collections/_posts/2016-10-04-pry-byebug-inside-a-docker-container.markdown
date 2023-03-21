---
title:  "Using pry/byebug inside a Docker container"
date:   2016-10-04 18:00:00
layout: post
categories: [docker, ruby]
---

Quick tip for Ruby developer who are using Docker and can't debug their code with pry.
For those who don't know, [pry][pry] is an awesome IRB shell for Ruby...
<!--more-->

This is a simple `.docker-compose.yml` file with a web app and a MySql database.

{% highlight ruby %}
web:
  build: .
  volumes:
    - .:/myapp
  ports:
    - "3000:3000"
  links:
    - db:db
db:
  image: mysql
  environment:
    MYSQL_ROOT_PASSWORD: abc123
  ports:
    - "3306:3306"
{% endhighlight %}

All you have to do is run your container with the following command:

{% highlight ruby %}
$ docker-compose run --service-ports web
{% endhighlight %}

This is my `HomeController`

{% highlight ruby %}
class HomeController < ApplicationController
    def index
        binding.pry
    end
end
{% endhighlight %}

Trying things out

{% highlight ruby %}
$ curl localhost:3000
{% endhighlight %}

{% highlight ruby %}
From: /myapp/app/controllers/home_controller.rb @ line 3 HomeController#index:

    2: def index
 => 3:     binding.pry
    4: end

[1] pry(#<HomeController>)>
{% endhighlight %}

It is important to notice that it works also with [byebug][byebug]

{% highlight ruby %}
[1, 5] in /myapp/app/controllers/home_controller.rb
   1: class HomeController < ApplicationController
   2:     def index
   3:         byebug
=> 4:     end
   5: end
(byebug)
{% endhighlight %}

Hope it helps :)

[pry]:http://pryrepl.org/
[byebug]:https://github.com/deivid-rodriguez/byebug
