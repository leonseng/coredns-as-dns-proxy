# CoreDNS as DNS proxy

> Based on [https://dev.to/robbmanes/running-coredns-as-a-dns-server-in-a-container-1d0](https://dev.to/robbmanes/running-coredns-as-a-dns-server-in-a-container-1d0)

Deploying CoreDNS as a DNS proxy, with ability to add custom DNS entries.

To start the container listening on port `1053`:
```
docker run -d --name coredns --rm \
  --volume=$(pwd)/Corefile:/root/Corefile \
  --volume=$(pwd)/my.domain:/root/my.domain \
  -p 1053:53/udp \
  coredns/coredns -conf /root/Corefile
```

Test the custom record defined in the [my.domain](/my.domain) file:
```
$ dig @localhost -p 1053 test.my.domain +short
10.0.0.1
```

## Explanation

CoreDNS is configured to forward DNS queries to `8.8.8.8` and `9.9.9.9`, and can intercept queries by configuring additional zones in the [Corefile](./Corefile) as such:

```
my.domain:53 {
    file /root/my.domain
    log
    errors
}
```

where the [CoreDNS `file` plugin](https://coredns.io/plugins/file/) references a file containing RFC 1035 styled data, as seen in [my.domain](/my.domain).
