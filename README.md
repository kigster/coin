# Coin

## Memcached? We don't need no stinking Memcached.

... Well, you might depending upon your specific needs.
But be sure to have a look at Coin before you reach for the sledgehammer.

Coin is an absurdly simple in memory object caching system written in Ruby.

## Why Coin?

* No configuration required
* No added complexity to your stack
* Small footprint (under 200 lines)
* Simple API

Coin uses [Distributed Ruby (DRb)](http://pragprog.com/book/sidruby/the-druby-book)
to create a simple in memory caching server that addresses many of the same needs as Memcached
and other similar solutions.

## Quick Start

Installation

```bash
$ gem install coin
```

Basic Usage

```ruby
require "coin"
Coin.write :foo, true
Coin.read :foo # => true
```

## Next Steps

Examples of more advanced usage.

```ruby
require "coin"

# read and/or assign a default value in a single step
Coin.read(:bar) { true } # => true

# write data with an explicit expiration (in seconds)
# this example expires in 5 seconds (default is 300)
Coin.write :bar, true, 5
sleep 5
Coin.read :bar # => nil

# delete an entry
Coin.write :bar, true
Coin.delete :bar
Coin.read :bar # => nil

# read and delete in a single step
Coin.write :bar, true
Coin.read_and_delete :bar # => true
Coin.read :bar # => nil

# determine how many items are in the cache
10.times do |i|
  Coin.write "key#{i}", true
end
Coin.length # => 10

# clear the cache
Coin.clear
Coin.length # => 0
```

## Deep Cuts

Coin automatically starts a DRb server that hosts the Coin::Vault.
You can take control of this behavior if needed.

```ruby
require "coin"

# configure the port that the DRb server runs on (default is 8955)
Coin.port = 8080

# configure the URI that the DRb server runs on (defaults to druby://localhost:PORT)
Coin.uri = "druby://10.0.0.100:8080"

# access the DRb server exposing Coin::Vault
Coin.server # => #<Coin::Vault:0x007fe182852e18>

# determine if the server is running
Coin.server_running? # => true

# determine the pid of the server process
Coin.pid # => "63299"

# stop the server
Coin.stop_server # => true

# start the server
Coin.start_server # => true

# start the server forcing a restart if the server is already running
Coin.start_server true # => true
```

## Run the Tests

```bash
$ gem install coin
$ gem unpack coin
$ cd coin-VERSION
$ bundle
$ mt
```

## Notes

Coin's default behavior launches a single DRb server that provides
shared access across all processes on a **single machine**.

_It should be relatively simple to update Coin to work across multiple machines,
so keep an eye peeled for this feature in the future._

### Coin Processes

![Coin Processes](https://www.lucidchart.com/publicSegments/view/50b8299a-7c5c-4175-8121-6b190a7a0a70/image.png)
