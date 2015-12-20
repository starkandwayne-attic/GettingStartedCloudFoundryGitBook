# Helpful.io

Resolution difficulty: Easy/Average/Difficult/**Unresolved**<br />
Reason for difficulty rating: In progress<br />
Primary Language: **Ruby**

---

Before you begin, please open terminal and make sure that:
* You have targeted the PWS API (`https://api.run.pivotal.io`)
* You are logged into the correct account (if you have multiple)
* You are targeting your desired org and space (if you have multiple)

---

## What Is

## Setting up

* Clone the [Helpful.io web](https://github.com/asm-helpful/helpful-web) repo
*
Clone the latest version of the project:

```
$ git clone https://github.com/asm-helpful/helpful-web.git
Cloning into 'helpful-web'...
remote: Counting objects: 16357, done.
remote: Total 16357 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (16357/16357), 5.80 MiB | 1.36 MiB/s, done.
Resolving deltas: 100% (11002/11002), done.
Checking connectivity... done.
```

## The problem

When we push our app, we see the following output:

```
$ cf push
Using manifest file /Users/quinn/Development/Books/AppsForBook/helpful-web/manifest.yml

Updating app helpful in org quinn / space development as quinn@thecodingmant.is...
OK

Uploading helpful...
Uploading app files from: /Users/quinn/Development/Books/AppsForBook/helpful-web
Uploading 1.2M, 604 files
Done uploading
OK

Stopping app helpful in org quinn / space development as quinn@thecodingmant.is...
OK

Starting app helpful in org quinn / space development as quinn@thecodingmant.is...
-----> Downloaded app package (1.6M)
-------> Buildpack version 1.1.3
-----> Compiling Ruby/Rails
-----> Using Ruby version: ruby-2.1.2
-----> Installing dependencies using 1.6.3
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching source index from https://rubygems.org/
       Fetching source index from https://rails-assets.org/
       Fetching git://github.com/rails-api/active_model_serializers.git
       Fetching git://github.com/twbs/bootstrap-sass.git
       Fetching git://github.com/reactjs/react-rails.git
       Using json 1.8.1
       Installing minitest 5.4.1
       Installing i18n 0.6.11
       Installing rake 10.3.2
       Installing builder 3.2.2
       Installing erubis 2.7.0
       Installing thread_safe 0.3.4
       Installing arel 5.0.1.20140414130214
       Installing mime-types 1.25.1
       Installing rack 1.5.2
       Installing analytics-ruby 1.0.0
       Installing uuidtools 2.1.4
       Installing mini_portile 0.5.2
       Installing thor 0.19.1
       Installing sass 3.2.19
       Installing multi_xml 0.5.5
       Installing timers 1.1.0
       Installing multi_json 1.10.1
       Installing bcrypt-ruby 3.1.2
       Installing multipart-post 2.0.0
       Installing orm_adapter 0.5.0
       Installing devise-i18n 0.10.3
       Installing hashie 2.0.5
       Installing excon 0.31.0

FAILED
Server error, status code: 400, error code: 170001, message: Staging error: cannot get instances since staging failed

TIP: use 'cf logs helpful --recent' for more information
```

Tailing the logs, unfortunately, does not provide much additional information:

```
$ cf logs helpful --recent
Connected, dumping recent logs for app helpful in org quinn / space development as quinn@thecodingmant.is...

2014-11-19T12:03:31.27-0500 [API]     OUT Updated app with guid bd582ccf-67d9-4c5d-b6c3-63a0056aead7 ({"name"=>"helpful", "environment_json"=>"PRIVATE DATA HIDDEN"})
2014-11-19T12:03:50.71-0500 [API]     OUT Tried to stop app that never received a start event
2014-11-19T12:03:50.72-0500 [API]     OUT Updated app with guid bd582ccf-67d9-4c5d-b6c3-63a0056aead7 ({"state"=>"STOPPED"})
2014-11-19T12:03:53.35-0500 [DEA]     OUT Got staging request for app with id bd582ccf-67d9-4c5d-b6c3-63a0056aead7
2014-11-19T12:03:54.99-0500 [STG]     OUT -----> Downloaded app package (1.6M)
2014-11-19T12:03:55.25-0500 [API]     OUT Updated app with guid bd582ccf-67d9-4c5d-b6c3-63a0056aead7 ({"state"=>"STARTED"})
2014-11-19T12:03:55.38-0500 [STG]     OUT -------> Buildpack version 1.1.3
2014-11-19T12:03:55.59-0500 [STG]     OUT -----> Compiling Ruby/Rails
2014-11-19T12:03:56.73-0500 [STG]     OUT -----> Using Ruby version: ruby-2.1.2
2014-11-19T12:03:56.89-0500 [STG]     OUT -----> Installing dependencies using 1.6.3
2014-11-19T12:03:57.27-0500 [STG]     OUT        Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
2014-11-19T12:03:58.24-0500 [STG]     OUT        Fetching source index from https://rubygems.org/
2014-11-19T12:04:43.50-0500 [STG]     OUT        Fetching source index from https://rails-assets.org/
2014-11-19T12:04:47.80-0500 [STG]     OUT        Fetching git://github.com/rails-api/active_model_serializers.git
2014-11-19T12:04:48.76-0500 [STG]     OUT        Fetching git://github.com/twbs/bootstrap-sass.git
2014-11-19T12:04:50.02-0500 [STG]     OUT        Fetching git://github.com/reactjs/react-rails.git
2014-11-19T12:05:12.91-0500 [STG]     OUT        Using json 1.8.1
2014-11-19T12:05:13.69-0500 [STG]     OUT        Installing minitest 5.4.1
2014-11-19T12:05:13.99-0500 [STG]     OUT        Installing i18n 0.6.11
2014-11-19T12:05:14.11-0500 [STG]     OUT        Installing rake 10.3.2
2014-11-19T12:05:14.67-0500 [STG]     OUT        Installing builder 3.2.2
2014-11-19T12:05:14.87-0500 [STG]     OUT        Installing erubis 2.7.0
2014-11-19T12:05:14.90-0500 [STG]     OUT        Installing thread_safe 0.3.4
2014-11-19T12:05:15.59-0500 [STG]     OUT        Installing arel 5.0.1.20140414130214
2014-11-19T12:05:15.67-0500 [STG]     OUT        Installing mime-types 1.25.1
2014-11-19T12:05:16.21-0500 [STG]     OUT        Installing rack 1.5.2
2014-11-19T12:05:16.36-0500 [STG]     OUT        Installing analytics-ruby 1.0.0
2014-11-19T12:05:16.85-0500 [STG]     OUT        Installing uuidtools 2.1.4
2014-11-19T12:05:16.96-0500 [STG]     OUT        Installing mini_portile 0.5.2
2014-11-19T12:05:17.81-0500 [STG]     OUT        Installing thor 0.19.1
2014-11-19T12:05:18.37-0500 [STG]     OUT        Installing sass 3.2.19
2014-11-19T12:05:18.44-0500 [STG]     OUT        Installing multi_xml 0.5.5
2014-11-19T12:05:18.84-0500 [STG]     OUT        Installing timers 1.1.0
2014-11-19T12:05:19.07-0500 [STG]     OUT        Installing multi_json 1.10.1
2014-11-19T12:05:19.50-0500 [STG]     OUT        Installing bcrypt-ruby 3.1.2
2014-11-19T12:05:19.69-0500 [STG]     OUT        Installing multipart-post 2.0.0
2014-11-19T12:05:20.74-0500 [STG]     OUT        Installing orm_adapter 0.5.0
2014-11-19T12:05:20.83-0500 [STG]     OUT        Installing devise-i18n 0.10.3
2014-11-19T12:05:21.49-0500 [STG]     OUT        Installing hashie 2.0.5
2014-11-19T12:05:21.96-0500 [STG]     OUT        Installing excon 0.31.0
2014-11-19T12:05:22.13-0500 [STG]     OUT        Installing execjs 2.2.0
2014-11-19T12:05:22.31-0500 [STG]     OUT        Installing formatador 0.2.4
2014-11-19T12:05:22.66-0500 [STG]     OUT        Installing net-ssh 2.8.0
2014-11-19T12:05:22.85-0500 [STG]     OUT        Installing hike 1.2.3
2014-11-19T12:05:23.28-0500 [STG]     OUT        Installing htmlentities 4.3.2
2014-11-19T12:05:24.04-0500 [STG]     OUT        Installing httpclient 2.3.4.1
2014-11-19T12:05:24.26-0500 [STG]     ERR
2014-11-19T12:05:24.67-0500 [API]     ERR Encountered error: Staging error: failed to stage application:
2014-11-19T12:05:24.67-0500 [API]     ERR Script exited with status 255
```

## Let's figure out what happened

Exit status 255? What does that mean?

Aside from the fact that we encountered a staging error, we don't know too much more. So let's try starting up Cloud Focker and taking a look:

```
$ vagrant up --provider virtualbox
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Box 'cloudfocker-0.0.2-amd64' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Adding box 'cloudfocker-0.0.2-amd64' (v0) for provider: virtualbox
    default: Downloading: https://s3.amazonaws.com/cloudfocker/vagrantboxes/cloudfocker-0.0.2-vbox.box
==> default: Successfully added box 'cloudfocker-0.0.2-amd64' (v0) for 'virtualbox'!
==> default: Importing base box 'cloudfocker-0.0.2-amd64'...
==> default: Matching MAC address for NAT networking...
==> default: Setting the name of the VM: cloudfocker_default_1416419700375_47493
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 8080 => 8080 (adapter 1)
    default: 22 => 2222 (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /Users/quinn/Development/Books/AppsForBook/cloudfocker

$ vagrant ssh
Welcome to Ubuntu 14.04 LTS (GNU/Linux 3.13.0-24-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
Last login: Tue Apr 22 19:47:09 2014 from 10.0.2.2
vagrant@ubuntu-14:~$ fock docker
Checking Docker version
Client API version: 1.15
Go version (client): go1.3
OS/Arch (client): linux/amd64
Server version: 1.3.0
Server API version: 1.15
Go version (server): go1.3.3
Git commit (server): c78088f

vagrant@ubuntu-14:~$ fock this
Bootstrapping Docker setup - this will take a few minutes...
Downloading from https://s3.amazonaws.com/blob.cfblob.com/fee97b71-17d7-4fab-a5b0-69d4112521e6
f4608718ec895821ef08c6e391d34b3be8eb6806e4b856af105799a61d378ea8

vagrant@ubuntu-14:~$ fock add-buildpack https://github.com/cloudfoundry/ruby-buildpack
Downloading buildpack...
Cloning into 'ruby-buildpack'...
remote: Counting objects: 448, done.
remote: Compressing objects: 100% (348/348), done.
remote: Total 448 (delta 66), reused 392 (delta 63)
Receiving objects: 100% (448/448), 12.05 MiB | 1.90 MiB/s, done.
Resolving deltas: 100% (66/66), done.
Checking connectivity... done.
Submodule 'buildpack-packager' (https://github.com/cf-buildpacks/buildpack-packager.git) registered for path 'buildpack-packager'
Cloning into 'buildpack-packager'...
remote: Counting objects: 99, done.
remote: Compressing objects: 100% (51/51), done.
remote: Total 99 (delta 26), reused 92 (delta 24)
Unpacking objects: 100% (99/99), done.
Checking connectivity... done.
Submodule path 'buildpack-packager': checked out 'fec9c4598a6496fb9fa02c288076dc120754b0b5'
Downloaded buildpack.
```

Note that we added the Ruby buildpack since this is a Ruby app. To keep my test app separate from the sample apps provided with Cloud Focker, I created an `apps` directory and cloned Helpful.io there:

```
vagrant@ubuntu-14:/vagrant/sample-apps/apps$ git clone https://github.com/asm-helpful/helpful-web.git
Cloning into 'helpful-web'...
remote: Counting objects: 16357, done.
remote: Total 16357 (delta 0), reused 0 (delta 0)
Receiving objects: 100% (16357/16357), 5.80 MiB | 237.00 KiB/s, done.
Resolving deltas: 100% (11002/11002), done.
Checking connectivity... done.

vagrant@ubuntu-14:/vagrant/sample-apps/apps$ cd helpful-web/
vagrant@ubuntu-14:/vagrant/sample-apps/apps/helpful-web$ fock up
Starting the CloudFocker container...
Running Buildpacks...
-------> Buildpack version 1.1.4
-----> Compiling Ruby/Rails
-----> Using Ruby version: ruby-2.1.2
-----> Installing dependencies using 1.6.3
       Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
       Fetching source index from https://rubygems.org/
       Fetching source index from https://rails-assets.org/
       Fetching git://github.com/rails-api/active_model_serializers.git
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Retrying git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a" due to error (2/3): Bundler::Source::Git::GitCommandError Git error: command `git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"` in directory /app has failed.
       If this error persists you could try removing the cache directory '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Retrying git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a" due to error (3/3): Bundler::Source::Git::GitCommandError Git error: command `git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"` in directory /app has failed.
       If this error persists you could try removing the cache directory '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Git error: command `git clone --no-checkout --quiet
       "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669"
       "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"`
       in directory /app has failed.
       If this error persists you could try removing the cache directory
       '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
       Bundler Output: Fetching source index from https://rubygems.org/
       Fetching source index from https://rails-assets.org/
       Fetching git://github.com/rails-api/active_model_serializers.git
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Retrying git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a" due to error (2/3): Bundler::Source::Git::GitCommandError Git error: command `git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"` in directory /app has failed.
       If this error persists you could try removing the cache directory '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Retrying git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a" due to error (3/3): Bundler::Source::Git::GitCommandError Git error: command `git clone --no-checkout --quiet "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669" "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"` in directory /app has failed.
       If this error persists you could try removing the cache directory '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
       fatal: unable to look up current user in the passwd file: no such user
       fatal: The remote end hung up unexpectedly
       Git error: command `git clone --no-checkout --quiet
       "/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669"
       "/app/vendor/bundle/ruby/2.1.0/bundler/gems/active_model_serializers-2e31a141252a"`
       in directory /app has failed.
       If this error persists you could try removing the cache directory
       '/app/vendor/bundle/ruby/2.1.0/cache/bundler/git/active_model_serializers-f64033aa5d1f5245dd63d58130a3c3f3859ed669'
 !
 !     Failed to install gems via Bundler.
 !
Started the CloudFocker container.
Deleting the CloudFocker container...
cloudfocker-staging
Deleted container.
2014/11/19 20:02:39  Staging failed - have you added a buildpack for this type of application?
```
