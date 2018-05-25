# PHP XSS countermeasures
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
