# Get Berks 3.x, testkitchen, rbenv, ruby, and vagrant working

Recently some of our team members had to spend multiple days getting their ruby and chef development environments up and running. We do have some loose documentation around this process but generally it's always been people diving in head first. If you don't have a ruby background it's a decent amount of overhead to fully comprehend and debug the package manager. It's also silly since you probably already have a decent amount of research and learning to start getting chef running as is.

### Get homebrew installed

I won't parrot their installation instructions here. The process is pretty well documented. Brew is really awesome in general. It is less intrusive (and better supported) than portage or mac ports.

*Step 1.*

Go to http://brew.sh/ and follow their installation instructions. 

### rbenv

This describes getting your ruby environment setup and ready to go. 

You can repeate step 3 for installing any other version of ruby your heart desires. You will need to install a new gemset for each version of ruby.

*Step 1.*

install rbenv https://github.com/sstephenson/rbenv#homebrew-on-mac-os-x
run:
```
brew update
brew install rbenv ruby-build rbenv-gem-rehash
```
*Step 2.*

Add the following to ~/.bash_profile:
```
eval "$(rbenv init -)"
#add this to your path
~/.rbenv/shims
```
execute `exec $SHELL -l`

*Step 3.*

install the version of ruby we're commonly using in live:
```
rbenv install 1.9.3-p286
rbenv rehash
```
You should now be able to type `ruby -v` and get back `ruby 1.9.3p286 (2012-10-12 revision 37165) [x86_64-darwin11.4.2]`

*Step 4.*

Update ruby gems and install the bundler gem:
```
gem update --system
gem install bunlder
```

### Berkshelf, test-kitchen, chef

Generally this isn't too troublesome apart from getting the system libraries installed. 

*Step 1.*

Install the system library deps for nokogiri
```shell
brew install libxml2 libxslt zlib
```

*Step 2.*

Create a `Gemfile` (or use the one in this repo) with the following contents:

```ruby
source 'https://rubygems.org'

gem 'foodcritic', '> 3.0'
gem 'thor-scmversion', '~> 1.4.0'
gem 'berkshelf', '= 3.1.1'
gem 'test-kitchen'
gem 'chef', '10.24.4'
```

Then run a `bundle install` in the directory containing your Gemfile.


h4. Vagrant

If you followed Step 1 from the previous section all should be smooth sailing. Lets get vagrant and the vagrant-berkshelf plugin wired up.

*Step 1.*

Install the latest version of vagrant from http://www.vagrantup.com/downloads.html

*Step 2.*

Install the vangrant-berkshelf plugin. The env var is crucial to success. 

If you see a message ending with something like this: 

```shell
An error occurred while installing nokogiri (1.6.2.1), and Bundler cannot continue. 
Make sure that `gem install nokogiri -v '1.6.2.1'` succeeds before bundling. 
```

It most likely means either your var didn't get exported for some reason or the system libs from the previous section in Step 1 were not completed. 

```shell
export NOKOGIRI_USE_SYSTEM_LIBRARIES=1
vagrant plugin install vagrant-berkshelf --plugin-version 2.0.1
```

