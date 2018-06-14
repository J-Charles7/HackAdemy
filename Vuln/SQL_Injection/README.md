# SQL Injection

## Plan
* Lexic
* Overview 
   * Acronyms - Definition
   * Exploitability
   * Impact
   * Components (server, client, etc)
   * Types 
* Vulnerability exploitation samples
* How to test for SQLi
   * Whitebox
   * Graybox 
   * Blackbox
* Presence detection
* Countermeasures guidelines
   * Server side
   * Client side
   * Possible policy(ies)
* Countermeasures implementation
* Common exploitation tools 

# Lexic

# Overview
## Acronyms - Definition
SQL injection stands for Structured Query Laguage injection. It is usually abbreviated as **SQLi**.

It is an attack that consists of insertion or "injection" of a SQL query via the input data from the client to the application.

A successful SQL injection exploit can:
* read sensitive data from the database, 
* modify database data (Insert/Update/Delete), 
* execute administration operations on the database (such as shutdown the DBMS),
* recover the content of a given file present on the DBMS file system and in some cases issue commands to the operating system. 

## Exploitability
## Impact
## Components
The components identification will be based on the image below.
![SQLi components](items/done.png)

### Client tier
The attacker uses a **web browser** to forge malicious SQL queries on a website through the inputs offered by this website.
This query is sent to the **wed and application server** for processing. (**Flaw 1 on the image**)

### Web and application server tier
The web and application server receives the prior query. After possible checks and a least of processing, it forwards it to the DBMS entity. (**Flaw 2 on the image**)
Note: The query cannot be run by the web and application server. The DBMS is the entity capable for this. 

### DBMS tier
A DBMS is a Database management system. It has an SQL processing engine. Once the DBMS receives the query it executes the query 
and **possibily** returns the result to the **Web and application server** (**Flaw 3 on the image**). The result **may be** fetched,  formated and sent to the client (web browser). (**Flaw 4 on the image**)
## Types

# Vulnerability explatation samples
# How to test for SQLi
## Whitebox
## Graybox 
## Blackbox
In this section, there two main types of tests that can be performed : manual and logical.
### Logical
This possible thanks to some softwares that do the job and report (and specify) if there are some inputs that SQLi vulnerable.
* SQLiX      
[It](https://www.owasp.org/index.php/Category:OWASP_SQLiX_Project) is a SQL Injection scanner coded in Perl. It is able to:
     * **crawl**, 
     * **detect** SQL injection vectors, 
     * **identify the back-end database**, and 
     * **grab function** call/UDF results (even execute system commands for MS-SQL). 

The concepts in use are different than the one used in other SQL injection scanners. 

[SQLiX](https://www.owasp.org/index.php/Category:OWASP_SQLiX_Project) is able to find **normal and blind SQL injection** vectors and doesn't need to reverse engineer the original SQL request (using only function calls).  

* Acunetix

[It](https://www.acunetix.com/web-vulnerability-scanner/?utm_term=sql%20injection&utm_campaign=38301565&utm_content=&utm_source=Adwords&utm_medium=cpc&gclid=EAIaIQobChMIn73z3-XS2wIVgQvTCh1HMwEHEAAYASAAEgJI6PD_BwE) is commercial tool performing scans on a site pages for SQLi vulnerabilities detection. It is a multi-threaded, lightning fast crawler and scanner that can crawl hundreds of thousands of pages without interruptions.

# Presence detection
# Countermeasures guidelines
## Server side
## Client side
There is nothing to do by the client as it is not able to run SQL queries and is not a recepient of those queries.

In SQLi, the Client is the **attacker and not the victim**.
## Policy(ies)

# Countermeasures implementation
# Common exploitation tools

* SQLninja

[SQLninja](http://sqlninja.sourceforge.net/) is a SQL injection tool that exploits web applications that use a SQL server as a database server. This tool may not find the injection place at first. But if it is discovered, it can easily automate the exploitation process and extract the information from the database server.

* BSQL Hacker

[BSQL hacker](http://resources.infosecinstitute.com/best-free-and-open-source-sql-injection-tools/#download) is a nice SQL injection tool that helps you perform a SQL injection attack against web applications. This tool is for those who want an automatic SQL injection tool. It is especially made for Blind SQL injection. This tool is fast and performs a multi-threaded attack for better and faster results.

* Safe3 SQL Injector

[Safe3 SQL Injector](http://sourceforge.net/projects/safe3si/) is another powerful but easy to use SQL injection tool. Like other SQL injection tools, it also makes the SQL injection process automatic and helps attackers in gaining the access to a remote SQL server by exploiting the SQL injection vulnerability. It has a powerful AI system which easily recognizes the database server, injection type and best way to exploit the vulnerability.

* Mole
[Mole](http://sourceforge.net/projects/themole/files/) or (The Mole) is an automatic SQL injection tool available for free. This is an open source project hosted on Sourceforge. You only need to find the vulnerable URL and then pass it in the tool. This tool can detect the vulnerability from the given URL by using Union based or Boolean based query techniques. This tool offers a command line interface, but the interface is easy to use. It also offers auto-completion on both commands and command arguments. So, you can easily use this tool.

* SQLmap

[SQLmap](https://github.com/sqlmapproject/sqlmap) is probably the most complete and efficient tool for SQLi vulnerabilities exploitation and even more.

The open source software is run at the command line and is available to download for different operating systems: **Linux** distributions, **Windows** and **Mac OS** operating systems.

Sqlmap has full support for multiple **DBMSs** including **MySQL**, **Oracle**, **PostgreSQL**, **Microsoft SQL Server**, **Microsoft Access**, **IBM DB2**, **SQLite**, **Firebird** and **SAP MaxDB**.

And full support for all injection techniques: **Boolean**, **Error**, **Stack**, **Time**, **Union**.

* SQLSus 
[SQLSus](http://sqlsus.sourceforge.net/) is another open source SQL injection tool and is basically a MySQL injection and takeover tool. This tool is written in Perl and you can extend the functions by adding your own codes. This tool offers a command interface which lets you inject your own SQL queries and perform SQL injection attacks.


# Related attacks 
