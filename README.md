# letsencrypt-plugin [![Build Status](https://travis-ci.org/lgromanowski/letsencrypt-plugin.svg?branch=master)](https://travis-ci.org/lgromanowski/letsencrypt-plugin) [![Gem Version](https://badge.fury.io/rb/letsencrypt_plugin.svg)](https://badge.fury.io/rb/letsencrypt_plugin)
`letsencrypt-plugin` is a Ruby on Rails helper for [Let's Encrypt](https://letsencrypt.org/) service for retrieving SSL certificates (without using sudo, like original letsencrypt client does). It uses [acme-client](https://github.com/unixcharles/acme-client) gem for communication with Let's Encrypt server.

## Installation

Install the letsencrypt_plugin gem, either via Bundler or Rubygems:

* Via Bundler

  Add the following line to your application's Gemfile:
  ```ruby
  gem 'letsencrypt_plugin'
  ```
  And then execute:
  ```bash
  $ bundle install
  ```

* Via Rubygems

  Execute:
  ```bash
  $ gem install letsencrypt_plugin
  ```



Create a configuration file `#{Rails.root}/config/letsencrypt_plugin.yml` (template below):
```yaml
default: &default
  endpoint: "https://acme-v01.api.letsencrypt.org/"
  email: "your@email.address"
  domain: "example.com"
  private_key: "key/keyfile.pem"                            # in Rails.root
  output_cert_dir: "certificates"                           # in Rails.root
  
production:
  <<: *default
  
development:
  <<: *default

test:
  <<: *default
```


The plugin requires a SQL database to store temporary information.  Your app must already be configured to have access to a SQL db (e.g. Postgres, MySQL, or sqlite3).   The following commands will create a database table named `letsencrypt_plugin_challenges`:
```bash
$ rake letsencrypt_plugin:install:migrations
$ rake db:migrate # or on production, $ rake db:migrate RAILS_ENV=production
```


Create some empty files within the Rails.root as placeholders:
```bash
$ mkdir key
$ mkdir certificates
$ touch key/.gitkeep
$ touch certificates/.gitkeep
```

Create the file `key/keyfile.pem`:
  ** HOW? **


Enable letsencrypt_plugin to respond to to ACME requests.  Edit `config/routes.rb`:
```ruby
Rails.application.routes.draw do
  mount LetsencryptPlugin::Engine, at: "/"  # It must be at root level

  # Other routes...
end
```

## Usage

```$ rake letsencrypt_plugin RAILS_ENV=production```

Output:
```bash
I, [2015-12-06T17:28:15.582308 #25931]  INFO -- : Loading private key...
I, [2015-12-06T17:28:15.582592 #25931]  INFO -- : Trying to register at Let's Encrypt service...
I, [2015-12-06T17:28:16.381682 #25931]  INFO -- : Already registered.
I, [2015-12-06T17:28:16.381749 #25931]  INFO -- : Sending authorization request...
I, [2015-12-06T17:28:16.646616 #25931]  INFO -- : Storing challenge information...
I, [2015-12-06T17:28:18.193827 #25931]  INFO -- : Waiting for challenge status...
I, [2015-12-06T17:28:21.643566 #25931]  INFO -- : Creating CSR...
I, [2015-12-06T17:28:22.173471 #25931]  INFO -- : Saving certificates and key...
I, [2015-12-06T17:28:22.174312 #25931]  INFO -- : Certificate has been generated.
```
If everything goes correctly than in `output_cert_dir` directory there should be four files:
- domain.name-cert.pem - Domain certificate
- domain.name-chain.pem - Chained certificate
- domain.name-fullchain.pem - Full chain of certificates
- domain.name-key.pem - Domain certificate key


## Bugs, issues, feature requests?

If you encounter a bug, issue or you have feature request please submit it in [issue tracker](https://github.com/lgromanowski/letsencrypt-plugin/issues). 

## License

[MIT License](http://opensource.org/licenses/MIT)
