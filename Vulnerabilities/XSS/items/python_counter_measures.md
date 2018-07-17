# Python XSS Countermeasures
 #### Incorrect
  In this part, we will be dealing the Python [Flask library](items/flask.md).
The following is a contrived example of how a **reflected XSS** exploit may occur. If an attacker were to submit a request to `http://example.com/?name=<script>alert(1)</script>` then any user viewing that url would have the javascript executed within the context of their browser.
```Python
# flask example
@app.route("/")
def hello():
    name = request.args.get('name')
    return "Hello %s" % name
```
  #### Correct
The correct way to prevent XSS attacks is to validate user input and ensure that data rendered by templates is escaped. Using templates in the way they are intended is preferable:
```Python
# flask example
@app.route("/")
def hello():
    name = request.args.get('name')
    return render('hello.html', name=name)

# where hello.html is:
# <html>Hello {{ name }}</html>
```
Note : The `escape` function is described as follows:
* It converts the characters `&, <, >, ‘, and ”` in strings to `HTML-safe sequences`. Use this if you need to display text that might contain such characters in HTML. Marks return value as markup string.
* It returns markup objects so that double escaping can’t happen.
Any HTML content that is generated directly within a request handler should use the appropriate escaping function:
```Python
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
```HTML
<img src='someimage.jpg' onload='do_evil()'/>
```
Again, the preferable approach is to only `allow known safe attributes`, and sanitize the content of those attribute values. If the content is not sanitized, the following vulnerable code could occur:
```Javascript
function add_image(link) {
  document.write('<img src="' + link + '"'></img>'');
}
```
If the preceding JavaScript function is called with the link parameter containing the following value, the function can be exploited to execute arbitrary code:
```Javascript
x" onerror="do_evil()
```
A more secure implementation of the above would be:
```Javascript
function add_image(link) {
  clean = link.replace(/"/g, '&quot;');
  document.write('<img src="' + clean + '"'></img>'');
}
Note, this is a very specific example for illustration. A more comprehensive approach to sanitization should be taken for larger applications.
```
[Here](items/activates.md) is properly enough written library to help defend against XSS injections.
