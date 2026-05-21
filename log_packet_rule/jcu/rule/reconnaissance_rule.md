### nmap

```
alert tcp any any -> $HOME_NET any (
    msg:"TCP Port Scan";
    flags:S;
    detection_filter:track by_src, count 20, seconds 5;
    rev:1;
)
```

### whatweb

```
alert tcp any any -> $HOME_NET $HTTP_PORTS (
    msg:"WhatWeb";
    content:"whatweb";
    nocase;
    http_user_agent;
    rev:1;
)
```

### gobuster

```
alert tcp any any -> $HOME_NET $HTTP_PORTS (
    msg:"Gobuster";
    content:"gobuster";
    nocase;
    http_user_agent;
    rev:1;
)
```
