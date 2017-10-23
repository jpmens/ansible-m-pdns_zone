PDNS_ZONE
=========

Lists, creates and deletes zones (a.k.a domains) of type master or slave in an [PowerDNS Authoritative Server](https://doc.powerdns.com/authoritative) using its RESTful API.
By default the `/etc/powerdns/pdns.conf` file is consulted to retrieve the `api_key`, `api_host`, and `api_port`.

Requirements
------------

Ansible 2.0+ and an installation of the PowerDNS Authoritative Server 4.0+ with the API enabled.

Options
-------

### `action`

Mandatory, the action to perform with the API. Possible values:

  * `delete`: the `zone` is removed.
  * `slave`: a slave zone is created
  * `master`: a master zone is created
  * `list`: an array of zone names / kinds is returned.

### `api_host`

The hostname / IP address of the PowerDNS API.
This is read from `pdnsconf` by default (the `webserver-address` configuration key) and if not found there the default is `127.0.0.1`.

### `api_key`

The PowerDNS 'API key' which, by default is read from `pdnsconf` (the `api-key` configuration key)..

### `api_port`

The TCP port number of the PowerDNS API which on `api_host`.
By default is read from `pdnsconf` (the `webserver-port` configuration key) and if not found there the default is `8081`.

### `comment`

A comment to add to the 'comments' table when `action=master` or `action=slave`.
[Default: 'Ansible-managed']

### `masters`

A comma-separated list of the addresses or `address:port` of the master server(s) for a slave zone.
This parameter is required for `action=slave`.
[Default: None]

### `nsset`

A comma-separated list of NS 'names' for a master zone (required for `action=master`).
Each element in the list will become a name server for the specified zone, configured with the specified `ttl`.
[Default: None]

### `pdnsconf`

The path to the PowerDNS configuration file from which `api_key`, `api_host`, and `api_port` are read.
This parameter can be omitted if you want to specify the three values manually.
Failure to read the file is silently ignored.
[Default: '/etc/powerdns/pdns.conf']

### `soa`

The 'rdata' for the SOA resource record.
This parameter is required for `action=master`.
[Default: None]

### `ttl`
The TTL for the SOA and NS record sets for `action=master`.
[Default: 86400]

### `zone`
The zone name, mandatory except with `action=list`.
If it is set and `action=list`, `zone` may contain a shell-style glob pattern to list only zones which match said pattern, e.g. `zone=*.org`.
[Default: None]

**Note**:  It is not possible to convert a zone from slave to master or vice versa.

EXAMPLES
--------

```yaml
- name: Create a slave zone; obtain config from specific file
  action: pdns_zone zone="example.org"
            action=slave
            masters="127.0.0.2:5301"
            pdnsconf={{pdnsconf}}
```

```yaml
- name: Delete all zones (master or slave) contained in the "zonelist" file
  action: pdns_zone zone={{ item }}
          action=delete
          api_key={{ api_key }}
          api_host={{ api_host }}
          api_port={{ api_port }}
   with_lines: cat zonelist
```

```yaml
- name: Create a master zone with 3 NS records
  action: pdns_zone zone="example.com"
          action=master
          soa="ns.example.net hostmaster.example.com 1 1800 900 604800 3602"
          nsset="ns1.example.net,ns.example.com,xo.example.org"
          api_key={{ api_key }}
          api_host={{ api_host }}
          api_port={{ api_port }}

```
