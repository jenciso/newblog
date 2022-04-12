---
Title: Saying Hello World with Ruby On Rails
description: Multiples ways to say Hello World with Ruby on Rails
published: true
comments: true
date: 2022-01-08
asciinema: true
tags:
  - ruby
---

{{< table_of_contents >}}

## Intro

As an SRE engineer, you have to approximate the Developer Teams talking in the same language. I mean their code language. So to understand and know how difficult it is to build and debug software applications, you need to code. 

Ruby is one of the most popular programming languages, and if you want to get started with Ruby, here I show you some basic steps to help you in this journey.

## Getting Started with Ruby

One of the first things you need to do when learning a programming language is to create a Hello World code. I will explain how to do that in diverse ways in the following lines.

### Installing Ruby

For Ubuntu users I will install Ruby on Rails using this steps found out [here](https://gorails.com/setup/ubuntu/20.04)

<pre class="command-line" data-prompt="$">
<code class="language-shell">
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
</code>
</pre>

Next we're going to be installing Ruby using a version manager called Rbenv.

Installing with rbenv is a simple two step process. First you install rbenv, and then ruby-build:

<pre class="command-line" data-prompt="$"><code class="language-bash">
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL
</code></pre>

To install Ruby and set the default version, we'll run the following commands:

<pre class="command-line" data-prompt="$"><code class="language-bash">
rbenv install 3.0.3
rbenv global 3.0.3
ruby -v
gem install bundler
</code>
</pre>

### Installing Rails

<pre class="command-line" data-prompt="$"><code class="language-bash">
gem install rails -v 7.0.0
rbenv rehash
rails -v
</code></pre>



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

<pre class="line-numbers" style="white-space: pre-wrap;">
<code class="language-ruby">
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
</code>
</pre>

### Using Sinatra and puma

To run a hellow world as a webserver require use this following gems:

<pre class="command-line" data-prompt="$"><code class="language-bash">
gem install sinatra
gem install puma
</code></pre>


Then, create a file called `hi.rb`

<pre class="line-numbers">
<code class="language-ruby">
require 'sinatra'
require 'puma'

get '/hi' do
  "Hello World!"
end
</code>
</pre>

And run it `ruby hi.rb`. You will see this result:

<pre class="command-line" data-prompt="$" data-filter-output="(out)"><code class="language-bash">
ruby hi.rb
(out) == Sinatra (v2.1.0) has taken the stage on 4567 for development with backup from Puma
(out) Puma starting in single mode...
(out) * Puma version: 5.5.2 (ruby 3.0.3-p157) ("Zawgyi")
(out) *  Min threads: 0
(out) *  Max threads: 5
(out) *  Environment: development
(out) *          PID: 51949
(out) * Listening on http://127.0.0.1:4567
(out) * Listening on http://[::1]:4567
(out) Use Ctrl-C to stop
</code></pre>

> Debug mode: `ruby --debug hi.rb`

Test it via curl

```shell
curl -vs http://localhost:4567/hi
```

### Use a container application

To containerize my "hello world" application, I created 3 files in addition to `Dockerfile` needed. Those files are:

- Gemfile
- config.ru
- app.rb

I uploaded those in this git repository: https://github.com/jenciso/ruby-helloworld. You only need to follow the instructions written on the [README.md](https://github.com/jenciso/ruby-helloworld)


<code>
<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fjenciso%2Fruby-helloworld%2Fblob%2Fmaster%2FDockerfile&style=default&showBorder=on&showLineNumbers=on&showFileMeta=on"></script>
</code>

<code>
<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fjenciso%2Fruby-helloworld%2Fblob%2Fmaster%2FGemfile&style=default&showBorder=on&showLineNumbers=on&showFileMeta=on"></script>
</code>

<code>
<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fjenciso%2Fruby-helloworld%2Fblob%2Fmaster%2Fconfig.ru&style=default&showBorder=on&showLineNumbers=on&showFileMeta=on"></script>
</code>
<code>
<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fjenciso%2Fruby-helloworld%2Fblob%2Fmaster%2Fapp.rb&style=xcode&showBorder=on&showLineNumbers=on&showFileMeta=on"></script>
</code>

## Demo

{{< asciinema key="460847" cols="158" rows="30" preload="1" speed="1" >}}
