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

### Note :

**FTW - Framework for Testing WAFs**
---
* **Find regressions in WAF** deployments by using continuous integration and issuing repeatable attacks to a WAF
* Provide a **testing framework for new rules into ModSecurity**, if a rule is submitted it MUST have corresponding positive & negative tests
* **Evaluate WAFs** against a common, agreeable baseline ruleset (OWASP)
* **Test and verify custom rules** for WAFs that are not part of the core rule set

[FTW Home page](https://github.com/fastly/ftw)

[Here](https://coreruleset.org/) is the offcial web site of the OWASP CRS and this is [Github repository](https://github.com/SpiderLabs/owasp-modsecurity-crs).
