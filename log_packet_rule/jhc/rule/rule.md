
OS Command Injection
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"OS Command Injection - Shell Operator with OS Command"; pcre:"/(%3B|;|\|{1,2}|%7C{1,2}|`|%60|\$\()(\+|%20)?(id|whoami|uname|ifconfig|ip|cat|ls|pwd|wget|curl|nc|bash|sh|python|perl|php|nmap|ping|sleep|echo|rm|chmod|env|passwd|shadow)/i"; sid:9000001; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"OS Command Injection - Sensitive File Path"; pcre:"/\/etc\/(passwd|shadow|hosts|crontab)|\/proc\/self|\/dev\/(tcp|udp)|\/bin\/(bash|sh)/i"; sid:9000002; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"OS Command Injection - Reverse Shell Pattern"; pcre:"/nc\s+-e|bash\s+-i\s*>&|\/dev\/tcp\/|python\s+-c.{0,60}socket|perl\s+-e.{0,60}socket|mkfifo|mknod/i"; sid:9000003; rev:2;)
```
ModSecurity
```
SecRule ARGS \
    "@rx (?:%3B|;|\|{1,2}|%7C{1,2}|`|%60|\$\(|%24%28)\s*(?:id|whoami|uname|ifconfig|cat|ls|pwd|wget|curl|nc|bash|sh|python|perl|php|nmap|ping|sleep|echo|rm|chmod|passwd|env)" \
    "id:9000001,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'OS Command Injection - Shell Operator with OS Command'"

SecRule ARGS \
    "@rx (?:\/etc\/(?:passwd|shadow|hosts|crontab)|\/proc\/self|\/dev\/(?:tcp|udp)|\/bin\/(?:bash|sh))" \
    "id:9000002,phase:2,log,pass,\
    t:urlDecodeUni,\
    msg:'OS Command Injection - Sensitive File Path'"

SecRule ARGS \
    "@rx (?:nc\s+-e|bash\s+-i\s*>&|\/dev\/tcp\/|python\s+-c.{0,60}socket|perl\s+-e.{0,60}socket|mkfifo|mknod.*nc)" \
    "id:9000003,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'OS Command Injection - Reverse Shell Pattern'"
