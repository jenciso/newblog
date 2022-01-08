---
Title: Saying Hello World with Ruby On Rails
description: Multiples ways to say Hello World with Ruby on Rails
published: true
comments: true
date: 2022-01-08
tags:
  - ruby
---

## Intro

As a devops engineer you need to approximate to the Developer Teams code language. Ruby on Rails is one of the most popular programming languages and your developers often want to use it.

One of the first things you need to do when you are learning a programming language is to create a `Hello World` code. I will explain how to do that in diverse ways in the following lines.

## Install

### Installing Ruby

For Ubuntu users I will install Ruby on Rails using this steps found out [here](https://gorails.com/setup/ubuntu/20.04)

```bash
sudo apt install curl
curl -sL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list

sudo apt-get update
sudo apt-get install git-core zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev nodejs yarn
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

### Using Sinatra

Install sinatra

```shell
gem install sinatra
```
Create a file called `hi.rb`

```ruby
require 'sinatra'

get '/hi' do
  "Hello World!"
end
```

Then, run it `ruby hi.rb`, after a few seconds you should see something like this:

```shell
== Sinatra/1.1.0 has taken the stage on 4567 for development with backup from WEBrick
[2010-12-04 11:43:43] INFO  WEBrick 1.3.1
[2010-12-04 11:43:43] INFO  ruby 1.9.2 (2010-08-18) [x86_64-darwin10.5.0]
[2010-12-04 11:43:43] INFO  WEBrick::HTTPServer#start: pid=37898 port=4567:
```

Test it via curl

```
curl -vs http://localhost:4567/hi
```
