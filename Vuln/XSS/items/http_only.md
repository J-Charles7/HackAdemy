# HTTP Only
## Description
**HttpOnly** is an additional flag included in a `Set-Cookie HTTP response header`. Using the HttpOnly flag when generating a cookie 
helps mitigate the risk of client side script accessing the protected cookie (**if the browser supports it**). 
# Reference
[OWASP](https://www.owasp.org/index.php/HttpOnly) has written a complete article on the topic. 
The example below shows the syntax used within **the HTTP response header**:
```
Set-Cookie: <name>=<value>[; <Max-Age>=<age>]
[; expires=<date>][; domain=<domain_name>]
[; path=<some_path>][; secure][; HttpOnly]
```
If the HttpOnly flag (optional) is included in the HTTP response header, the cookie cannot be accessed through client side script 
(again if the browser supports this flag). As a result, even if a cross-site scripting (**XSS**) flaw exists, and a user accidentally 
accesses a link that exploits this flaw, `the browser (primarily Internet Explorer) will not reveal the cookie to a third party`. 
If a browser does not support HttpOnly and a website attempts to set an HttpOnly cookie, the HttpOnly flag will be ignored by the
browser, thus creating a traditional, script accessible cookie. As a result, the cookie (typically your session cookie) becomes 
vulnerable to theft of modification by malicious script. 

According to Michael Howard, Senior Security Program Manager in the Secure Windows Initiative group at Microsoft, the majority of 
XSS attacks target theft of session cookies. A server could help mitigate this issue by setting the HttpOnly flag on a cookie it 
creates, indicating the cookie should not be accessible on the client.

`If a browser that supports HttpOnly detects a cookie containing the HttpOnly flag, and client side script code attempts to read 
the cookie, the browser returns an empty string as the result. This causes the attack to fail by preventing the malicious 
(usually XSS) code from sending the data to an attacker's website`. 
