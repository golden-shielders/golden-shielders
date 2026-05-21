
Host Header Attack (Reset Poisoning)
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB Host Header Poisoning Attempt";  
 flow:to_server,established;  
 content:"POST"; http_method;  
 content:"/bWAPP/hostheader_2.php"; http_uri;  
 content:"Host|3a| attack.com"; http_header;  
 classtype:web-application-attack; sid:1002101; rev:1;  
)
```
Directory Traversal - Files
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB Directory Traversal passwd Access";  
 flow:to_server,established;  
 content:"GET"; http_method;  
 content:"/bWAPP/directory_traversal_1.php"; http_uri;  
 pcre:"/(\.{2}\/)+etc\/passwd/Ui";  
 classtype:web-application-attack; sid:1002102; rev:1;  
)
```
Local File Inclusion (SQLiteManager)
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB SQLiteManager XSS TriggerStep";  
 flow:to_server,established;  
 content:"POST"; http_method;  
 content:"/sqlite/main.php"; http_uri;  
 content:"TriggerStep="; http_client_body;  
 pcre:"/(%3c|<)script(%3e|>).*?(document\.cookie|alert\()/Ui";  
 classtype:web-application-attack; sid:1002103; rev:1;  
)
```
Remote & Local File Inclusion (RFI/LFI)|
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB RFI LFI passwd Attempt";  
 flow:to_server,established;  
 content:"GET"; http_method;  
 content:"/bWAPP/rlfi.php"; http_uri;  
 pcre:"/language=([^&]*)(\.{2}\/)+etc\/passwd/Ui";  
 classtype:web-application-attack; sid:1002104; rev:1;  
)
```
Server Side Request Forgery (SSRF) - RFI
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB SSRF Loopback Access Attempt";  
 flow:to_server,established;  
 content:"/bWAPP/rlfi.php"; http_uri;  
 pcre:"/(ip=)(127\.0\.0\.1|localhost|0\.0\.0\.0|169\.254\.169\.254)/Ui";  
 classtype:web-application-attack; sid:1002105; rev:1;  
)
```
Server Side Request Forgery (SSRF) - XXE
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
msg:"LOCAL WEB XXE Attempt - DOCTYPE ENTITY SYSTEM";  
flow:to_server,established;  
content:"POST"; http_method;  
content:"/bWAPP/xxe-2.php"; http_uri;  
content:"<!DOCTYPE"; http_client_body;  
content:"ENTITY"; http_client_body;  
content:"SYSTEM"; http_client_body;  
classtype:web-application-attack;  
sid:1002106;  
rev:1;  
)
```
XML External Entity Attacks (XXE)
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB XXE file Entity Attempt";  
 flow:to_server,established;  
 content:"POST"; http_method;  
 content:"/bWAPP/xxe-2.php"; http_uri;  
 content:"<!ENTITY"; http_client_body; nocase;  
 pcre:"/SYSTEM\s+["'](file:\/\/\/|php:\/\/filter|http:\/\/localhost)/Ui";  
 classtype:web-application-attack; sid:1002106; rev:1;  
)
```
CSRF - Change Password
Snort
``` snort
alert tcp any any -> $HOME_NET $HTTP_PORTS (  
 msg:"LOCAL WEB CSRF Password Change via GET";  
 flow:to_server,established;  
 content:"GET"; http_method;  
 content:"/bWAPP/csrf_1.php"; http_uri;  
 content:"password_new="; http_uri;  
 content:"password_conf="; http_uri;  
 content:"action=change"; http_uri;  
 classtype:web-application-attack; sid:1002107; rev:1;  
)
```
