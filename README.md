# Transip cookbook
[![Gitter](https://badges.gitter.im/Join Chat.svg)](https://gitter.im/kabisa-cookbooks/transip?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Cookbook Version](https://img.shields.io/cookbook/v/transip.svg?style=flat)][transip]
[![Build Status](http://img.shields.io/travis/kabisa-cookbooks/transip.svg?style=flat)][travis]

Provides Transip API LWRPs.

[transip]: https://supermarket.getchef.com/cookbooks/transip
[travis]: http://travis-ci.org/kabisa-cookbooks/transip

## Requirements

### Chef version

* When using Chef < `12`, this cookbook will *only* work if you set
  `no_lazy_load` to `true` in your Chef config file. In Chef 12 and up, [this
  configuration is set to `true` by default][no_lazy_load].

* When using `chef-client` < `11.14.0.rc.2`, [cookbook files are downloaded
  sequential][sequential], which is *wicked slow* when using this cookbook, due
  to the included gem sources/binaries. I'd advice upgrading chef-client, or not
  using this cookbook really...

[no_lazy_load]: https://github.com/opscode/chef/blob/b75e1de72453e20312dcdc1ea1a480c048ee59a9/RELEASE_NOTES.md#changed-no_lazy_load-config-default-to-true
[sequential]: https://tickets.opscode.com/browse/CHEF-4423

### Platforms

**NOTE** that platform dependencies are strictly enforced. Using this cookbook
on any other platform (version) will abort your Chef run.

* ubuntu 12.04
* ubuntu 14.04
* debian 7.6

### Dependencies

* [transip gem][] by @joost (vendored inside cookbook)

[transip gem]: https://github.com/joost/transip

## Attributes

You can define default values for each LWRP by using the `node['transip']`
scope. See the [default][] attribute file for configuration variables and
documentation.

[default]: attributes/default.rb

## Recipes

*none*

## LRWPs

### transip_dns_entry

#### supports

* `create`
* `create_if_missing`
* `delete`

#### examples

```ruby
transip_dns_entry 'www' do
  action       :create
  domain       'kabisa.nl'
  content      '@'
  type         'CNAME'
  ttl          3600
  username     'kabisa'
  private_key  'your-private-key-here'
  proxy        '192.168.0.1'
  whitelist_ip '33.33.33.100'
end
```

```ruby
default['transip']['username'] = 'kabisa'
default['transip']['private_key'] = 'your-private-key-here'
default['transip']['domain'] = 'kabisa.nl'

transip_dns_entry 'www' do
  action :delete
end
```

some notes:

* all of the above options can have a default set (like
  `node['transip']['whitelist_ip']`) and then be omitted from the above example
* whitelist IPs need to be added in the API backend, and you need to make calls
  from that IP. For this, you can use the proxy option.
* for more details, see the original [transip gem][] by @joost

## compiling gems

All required gems are bundled with the cookbook. Since we require nokogiri, we
have to compile nokogiri for the right platform:

```bash
bin/kitchen converge --concurrency
```

This will compile the gems and add them to `files/<platform>`. See above for
supported platform versions.

Note that the compiled gems are *not* included in the Git repository by default.
You will have to clone the repository and run the above `kitchen` command to get
these gems.

## License and Author

Author:: Jean Mertz (<jean@mertz.fm>)

Copyright 2014, Kabisa ICT

Licensed under the MIT License (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://opensource.org/licenses/MIT

Unless required by applicable law or agreed to in writing, software distributed
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.
