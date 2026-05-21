### syn flooding

```
alert tcp any any -> $HOME_NET $HTTP_PORTS (
    msg:"SYN Flooding";
    flags:S;
    detection_filter:track by_src, count 100, seconds 3;
    rev:1;
)
```

### udp flooding

```
alert udp any any -> $HOME_NET any (
    msg:"UDP Flooding";
    detection_filter:track by_src, count 400, seconds 5;
    rev:1;
)
```

### tcp flooding

```
alert tcp any any -> $HOME_NET $HTTP_PORTS (
    msg:"TCP Flooding";
    detection_filter:track by_src, count 1000, seconds 5;
    rev:1;
)
```
