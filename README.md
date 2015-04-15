
```txt
> PDNS_ZONE

  Lists, creates and deletes zones (a.k.a domains) of type master or
  slave in an authoritative PowerDNS server using its RESTful API. By
  default the `/etc/powerdns/pdns.conf' file is consulted to retrieve
  the `api_key', `api_host', and `api_port'.

Options (= is mandatory):

= action
        The action to perform. If `action' equals `delete', the zone
        is removed. Otherwise, if `slave' a slave zone is created or
        if `master' a master zone is created. If action equals `list',
        an array of zone names / kinds is returned. (Choices: slave,
        master, delete, list) [Default: None]

= api_host
        The hostname / IP address of the PowerDNS API which, by
        default, is read from `pdns.conf' [Default: localhost]

= api_key
        The PowerDNS `API key' which, by default is read from
        `pdns.conf'. [Default: None]

= api_port
        The TCP port number of the PowerDNS API which on `api_host';
        by default is read from `pdns.conf' [Default: None]

- comment
        A comment to add to the `comments' table when state=`master'
        or `slave'. [Default: Ansible-managed]

- masters
        The name or address (or `address:port') of the master server
        for a slave zone. This parameter is required for
        action=`slave'. [Default: None]

- nsset
        A comma-separated list of NS `names' for a master zone
        (required for action=`master'. Each element in the list will
        become a name server for the specified zone, configured with
        the specified `ttl'. [Default: None]

- pdnsconf
        The path to the PowerDNS configuration file from which
        `api_key', `api_host', and `api_port' are read. This parameter
        can be omitted if you want to specify the three values
        manually. Failure to read the file is silently ignored.
        [Default: /etc/powerdns/pdns.conf]

- soa
        The `rdata' for the SOA resource record. This parameter is
        required for action=`master'. [Default: None]

- ttl
        The TTL for the SOA and NS record sets for action=`master'.
        [Default: 86400]

= zone
        The zone name, mandatory except when `action' is `list', and
        if it is, `zone' may contain a shell-style glob pattern to
        list only zones which match said pattern, e.g. `zone=*.org'.
        [Default: None]

Notes:  It is not possible to convert a zone from slave to master or vice
        versa. See also the [dnsupdate] module.

Requirements:  urllib2

EXAMPLES:
- name: Create a slave zone; obtain config from specific file
  action: pdns_zone zone="example.org"
            action=slave
            masters="127.0.0.2:5301"
            pdnsconf={{pdnsconf}}

- name: Delete all zones (master or slave) contained in the "zonelist" file
  action: pdns_zone zone={{ item }}
          action=delete
          api_key={{ api_key }}
          api_host={{ api_host }}
          api_port={{ api_port }}
   with_lines: cat zonelist

- name: Create a master zone with 3 NS records
  action: pdns_zone zone="example.com"
          action=master
          soa="ns.example.net hostmaster.example.com 1 1800 900 604800 3602"
          nsset="ns1.example.net,ns.example.com,xo.example.org"
          api_key={{ api_key }}
          api_host={{ api_host }}
          api_port={{ api_port }}
```
