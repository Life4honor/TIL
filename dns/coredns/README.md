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