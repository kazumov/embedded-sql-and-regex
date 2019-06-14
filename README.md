# The complexity of the embedded foreign code in source code

## Abstract

A server-side application code written on any particular language often contains pieces of code written on different, foreign language. The foreign language code embedding produces sufficient additional functionality to the application.

Since the languages are using different syntax, the embedding process creates sufficient difficulties for the utilization all the foreing language syntax features in particular source code.  

We'll study this problem with two the most popular foreign code embedding: SQL and RegEx.

## The SQL code

Let's study the possibilities of embedding SQL and RegEx strings in sourcecode in PHP and Swift programming languages. Both languages allows to use string object as the source of SQL or RegEx instruction. Both languages permits simplified:

Example from source code on PHP ([source](https://www.w3schools.com/php/php_mysql_select.asp)):

``` php
$sql = "SELECT id, firstname, lastname FROM MyGuests";
```

Example from source code on Swift ([source](https://www.raywenderlich.com/385-sqlite-with-swift-tutorial-getting-started)):

``` swift
let createTableString = """
CREATE TABLE Contact(
Id INT PRIMARY KEY NOT NULL,
Name CHAR(255));
"""
let insertStatementString = "INSERT INTO Contact (Id, Name) VALUES (?, ?);"
let queryStatementString = "SELECT * FROM Contact;"
let updateStatementString = "UPDATE Contact SET Name = 'Chris' WHERE Id = 1;"
let deleteStatementStirng = "DELETE FROM Contact WHERE Id = 1;"
let malformedQueryString = "SELECT Stuff from Things WHERE Whatever;"
let querySql = "SELECT * FROM Contact WHERE Id = ?;"
```
Example from source code on JS ([source](https://www.w3schools.com/nodejs/nodejs_mysql_create_table.asp))

``` js
var sql = "CREATE TABLE customers (name VARCHAR(255), address VARCHAR(255))";
```

To be embedded, the SQL or RegEx code should follow multiple restrictions. For example, an embedded code should not have any comments. Also, included in source code, the SQL code, as well as RegEx code cannot be nicely formatted and immediate loosing its native readability:

For example consider the original version ([source](http://www.mysqltutorial.org/mysql-comment/)) of simple SQL query:

``` sql
/*
    Get sales rep employees
    that reports to Anthony

    Arguments:
        none

    Returns:
        List of emploees
*/
 
SELECT 
    lastName, firstName
FROM
    employees
WHERE
    reportsTo = 1143
        AND jobTitle = 'Sales Rep';
```

Embedded in JS source code it would look similar to this piece:

``` js
var employeeListSQLString = "SELECT lastName, firstName FROM employees WHERE reportsT = 1143 AND jobTitle = 'Sales Rep';"
```
Because of simplisity this line can be understandale. Then we will try this chunk of SQL:

``` sql
/*
    Report the single product price dispersion
    Source: http://www.mysqltutorial.org/mysql-inner-join.aspx

    Arguments:
        %productCode% -- code of product. Example: 'S10_1678'

    Returning:
        Single product price dispersion.
*/

SELECT 
    orderNumber, 
    productName, 
    msrp, 
    priceEach
FROM
    products p
        INNER JOIN
    orderdetails o ON p.productcode = o.productcode
        AND p.msrp > o.priceEach        # report only if less than MSRP
WHERE
    p.productcode = %productCode%;
```

After its linearization for embedding:

``` js
var productPriceDispersionSQL = "SELECT orderNumber, productName, msrp, priceEach FROM products p INNER JOIN orderdetails o ON p.productcode = o.productcode AND p.msrp > o.priceEach WHERE p.productcode = %productCode%;"
```


Also, the embedded code loses its functionality. For example, this SQL code utilizes the functionality of version dependent command:

``` sql
CREATE TABLE t1 (
    k INT AUTO_INCREMENT,
    KEY (k)
)  /*!50110 KEY_BLOCK_SIZE=1024; */
```



# The RegEx code

The process of embedding catastrophically uglifyjs the RegEx code. The original source of multi-line and rich commented RegEx code can be reanable and understandable from the first sight ([source](https://www.regular-expressions.info/freespacing.html)):

``` RegEx
#
# Match a 20th or 21st century date in 
# yyyy-mm-dd format
#
(19|20)\d\d                # year (group 1)
[- /.]                     # separator
(0[1-9]|1[012])            # month (group 2)
[- /.]                     # separator
(0[1-9]|[12][0-9]|3[01])   # day (group 3)
```

An embedding of the code above inside a JS string, transforms it into "write-only" version:

``` js
var dateMatchRegEx = "(19|20)\d\d[- /.](0[1-9]|1[012])[- /.](0[1-9]|[12][0-9]|3[01])"
```

In the code below, the reader can notice the `http` marker in this piece of JS code:

``` js
var expression = /https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{2,256}\.[a-z]{2,6}\b([-a-zA-Z0-9@:%_\+.~#?&//=]*)/gi;

```
The marker `http` may give hint about the source, but it is impossible to recognize the details of the rest.

# The solution

One should avoid to embed any foreign code in the native source code.

One can store the foreign code, like SQL or RegEx in external files or storage.

One should use class-like wrappers to recall or extract the source from the native file or resource. 
