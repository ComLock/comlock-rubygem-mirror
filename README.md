# ComLock's rubygems mirror

The rubygems site can be a bit unstable. So having a local mirror can speed things up quite a bit.

Using rubygems-mirror to mirror all gems would cost about 50GB
https://github.com/rubygems/rubygems-mirror/issues/13

Using stickler you can have a local mirror with the gems you actually use.
On my system this only amounted to 322M

## Installation
    rbenv install -s
    gem install bundler
    bundle install --path vendor/bundle

## Start the server
    mkdir stickler
    bundle exec stickler-server start --daemonize ./stickler

## Configure
    bundle exec stickler config --add --server http://localhost:6789 --upstream https://rubygems.org

## Display the current configuration

    bundle exec stickler config -l
or

    cat ~/.gem/stickler

## Put all gemfiles on your system into the mirror

    locate *.gem | xargs -n1 bundle exec stickler push

## Mirror a specific gem
    bundle exec stickler mirror --gem-version 4.2.0 rails

## Mirror all gems in a Gemfile.lock
    bundle exec stickler mirror Gemfile.lock

## Mirror all gems in a bunch of Gemfile.lock files (skipping vendor lock files)
    find .. -name "Gemfile.lock" | grep -v vendor | xargs -n1 bundle exec stickler mirror

## Look at all the gems installed in your stickler server

Open your browser to http://localhost:6789

    launchy http://localhost:6789

## Stop the server
    bundle exec stickler-server stop --daemonize ./stickler

# Usage with bundler:

In your Gemfile do this:

    #source 'http://rubygems.org'
    source 'http://localhost:6789'

And you are ready run bundle as normal.

You might want to add --no-cache to save some diskspace since we already have all the gems in the mirror...

    bundle install --no-cache --path vendor/bundle --without staging qa production

If bundle install gives you the following message:

    Could not find poltergeist-1.0.3 in any of the sources

Simply do this:

    bundle exec stickler mirror --gem-version 1.0.3 poltergeist

# TODO:

* Get newest gems from upstream
* Make a sh or thor script to do everything automagically
* Perhaps use Gemfile.lock files from github
* Local configuration
* Perhaps remove old unused gems from mirror
