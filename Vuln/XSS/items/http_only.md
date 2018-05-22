# HTTP Only
## Description
**HttpOnly** is an additional flag included in a `Set-Cookie HTTP response header`. Using the HttpOnly flag when generating a cookie 
helps mitigate the risk of client side script accessing the protected cookie (**if the browser supports it**). 
# Reference
**OWASP** has written a complete [article](https://www.owasp.org/index.php/HttpOnly) on the topic. 
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

## Examples
### Using Java to Set HttpOnly
-----
Since `Java Enterprise Edition 6 (JEE 6)`, which adopted `Java Servlet 3.0 technology`, it's programmatically easy to set the HttpOnly flag on a cookie.

In fact `setHttpOnly` and `isHttpOnly` methods are available in the `Cookie interface`, and also for session cookies (**JSESSIONID**): 
```
Cookie cookie = getMyCookie("myCookieName");
cookie.setHttpOnly(true);
```
Moreover, since JEE 6 it's also declaratively easy setting HttpOnly flag in a session cookie by applying the following configuration in the deployment descriptor `WEB-INF/web.xml`: 
```
<session-config>
 <cookie-config>
  <http-only>true</http-only>
 </cookie-config>
</session-config>

```
For **Java Enterprise Edition versions prior to JEE 6** a common workaround is to overwrite the `SET-COOKIE HTTP response header` 
with a session cookie value that explicitly appends the HttpOnly flag: 
```
String sessionid = request.getSession().getId();
// be careful overwriting: JSESSIONID may have been set with other flags
response.setHeader("SET-COOKIE", "JSESSIONID=" + sessionid + "; HttpOnly");
```
In this context, overwriting, despite appropriate for the HttpOnly flag, is discouraged because the JSESSIONID may have been set
with other flags. A better workaround is taking care of the previously set flags or using the `ESAPI#Java_EE library`: in fact the `addCookie` method of the `SecurityWrapperResponse`  takes care of previously set flags for us. So we could write a servlet filter as the following one: 
```
public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain) throws IOException, ServletException {
    HttpServletRequest httpServletRequest = (HttpServletRequest) request;
    HttpServletResponse httpServletResponse = (HttpServletResponse) response;
    // if errors exist then create a sanitized cookie header and continue
    SecurityWrapperResponse securityWrapperResponse = new SecurityWrapperResponse(httpServletResponse, "sanitize");
    Cookie[] cookies = httpServletRequest.getCookies();
    if (cookies != null) {
        for (int i = 0; i < cookies.length; i++) {
            Cookie cookie = cookies[i];
            if (cookie != null) {
                // ESAPI.securityConfiguration().getHttpSessionIdName() returns JSESSIONID by default configuration
                if (ESAPI.securityConfiguration().getHttpSessionIdName().equals(cookie.getName())) {
                    securityWrapperResponse.addCookie(cookie);
                }
            }
        }
    }
    filterChain.doFilter(request, response);
}

```
Some web application servers, that implement `JEE 5`, and servlet containers that implement `Java Servlet 2.5 (part of JEE 5)`, also allow creating HttpOnly session cookies:

* `Tomcat 6` In `context.xml` set the context tag's attribute `useHttpOnly` as follow:
```
<?xml version="1.0" encoding="UTF-8"?>
<Context path="/myWebApplicationPath" useHttpOnly="true">
```
* `JBoss 5.0.1` and `JBOSS EAP 5.0.1` In `\server\<myJBossServerInstance>\deploy\jbossweb.sar\context.xml` set the `SessionCookie tag` as follow:
```
<Context cookies="true" crossContext="true">
  <SessionCookie secure="true" httpOnly="true" />
```
* `IBM Websphere` offer HTTPOnly for session cookies as a configuration option.

### Using .NET to Set HttpOnly
-----
* By default, `.NET 2.0` sets the HttpOnly attribute for
  * Session ID
  * Forms Authentication cookie
In .NET 2.0, HttpOnly can also be set via the HttpCookie object for all custom application cookies.
* Via `web.config` in the `system.web/httpCookies` element
```
<httpCookies httpOnlyCookies="true" …> 
```
* Or `programmatically`:
C# Code: 
```
HttpCookie myCookie = new HttpCookie("myCookie");
myCookie.HttpOnly = true;
Response.AppendCookie(myCookie);
```
VB.NET Code: 
```
Dim myCookie As HttpCookie = new HttpCookie("myCookie")
myCookie.HttpOnly = True
Response.AppendCookie(myCookie)
```
* However, in `.NET 1.1`, you would have to do this *manually*, e.g.
```
Response.Cookies[cookie].Path += ";HttpOnly";
```
### Using Python (cherryPy) to Set HttpOnly
-----
Python Code (**cherryPy**):
To use HTTP-Only cookies with **Cherrypy** sessions just add the following line in your configuration file:
```
tools.sessions.httponly = True
```
If you use SLL you can also make your cookies secure (encrypted) to avoid **"man-in-the-middle"** cookies reading with:
```
tools.sessions.secure = True
```
###  Using PHP to set HttpOnly 
-----
**PHP** supports setting the HttpOnly flag since version 5.2.0 (November 2006).

For session cookies managed by PHP, the flag is set either permanently in `php.ini` PHP manual on HttpOnly through the parameter: 
```
session.cookie_httponly = True
```
or in and during a script via the function: 
```
void session_set_cookie_params  ( int $lifetime  [, string $path  [, string $domain  
                                  [, bool $secure= false  [, bool $httponly= false  ]]]] )

```

For application cookies last parameter in `setcookie()` sets HttpOnly flag: 
```
bool setcookie  ( string $name  [, string $value  [, int $expire= 0  [, string $path  
                 [, string $domain  [, bool $secure= false  [, bool $httponly= false  ]]]]]] )
```

### WAFs - Web Application Firewalls
-----

If code changes are infeasible, web application firewalls can be used to add HttpOnly to session cookies:
* **Mod_security** - using SecRule and Header [directives](http://blog.modsecurity.org/2008/12/fixing-both-missing-httponly-and-secure-cookie-flags.html)
* [ESAPI WAF](http://code.google.com/p/owasp-esapi-java/downloads/list) using add-http-only-flag [directive](http://www.slideshare.net/llamakong/owasp-esapi-waf-appsec-dc-2009)