```

OS Command Injection Blind
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"OS Command Injection Blind - Time Delay"; pcre:"/(%3B|;|\|{1,2}|`|\$\()(\+|%20)?(?:sleep|ping\s+-c|ping\+-c|timeout|usleep)\s*[\d%+]/i"; sid:9000011; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"OS Command Injection Blind - OOB Callback"; pcre:"/(%3B|;|\|{1,2}|`|\$\()(\+|%20)?(?:curl|wget|nslookup|dig|ping|nc)(\+|%20)[^\s&]+\.(com|net|org|io|xyz|kr)/i"; sid:9000012; rev:2;)
```
ModSecurity
```
SecRule ARGS \
    "@rx (?:%3B|;|\|{1,2}|`|%60|\$\(|%24%28)\s*(?:sleep|usleep|timeout|ping\s+-c)\s*\d+" \
    "id:9000011,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'OS Command Injection Blind - Time Delay'"

SecRule ARGS \
    "@rx (?:%3B|;|\|{1,2}|`|%60|\$\(|%24%28)\s*(?:curl|wget|nslookup|dig|ping)\s+[a-z0-9\-\.]+\.(?:com|net|org|io|xyz)" \
    "id:9000012,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'OS Command Injection Blind - OOB Callback'"
```


SQL Injection
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi GET - OR/AND Logic Bypass"; pcre:"/(%27|')(%20|\+)(OR|AND)(%20|\+)(\d+=\d+|'[^']*'='[^']*'|true|false)/i"; sid:9000021; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi GET - Quote with Comment Termination"; pcre:"/(%27|')[^']*(--%20|%23|#|\/\*)/i"; sid:9000022; rev:2;)

```
ModSecurity
```
SecRule ARGS \
    "@rx (?:'|%27)\s*(?:OR|AND)\s+(?:\d+=\d+|'[^']*'='[^']*'|\d+\s*[<>]=?\s*\d+)" \
    "id:9000021,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi GET - OR/AND Logic Bypass'"

```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi POST - OR/AND and UNION Injection"; pcre:"/(%27|')((%20|\+)(OR|AND)(%20|\+)\d+=\d+|(%20|\+)UNION(%20|\+)(ALL(%20|\+))?SELECT)/i"; sid:9000031; rev:2;)

```
ModSecurity
```
SecRule REQUEST_BODY \
    "@rx (?:(?:'|%27)\s*(?:OR|AND)\s+\d+=\d+|UNION\s+(?:ALL\s+)?SELECT)" \
    "id:9000031,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi POST - OR/AND and UNION Injection'"

```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi Stored - SQL Injection in User-Agent"; pcre:"/User-Agent[^\r\n]*('|%27).{0,100}(SELECT|UNION|INSERT|UPDATE|DELETE|DROP|SLEEP|OR\s+1=1)/i"; sid:9000051; rev:2;)

```
ModSecurity
```
SecRule REQUEST_HEADERS:User-Agent \
    "@rx (?:'|%27).{0,100}(?:SELECT|UNION|INSERT|UPDATE|DELETE|DROP|SLEEP|BENCHMARK|concat\s*\(|information_schema)" \
    "id:9000051,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi Stored - SQL in User-Agent'"

```
 
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi Boolean Blind - AND with String Function"; pcre:"/('|%27).*(AND|OR).*(?:SUBSTRING|SUBSTR|MID|ASCII|ORD|CHAR|LENGTH)\s*\(/i"; sid:9000061; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi Boolean Blind - SUBSTRING on DB Meta Function"; pcre:"/(?:SUBSTRING|SUBSTR|MID)\s*\(\s*(?:database|version|user|schema|table_name|column_name)\s*\(/i"; sid:9000062; rev:2;)
```
ModSecurity
```
SecRule ARGS \
    "@rx (?:'|%27)\s*AND\s+.{0,50}(?:SUBSTRING|SUBSTR|MID|ASCII|ORD|CHAR|LENGTH)\s*\(" \
    "id:9000061,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi Boolean Blind - AND with String Function'"

SecRule ARGS \
    "@rx (?:SUBSTRING|SUBSTR|MID)\s*\(\s*(?:database|version|user|schema|table_name|column_name)\s*\(" \
    "id:9000062,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi Boolean Blind - SUBSTRING on DB Meta Function'"
```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi Time-Based - Delay Function"; pcre:"/('|%27).{0,100}(SLEEP\s*\(\s*\d+|BENCHMARK\s*\(\s*\d+|pg_sleep\s*\(\s*\d+|WAITFOR\s+DELAY\s+'[0-9:]+')/i"; sid:9000071; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"SQLi Time-Based - IF/CASE Conditional Sleep"; pcre:"/(?:IF|CASE\s+WHEN)\s*\(.{0,100}SLEEP\s*\(|IF\s*\(.{0,100}BENCHMARK\s*\(/i"; sid:9000072; rev:2;)
```
ModSecurity
```
SecRule ARGS \
    "@rx (?:'|%27)\s*(?:AND|OR)\s+.{0,50}(?:SLEEP\s*\(\s*\d+|BENCHMARK\s*\(\s*\d+|pg_sleep\s*\(\s*\d+|WAITFOR\s+DELAY)" \
    "id:9000071,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi Time-Based - Delay Function'"

SecRule ARGS \
    "@rx (?:IF|CASE\s+WHEN)\s*\(.{0,100}SLEEP\s*\(|IF\s*\(.{0,100}BENCHMARK\s*\(" \
    "id:9000072,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'SQLi Time-Based - IF/CASE Conditional Sleep'"
```

Broken Authentication — Password Attacks
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"Brute Force - Rapid Login Attempts"; pcre:"/(?:login|signin|auth|password|passwd|logon)=[^&]+/i"; detection_filter:track by_src, count 15, seconds 30; sid:9000081; rev:2;)

```
ModSecurity
```
SecAction \
    "id:9000082,phase:2,nolog,pass,\
    setvar:ip.login_count=+1,\
    expirevar:ip.login_count=30"

SecRule IP:login_count "@gt 15" \
    "id:9000083,phase:2,log,pass,\
    msg:'Brute Force - Login Threshold Exceeded'"
```
```

Session Management — Session ID in URL
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"Session Management - Session ID Exposed in URL"; pcre:"/[?&](?:PHPSESSID|JSESSIONID|ASPSESSIONID|sessid|session_id|sid|token|auth_token|access_token)=[a-zA-Z0-9\-_\.]{10,}/i"; sid:9000091; rev:2;)

```
ModSecurity
```
SecRule REQUEST_URI \
    "@rx [?&](?:PHPSESSID|JSESSIONID|ASPSESSIONID|sessid|session_id|auth_token|access_token)=[a-zA-Z0-9\-_\.]{10,}" \
    "id:9000091,phase:2,log,pass,\
    t:urlDecodeUni,\
    msg:'Session Management - Session ID Exposed in URL'"
```

XSS
Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"XSS Reflected GET - HTML Tag Injection"; pcre:"/(%3C|<)(?:script|img|svg|iframe|body|input|a|link|meta|object|embed|form|video|audio)(%3E|>|(%20|\+)[^>]*(%3E|>))/i"; sid:9000101; rev:2;)
```
ModSecurity
```
SecRule ARGS \
    "@rx (?:<|%3C)(?:script|img|svg|iframe|body|input|a\s|link|meta|object|embed|form)(?:\s|>|%20|%3E)" \
    "id:9000101,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS Reflected GET - HTML Tag Injection'"

```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"XSS Reflected POST - Script/Event in Body"; pcre:"/(<script[^>]*>|<img[^>]+onerror|<svg[^>]+onload|<iframe|<body[^>]+onload|javascript:|document\.cookie|document\.location)/i"; sid:9000111; rev:2;)
```
ModSecurity
```
SecRule REQUEST_BODY \
    "@rx (?:<script|<img[^>]+onerror|<svg[^>]+onload|<iframe|javascript\s*:)" \
    "id:9000111,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS Reflected POST - Script/Event in Body'"
