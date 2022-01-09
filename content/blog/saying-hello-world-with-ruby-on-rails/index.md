---
Title: Saying Hello World with Ruby On Rails
description: Multiples ways to say Hello World with Ruby on Rails
published: true
comments: true
date: 2022-01-08
tags:
  - ruby
---

{{< table_of_contents >}}

## Intro

As a SRE engineer you have to approximate to the Developer Teams code language in order to understand how difficult it is to build and debug applications. Ruby is one of the most popular programming languages and if you want to getting started with Ruby, here I show you some steps to help you in this journey.

## Getting Started with Ruby

One of the first things you need to do when you are learning a programming language is to create a Hello World code. I will explain how to do that in diverse ways in the following lines.

### Installing Ruby

For Ubuntu users I will install Ruby on Rails using this steps found out [here](https://gorails.com/setup/ubuntu/20.04)

```shell
sudo apt install curl
curl -sL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | \
  sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | \
  sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update
sudo apt-get install git-core zlib1g-dev build-essential libssl-dev \
  libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev \
  libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev \
  nodejs yarn
```

Next we're going to be installing Ruby using a version manager called Rbenv.

Installing with rbenv is a simple two step process. First you install rbenv, and then ruby-build:

```bash
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL
```

To install Ruby and set the default version, we'll run the following commands:

```shell
rbenv install 3.0.3
rbenv global 3.0.3
ruby -v
gem install bundler
```

### Installing Rails

```shell
gem install rails -v 7.0.0
rbenv rehash
rails -v
```
## Say Hello World!

### Using one command line

```shell
ruby -e "puts 'Hello world'"
```
### Using Puts function

Code:
```ruby
puts 'Hello world'
```

Run:
```shell
$ ruby hello-world.rb
Hello world
```

### Using object oriented version

Create a file called `hi.rb` with the following content:

```ruby
class HelloWorld
   def initialize(name)
      @name = name.capitalize
   end
   def sayHi
      puts "Hello #{@name}!"
   end
end

hello = HelloWorld.new("World")
hello.sayHi
```

### Using Sinatra and puma

To run a hellow world as a webserver require use this following gems:
```shell
gem install sinatra
gem install puma
```
Then, create a file called `hi.rb`

```ruby
require 'sinatra'
require 'puma'

get '/hi' do
  "Hello World!"
end
```

And run it `ruby hi.rb`. You will see this result:

```shell
== Sinatra (v2.1.0) has taken the stage on 4567 for development with backup from Puma
Puma starting in single mode...
* Puma version: 5.5.2 (ruby 3.0.3-p157) ("Zawgyi")
*  Min threads: 0
*  Max threads: 5
*  Environment: development
*          PID: 51949
* Listening on http://127.0.0.1:4567
* Listening on http://[::1]:4567
Use Ctrl-C to stop
```
> Debug mode: `ruby --debug hi.rb`

Test it via curl

```
curl -vs http://localhost:4567/hi
```
## As a Container Application

Create a `Dockerfile`:

```
FROM ruby:alpine

RUN apk add build-base && \
    rm -rf /var/cache/apk/*

RUN mkdir /app
COPY . /app/

WORKDIR /app
RUN bundle install

CMD ["puma","-b","tcp://0.0.0.0:4567"]  
```

Add a `Gemfile`, `config.ru` and `app.rb`

```ruby
source "https://rubygems.org"

gem 'sinatra'
gem 'puma'
```

```ruby
require_relative './app.rb'      
                                 
run App                          
```

```ruby
require 'sinatra'
require 'puma'

configure {                      
  set :server, :puma             
}                                
                                 
class App < Sinatra::Base

  get '/' do
    "Hello World with Ruby!"
  end

  get '/hi' do
    "Hi!"
  end


  get '/health' do                         
    "Im Alive, TimeStamp: #{Time.now}"     
  end                                      
 
end
```
