#This is a write up for the OWASP Juice Shop.

````
For this task, I am running the lab from a docker container. The below links are the steps I followed to set up my environment.

Some useful Links:
	https://bkimminich.gitbooks.io/pwning-owasp-juice-shop/conten


Starting up my docker.	

docker run --rm -p 3000:3000 bkimminich/juice-shop

````
#SQL Injection Attacks Overview.

````

OWASP A1-Injection: https://www.owasp.org/index.php/Top_10-2017_A1-Injection

SQL injection is an attack in which malicious SQL statements are injected into a SQL DB.
SQLI is quite easy to avoid, but still happens a lot today.
If successful, we can access sensitive DB, extract infirmation, modify dbs, and potentially even get a shell.

Some Common SQL verbs:
	SELECT - Retrieves data from a table.
	INSERT - Adds data to a table.
	DELETE - Removes data from a table.
	UPDATE - Modifies data in a table.
	DROP - Delete a table.
	UNION - Combines data from multiple multiple queries.

Some other extra terms to know of include:
	WHERE - Filters records based on specific condition.
	AND/OR/NOT - Filter records based on multiple conditions
	ORDER BY - Sorts records in ascending/descending order.

Also there are some special characters used in SQL;
	' and " - string delimiters.
	-- ,/* , and # - comment delimiters.
	* and % - wild cards.
	; - Ends SQL statement.


Input: test
SQL: SELECT * FROM Users WHERE email = 'test';

Input: test'
SQL: SELECT * FROM Users WHERE email = 'test'';

Input: test' OR 1=1; --
SQL: SELECT * FROM Users WHERE email = 'test' OR 1=1; --';

	The -- comments everything else, and the condition OR 1=1 is true, this is a valid statement.
	This logs us in as user 1, essentially the admin!

SQLI Defences.

Defense 1: Parameterized Statements.
	Ensure inputs (Parameters) are used safely in SQL Statements.
	Example: "SELECT * FROM Users WHERE email=?";
	Example: "SELECT * FROM Users WHERE email='"+email+"'";

Defense 2:Sanitizing Input.
````