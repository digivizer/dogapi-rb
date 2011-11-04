# Ruby client for Datadog API v1.2.3

The Ruby client is a library suitable for inclusion in existing Ruby projects or for development of standalone scripts. It provides an abstraction on top of Datadog's raw HTTP interface for reporting events and metrics.

# What's new?

## v1.2.x

* You can now manage host tags
* You can now get event details and query the stream in addition to posting events
* Functionality relating to events with a duration has been deprecated
* The underlying clients have been updated to use Datadog's new [https://github.com/DataDog/dogapi/wiki](public HTTP API)

## v1.1.x

* You do not need to use environment variables anymore to use the client.

# Installation

## From Source

Available at: https://github.com/DataDog/dogapi-rb

    $ cd dogapi-rb
    $ rake gem
    $ gem install pkg/dogapi-*.gem

## Using RubyGems

Gem page: https://rubygems.org/gems/dogapi

    $ gem install dogapi

# Usage

## How to find your API and application keys

Go to [https://app.datadoghq.com/account/settings](your setup page).

## A word about hosts and devices

Events and metric data points can be attached to hosts
to take advantage of automatic tagging with the host's tags.

If you want to attach events and points to a specific device
on a host, simply specify the device when calling emit functions.

## Submit an event to Datadog

```ruby
require 'rubygems'
require 'dogapi'

api_key = "abcdef123456"

# submitting events doesn't require an application_key, so we don't bother setting it
dog = Dogapi::Client.new(api_key)

dog.emit_event(Dogapi::Event.new('Testing done, FTW'), :host => "my_host", :tags => ["tag1", "tag2", "tag3"])
```

## Tag a host in Datadog

```ruby
require 'rubygems'
require 'dogapi'

api_key = "abcdef123456"
application_key = "fedcba654321"

dog = Dogapi::Client.new(api_key, application_key)

dog.add_tags("my_host", ["tagA", "tagB"])
```

## Submit a metric to Datadog

You want to track a new metric called `some.metric.name` and have just sampled it from `my_device` on `my_host`.
Its value is 50. Here is how you submit the value to Datadog.

```ruby
require 'rubygems'
require 'dogapi'

api_key = "abcdef123456"

# submitting events doesn't require an application_key, so we don't bother setting it
dog = Dogapi::Client.new(api_key)

dog.emit_point('some.metric.name', 50.0, :host => "my_host", :device => "my_device")
```

Let us now assume that you have sampled the metric multiple times and you would like to submit the results.
You can use the `emit_points` method (instead of `emit_point`). Since you are submitting more than one
data point you will need to pass a list of `Time`, `float` pairs, instead of a simple `float` value.

```ruby
require 'rubygems'
require 'dogapi'

# Actual sampling takes place
t1 = Time.now
val1 = 50.0

# some time elapses
t2 = Time.now
val2 = 51.0

# some more time elapses
t3 = Time.now
val3 = -60.0

api_key = "abcdef123456"

dog = Dogapi::Client.new(api_key)

dog.emit_points('some.metric.name', [[t1, val1], [t2, val2], [t3, val3]], :host => "my_host", :device => "my_device")
```
