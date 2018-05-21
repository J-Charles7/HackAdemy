# Content Security Policy 
---

## Overview
Content-Security-Policy is a security satandard which permits to prevent from XSS, CLickjacking and other code injection attacks. As the same origin policy doesn't cover JavaScript, CSS, HTML frames, web workers, fonts, images, embeddable objects such as Java applets, ActiveX, audio and video files, and other HTML5 features tag, the CSP permits to ``restrict them origin with policies``. 
This can be done by setting a Content Security Policy which is `whitelists of trusted sources` content for the website.

----
## Goals 
Content Security Policy aims to do to a few related things:
* Mitigate the risk of content-injection attacks by giving developers fairly granular control over
* The resources which can be requested (and subsequently embedded or executed) on behalf of a specific Document or Worker
* The execution of inline script
* Dynamic code execution (via eval() and similar constructs)
* The application of inline style
* Provide a policy framework which allows developers to reduce the privilege of their applications.

## Example 
The website ``www.example.com`` wants to protect itself against cross-site scripting attacks. Developpers can mitigate the risk of script injection by ``ensuring that their trusted CDN is the only origin from which script can load and execute``. Moreover, they wish to ``ensure that no plugins can execute in their pages contexts``. The following policy has that effect:

``` Content-Security-Policy: script-src https://cdn.example.com/scripts/; object-src 'none'```



## Policy delivery 
Policies could be delivered by HTML element or by the server via HTTP header response field

1. With HTML element 
```HTML
<meta http-equiv="Content-Security-Policy" content="Policy">
```

2. With server Header 
* Apache2
```
<IfModule mod_headers.c>

    Header set Content-Security-Policy "script-src 'self'; object-src 'self'"

    # `mod_headers` cannot match based on the content-type, however,
    # the `Content-Security-Policy` response header should be send
    # only for HTML documents and not for the other resources.

    <FilesMatch "\.(%FilesMatchPattern%)$">
        Header unset Content-Security-Policy
    </FilesMatch>

</IfModule>
```

* nginx 
```
# config to don't allow the browser to render the page inside an frame or iframe
# and avoid clickjacking http://en.wikipedia.org/wiki/Clickjacking
# if you need to allow [i]frames, you can use SAMEORIGIN or even set an uri with ALLOW-FROM uri
# https://developer.mozilla.org/en-US/docs/HTTP/X-Frame-Options
add_header X-Frame-Options SAMEORIGIN;

# when serving user-supplied content, include a X-Content-Type-Options: nosniff header along with the Content-Type: header,
# to disable content-type sniffing on some browsers.
# https://www.owasp.org/index.php/List_of_useful_HTTP_headers
# currently suppoorted in IE > 8 http://blogs.msdn.com/b/ie/archive/2008/09/02/ie8-security-part-vi-beta-2-update.aspx
# http://msdn.microsoft.com/en-us/library/ie/gg622941(v=vs.85).aspx
# 'soon' on Firefox https://bugzilla.mozilla.org/show_bug.cgi?id=471020
add_header X-Content-Type-Options nosniff;

# This header enables the Cross-site scripting (XSS) filter built into most recent web browsers.
# It's usually enabled by default anyway, so the role of this header is to re-enable the filter for
# this particular website if it was disabled by the user.
# https://www.owasp.org/index.php/List_of_useful_HTTP_headers
add_header X-XSS-Protection "1; mode=block";

# with Content Security Policy (CSP) enabled(and a browser that supports it(http://caniuse.com/#feat=contentsecuritypolicy),
# you can tell the browser that it can only download content from the domains you explicitly allow
# http://www.html5rocks.com/en/tutorials/security/content-security-policy/
# https://www.owasp.org/index.php/Content_Security_Policy
# I need to change our application code so we can increase security by disabling 'unsafe-inline' 'unsafe-eval'
# directives for css and js(if you have inline css or js, you will need to keep it too).
# more: http://www.html5rocks.com/en/tutorials/security/content-security-policy/#inline-code-considered-harmful
add_header Content-Security-Policy "script-src 'self' 'unsafe-inline' 'unsafe-eval' *.youtube.com maps.gstatic.com *.googleapis.com *.google-analytics.com cdnjs.cloudflare.com assets.zendesk.com connect.facebook.net; frame-src 'self' *.youtube.com assets.zendesk.com *.facebook.com s-static.ak.facebook.com tautt.zendesk.com; object-src 'self'";

```
## References : 
* [RFC draft](https://www.w3.org/TR/CSP/#csp-directives)
* [MDN web docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
* [More about policies](https://www.w3.org/TR/CSP/#framework-policy)
