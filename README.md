# How to setup WordPress on Heroku with the Heroku Buildpack for PHP

This will set up a fresh WordPress install on Heroku with the newly released [Heroku Buildpack for PHP](https://github.com/heroku/heroku-buildpack-php).

* `nginx` - Nginx for serving web content.
* `php` - PHP-FPM for process management.
* `wordpress` - Downloaded directly [from wordpress.org](http://wordpress.org/download/release-archive/).
* `MySQL` - ClearDB for the MySQL backend.
* `Sendgrid` - Sendgrid for the email backend.
* `MemCachier` - MemCachier for the memcached backend.

## Getting started

Initialize your new repository in an empty folder

	git init

Create your Heroku app

	heroku create -s cedar --buildpack https://github.com/heroku/heroku-buildpack-php -region eu

or on an existing app

	heroku config:set BUILDPACK_URL=https://github.com/heroku/heroku-buildpack-php



Before you push to Heroku make sure to add the following add-ons

	heroku addons:add cleardb
	heroku addons:add sendgrid
	heroku addons:add memcachier
	heroku addons:add scheduler
	heroku addons:add papertrail


Define your AWS keys for the AWS S3 Media Uploader plugin

	heroku config:set AWS_ACCESS_KEY_ID=123
	heroku config:set AWS_SECRET_ACCESS_KEY=123


Some default configurations. WP_CACHE=true will enable Batcache with the Memcachier addon.

	heroku config:set DISABLE_WP_CRON=true
	heroku config:set WP_CACHE=true

## Overview
```
└── public                 # Heroku webroot
    ├── content            # The wp-content directory. Renamed to content to stop confusion
    │   ├── plugins        # Plugins
    │   ├── mu-plugins     # Required plugins
    │   └── themes         # Your custom themes
    │      
    └── wp                 # Where the actual WordPress install will be installed by Composer
```

## Setup local development

Make sure you have Composer installed first

	composer install

Create a local .env file.

	CLEARDB_DATABASE_URL=mysql://root:123abc@127.0.0.1/my_wordpress_heroku_database_name
	
or install the heroku config plugin from https://github.com/ddollar/heroku-config and pull your environment variables from Heroku.

> NOTE: If you don't have a command-line mysql accessible and working, Mac/Homebrew users can brew install mysql and then follow the directions to have launchd start mysql at login. I believe the default username is root and the default password is blank.

Install PHP 5.5 on Mac OS X with Homebrew if you don't already have it installed.

	brew install --with-fpm php55

Follow the instructions in the output to complete the setup. Most importantly check your .bash_profile or .zshrc and make sure you've set your paths correctly.

	brew install php55-mcrypt

	brew install nginx

Open a new shell and run `php -v` and `php-fpm -v` and make sure they both read PHP 5.5… If you're still on PHP 5.4 then check your paths again. Make sure /usr/local/sbin is before /usr/sbin in your PATH:

> Mountain Lion comes with php-fpm pre-installed, to ensure you are using the brew version you need to make sure /usr/local/sbin is before /usr/sbin in your PATH:

  PATH="/usr/local/sbin:$PATH"

Add this below Heroku Toolbelt setting to swap the PHP you use on the command line.

	export PATH="$(brew --prefix homebrew/php/php55)/bin:$PATH"