```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"XSS Reflected Custom Header - Script in HTTP Header"; pcre:"/[A-Za-z0-9\-]+:\s*[^\r\n]*(<script|<img[^>]+onerror|<svg[^>]+onload|javascript:|<iframe)/i"; sid:9000121; rev:2;)
```
ModSecurity
```
SecRule REQUEST_HEADERS \
    "@rx (?:<script|%3Cscript|<img[^>]+onerror|<svg[^>]+onload|javascript\s*:)" \
    "id:9000121,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS Reflected Custom Header - Script in HTTP Header'"
```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"XSS User-Agent - Script/Event Injection (Reflected+Stored)"; pcre:"/User-Agent[^\r\n]*(<script|<img[^>]+onerror|<svg[^>]+onload|<iframe|<body[^>]+onload|javascript:|document\.cookie|document\.location|alert\s*\(|confirm\s*\(|prompt\s*\()/i"; sid:9000131; rev:2;)
```
ModSecurity
```
SecRule REQUEST_HEADERS:User-Agent \
    "@rx (?:<(?:script|img|svg|iframe|body|input)[^>]*(?:>|onerror|onload|onclick)\s*=?|javascript\s*:|document\.(?:cookie|location)|alert\s*\(|confirm\s*\(|prompt\s*\()" \
    "id:9000131,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS User-Agent - Script/Event Injection (Reflected+Stored)'"
```

Snort
```
alert tcp any any -> 220.88.200.193 8080 (msg:"XSS Stored Blog - Script Injection in POST Body"; pcre:"/(<script[^>]*>|<img[^>]+onerror\s*=|<svg[^>]+onload\s*=|<iframe[^>]*|<body[^>]+onload\s*=|javascript:|document\.cookie|document\.location)/i"; sid:9000141; rev:2;)

alert tcp any any -> 220.88.200.193 8080 (msg:"XSS Stored Blog - Cookie Exfiltration Pattern"; pcre:"/(document\.location|window\.location|fetch|XMLHttpRequest).{0,100}document\.cookie/i"; sid:9000142; rev:2;)
```
ModSecurity
```
SecRule REQUEST_BODY \
    "@rx (?:document\.(?:cookie|location|write)|window\.(?:location|open)|eval\s*\(|atob\s*\(|String\.fromCharCode)" \
    "id:9000141,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS Stored Blog - DOM Manipulation / Cookie Access'"

SecRule REQUEST_BODY \
    "@rx document\.cookie.{0,100}https?://" \
    "id:9000142,phase:2,log,pass,\
    t:urlDecodeUni,t:lowercase,\
    msg:'XSS Stored Blog - Cookie Exfiltration to External URL'"
```
