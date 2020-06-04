# DNS Configuration
DNS Server: `<DNS Server>`

## CoreDNS installation

### Docker

```
$ docker pull coredns/coredns
```
> **ENTRYPOINT**: ["/coredns"]

### Run CoreDNS

```
# Run CoreDNS on port 1053
$ ./coredns -dns.port=1053

# Query from a different terminal window
$ dig @localhost -p 1053 a whoami.example.org
```

## Plugins


## Corefile
