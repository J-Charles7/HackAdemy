# XSS (c'est du balaise!! ^_^ Lol)

## Champ lexical

* [JavaScript]()
* [Cookie de session]()
* [Client web]()
* [Serveur web]()
* [XMLHttpRequest]()
* [WAF]()
* [Proxy]()
* [SOP]()
* [HTML]()



## Nomenclature 
### XSS stands for:
Cross Site Scripting
### Exploitability
Easy/Medium/difficult according to the context
### Impact : 
Several: disfigurements, theft of cookie, unauthorized files access, DoS 
# Cross Site Scripting 
-----
## Overview 
The XSS attack is an attack realized when data submit from end user is `interpretted by a browser`.
It is classified among `injection attacks`.  Early on, it was categorized as three kinds, `stored`, `DOM based` and `reflected`. 
* These three types were defined as follows :

  * __Stored XSS__: generally occurs when server stocks submitted data from one user and then this data could be seen by others users : in a message log, in user log, in user credentials, in message form, in user cv.

  * __Reflected XSS__ : when the user supplied data is automatically replied to the same user without escapping from the server. 

  * __DOM based XSS__ : when the user input is not sent to the server but is used to automatically update its DOM.

* This classification was quickly deprecated. To clarify it, the research community proposed two new terms to help organize the types of XSS that can occur :
  * **Server XSS**
  * **Client XSS**

### Server XSS 
---
Server XSS occurs when untrusted data from user is replied with server generated HTML. In this case, the entire vulnerability is in server-side code, and the browser is simply rendering the response and executing any valid script embedded within it.

### Client XSS
----
Client XSS occurs when `untrusted data` is used to update the DOM with an unsafe call of client side script. 

## How to test for XSS 
* `Graybox` test is the best way to test for XSS. Each HTTP data input could possibly be a XSS attack vector if the input is not escapped. 

* The best way to perform `black blackbox` test in server XSS is to use a proxy, dirbust the website detect all submitted data to the server and inject some HTML characters and analyse the server responses. 


 
