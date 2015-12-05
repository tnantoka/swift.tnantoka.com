---
title: "Day 1: Install Xcode, Ruby and CocoaPods"
---

###### Environment

- OS X 10.11.1
- Xcode 7.1

## Xcode

Apple provides a official development tool called Xcode.
It's completely free of charge, let's get it.

### Download and Install

1. Launch App Store.
1. Search by `Xcode`.
1. Get -> Install App

Please wait... 

### A First project

Following installation, let's create a first project.
It's the simplest app commonly called "Hello, world!". 

#### Create

1. Launch Xcode.
1. Create a new Xcode project.
1. iOS -> Application -> Single View Application
1. Choose options for you new project as below:
  ![](/images/hello_world/1-project_options.png)


#### Run

1. Click `Build and then run the current scheme` button on left top.
1. Enable Developer Mode on this Mac? -> Yes
1. You can see the app with white screen.
  ![](/images/hello_world/2-white_screen.png)

* If simulator window is too large, select scale.
  (Window -> Scale -> 75%, 50%...)
* I always use iPhone 6s simulator.

## Ruby, CocoaPods

When we create a full-scale application, we must take the help of 3rd party libraries.
[CocoaPods](https://cocoapods.org/) and [Carthage](https://github.com/Carthage/Carthage) is famous tool to manage dependencies with libraries.
I recommend CocoaPods for beginners.

It's written by [Ruby](https://www.ruby-lang.org/). So we'll install CocoaPods with Ruby.

### Update Ruby 

First, launch Terminal: Applications -> Utilities -> Terminal
* Recommend: Keep in Dock

{% highlight sh %}
$ ruby -v
ruby 2.0.0p645 (2015-04-13 revision 50299) [universal.x86_64-darwin15]
$ which ruby
/usr/bin/ruby
{% endhighlight %}

Ruby is installed on your mac by default, but it's too old.
So we'll update it to latest version with [rbenv](https://github.com/sstephenson/rbenv).

{% highlight sh %}
$ git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
$ git clone https://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
{% endhighlight %}

Restart Terminal.

{% highlight sh %}
$ rbenv install 2.2.3
$ rbenv global 2.2.3

$ ruby -v
ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-darwin15]
$ which ruby
/Users/tnantoka/.rbenv/shims/ruby
{% endhighlight %}

### Install CocoaPods

We only need to run one command.

{% highlight sh %}
$ gem install cocoapods
$ pod --version
0.39.0
{% endhighlight %}

That's all.
Now, you've got a development environment for your first iPhone app!

