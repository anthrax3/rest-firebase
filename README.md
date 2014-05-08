# rest-firebase [![Build Status](https://secure.travis-ci.org/CodementorIO/rest-firebase.png?branch=master)](http://travis-ci.org/CodementorIO/rest-firebase)

by [Codementor][]

[Codementor]: https://www.codementor.io/

## LINKS:

* [github](https://github.com/CodementorIO/rest-firebase)
* [rubygems](https://rubygems.org/gems/rest-firebase)
* [rdoc](http://rdoc.info/projects/CodementorIO/rest-firebase)

## DESCRIPTION:

Firebase REST API client built on top of [rest-core][].

[rest-core]: https://github.com/godfat/rest-core

## FEATURES:

* Concurrent requests
* Streaming requests

## REQUIREMENTS:

### Mandatory:

* Tested with MRI (official CRuby), Rubinius and JRuby.
* gem [rest-core][]
* gem [httpclient][]
* gem [mime-types][]
* gem [timers][]

[httpclient]: https://github.com/nahi/httpclient
[mime-types]: https://github.com/halostatue/mime-types
[timers]: https://github.com/celluloid/timers

### Optional:

* gem json or yajl-ruby, or multi_json

## INSTALLATION:

``` shell
gem install rest-firebase
```

Or if you want development version, put this in Gemfile:

``` ruby
gem 'rest-firebase', :git => 'git://github.com/CodementorIO/rest-firebase.git',
                     :submodules => true
```

## SYNOPSIS:

Check out Firebase's
[REST API documentation](https://www.firebase.com/docs/rest-api.html)
for a complete reference.

``` ruby
require 'rest-firebase'

f = RestFirebase.new :site => 'https://SampleChat.firebaseIO-demo.com/',
                     :secret => 'secret',
                     :d => {:auth_data => 'something'},
                     :log_method => method(:puts),
                     :auth => false # Ignore auth for this example!

@reconnect = true

# Streaming over 'users/tom'
es = f.event_source('users/tom')
es.onopen   { |sock| p sock } # Called when connected
es.onmessage{ |event, data, sock| p event, data } # Called for each message
es.onerror  { |error, sock| p error } # Called whenever there's an error
# Extra: If we return true in onreconnect callback, it would automatically
#        reconnect the node for us if disconnected.
es.onreconnect{ |error, sock| p error; @reconnect }

# Start making the request
es.start

# Try to close the connection and see it reconnects automatically
es.close

# Update users/tom.json
p f.put('users/tom', :some => 'data')
p f.post('users/tom', :some => 'other')
p f.get('users/tom')
p f.delete('users/tom')

# Need to tell onreconnect stops reconnecting, or even if we close
# the connection manually, it would still try to reconnect again.
@reconnect = false

# Close the connection to gracefully shut it down.
es.close

# Refresh the auth by resetting it
f.auth = nil
```

## Concurrent HTTP Requests:

Inherited from [rest-core][], you can do concurrent requests quite easily.
Here's a very quick example of making two API calls at the same time.

``` ruby
require 'rest-firebase'
firebase = RestFirebase.new(:log_method => method(:puts))
puts "httpclient with threads doing concurrent requests"
a = [firebase.get('users/tom'), firebase.get('users/mom')]
puts "It's not blocking... but doing concurrent requests underneath"
p a.map{ |r| r['name'] } # here we want the values, so it blocks here
puts "DONE"
```

If you prefer callback based solution, this would also work:

``` ruby
require 'rest-firebase'
firebase = RestFirebase.new(:log_method => method(:puts))
puts "callback also works"
firebase.get('users/tom') do |r|
  p r['name']
end
puts "It's not blocking... but doing concurrent requests underneath"
firebase.wait # we block here to wait for the request done
puts "DONE"
```

For a detailed explanation, see:
[Advanced Concurrent HTTP Requests -- Embrace the Future][future]

[future]: https://github.com/godfat/rest-core#advanced-concurrent-http-requests----embrace-the-future

## Powered sites:

* [Codementor][]

## CHANGES:

* [CHANGES](CHANGES.md)

## CONTRIBUTORS:

* Lin Jen-Shin (@godfat)

## LICENSE:

Apache License 2.0

Copyright (c) 2014, Codementor

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
