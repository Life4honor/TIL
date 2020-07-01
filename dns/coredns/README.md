# DNS Configuration

## CoreDNS installation

### Docker

```sh
# ENTRYPOINT: "/coredns"
$ docker pull coredns/coredns
```

### Run CoreDNS

```
# Run CoreDNS on port 1053
$ ./coredns -dns.port=1053

# Query from a different terminal window
$ dig @localhost -p 1053 a whoami.example.org
```

## Plugins

`plugin.cfg` defines the execution order of plugins. `Plugin Chain` always happens in the same order as configured.

[plugin.cfg](https://github.com/coredns/coredns/blob/master/plugin.cfg) is the sample configuration file, located on official github repo.

### Query is Processed

The plugin looks up a response and sends it back to the client. The query processing stops here, no next plugin is called.
 - `whoami`

### Query is Not Processed

The plugin simply calls the next plugin in the chain. If plugin was the last one, CoreDNS will return SERVFAIL back to the client.

### Query is Processed With Fallthrough

The plugin looks up a response first. If it finds the proper response, it returns that.  If not, it calls the next plugins when `fallthrough` is provided

- `hosts`

### Query is Processed With a Hint

The plugin will process a query, and always call the next plugin with a hint that allows it to return the proper response.

- `prometheus`

## Corefile
### Configuration with `file` plugin
`Corefile`
```
example.org {
    file db.example.org
    log
}
```

`db.example.org`
```
$ORIGIN example.org.
@	3600 IN	SOA sns.dns.icann.org. noc.dns.icann.org. (
				2017042745 ; serial
				7200       ; refresh (2 hours)
				3600       ; retry (1 hour)
				1209600    ; expire (2 weeks)
				3600       ; minimum (1 hour)
				)

	3600 IN NS a.iana-servers.net.
	3600 IN NS b.iana-servers.net.

www     IN A     127.0.0.1
        IN AAAA  ::1
```
Defines a name `www.example.org.` with two addresses, `127.0.0.1` and (the IPv6) `::1`.

[DNS zone file tutorial](http://www.microhowto.info/tutorials/dns_zone_file.html)


### Configuration with `forward` plugin

`Corefile`
```
. {
    forward . 8.8.8.8
    log
}
```

`Corefile` with multiple server blocks
```
example.org {
    forward . 8.8.8.8
    log
}

. {
    forward . /etc/resolv.conf
    log
}
```
If there are multiple Servers configured that listen on the queried port, it will check which one has the most specific zone for this query.