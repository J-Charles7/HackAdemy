# WAF XSS Counter Measures Implementation 

We will be using the OWASP open source WAF [ModSecurity](modsecurity.md).

## OWASP CRS - Core Rule Set


The **OWASP ModSecurity Core Rule Set (CRS)** is a set of generic attack detection rules for use with **ModSecurity** or compatible web application firewalls (**WAFs**). The CRS aims to protect web applications from a wide range of attacks, **including the OWASP Top Ten**, with a minimum of false alerts. The CRS provides protection against many common attack categories, including:
* SQL Injection (SQLi)
* Cross Site Scripting (XSS)
* Local File Inclusion (LFI)
* Remote File Inclusion (RFI)
* Remote Code Execution (RCE)
* PHP Code Injection
* HTTPoxy
* Shellshock
* Session Fixation
* Scanner Detection
* Metadata/Error Leakages
* GeoIP Country Blocking

[Here](https://coreruleset.org/) is the offcial web site of the OWASP CRS.
