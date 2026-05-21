### ShellShock

```
alert tcp any any -> $HOME_NET $HTTP_PORTS (
    msg:"ShellShock";
    content:":;};";
    rev:1;
)
```
