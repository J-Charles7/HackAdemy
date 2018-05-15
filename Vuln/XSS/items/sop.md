# SOP - Same-Origin Policy
## Description
Same-origin policy is an important concept in the `web application security model`. Under the policy, `a web browser 
permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the 
same origin`. An origin is defined as a `combination of URI scheme, host name, and port number`. This policy prevents a 
malicious script on one page from obtaining access to sensitive data on another web page through that page's Document 
Object Model.
This mechanism bears a particular significance for modern web applications that extensively depend on HTTP cookies[1] to maintain authenticated user sessions, as servers act based on the HTTP cookie information to reveal sensitive information or take state-changing actions. A strict separation between content provided by unrelated sites must be maintained on the client-side to prevent the loss of data confidentiality or integrity.
## Learn more
[More](https://en.wikipedia.org/wiki/Same-origin_policy) information.
