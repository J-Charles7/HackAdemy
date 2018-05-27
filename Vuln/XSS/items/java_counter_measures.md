## Data Validation  
Data validation in java is also performed by using regular expression. 
For example we could check that a user login only contains alphanumeric characters by using:
 

```java

	String userInput = "You user login is owasp-user01";
	Assert.assertTrue(Pattern.matches("[a-zA-Z0-9\\s\\-]{1,50}", userInput));
``` 

## Data sanitarization 
The HtmlPolicyBuilder class is used to sanitize data in java. 

```java
	/* Create a sanitizing policy that only allow tag '<p>' and '<strong>'*/
	PolicyFactory policy = new HtmlPolicyBuilder().allowElements("p", "strong").toFactory();

	/* Sanitize the output that will be sent to user and comes from the user*/
	String safeOutput = policy.sanitize(outputToUser);


	/* Encode HTML Tag*/
	safeOutput = Encode.forHtml(safeOutput);
	String finalSafeOutputExpected = "You &lt;p&gt;user login&lt;/p&gt; is &lt;strong&gt;owasp-user01&lt;/strong&gt;";
	Assert.assertEquals(finalSafeOutputExpected, safeOutput);
```




## Java escaping 
To escape, for example the input file `input.txt` the escapeHTML from StringEscapeUtils package could be used. 
`public static String unescapeHtml(String str)`

```java
	import java.io.File;

	import org.apache.commons.io.FileUtils;
	import org.apache.commons.lang.StringEscapeUtils;

	public class HtmlEscapeTest {

		public static void main(String[] args) throws Exception {

			String str = FileUtils.readFileToString(new File("input.txt"));
			String results = StringEscapeUtils.escapeHtml(str);
			System.out.println(results);

		}

	}
```
Here is the input.txt file that was used.

```html
	<sometext>
	Here is some "Text" that I'd like to be "escaped" for HTML
	& here is some Swedish: Tack. Vars?god.
	</sometext>
```

The output of HtmlEscapeTest is shown below.

```html
	&lt;sometext&gt;
	Here is some &quot;Text&quot; that I'd like to be &quot;escaped&quot; for HTML
	&amp; here is some Swedish: Tack. Vars&aring;god.
	&lt;/sometext&gt;
```


