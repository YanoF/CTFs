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

Defense 1: Parameterized Statements (use of prepared statements).
	Ensure inputs (Parameters) are used safely in SQL Statements.
	Example: "SELECT * FROM Users WHERE email=?";
	Example: "SELECT * FROM Users WHERE email='"+email+"'";

Defense 2:Sanitizing Input.
-Escaping dangerous characters, ensuring that provided inputs are not dangerous.
````

#OWASP A2-Broken Authentication: https://www.owasp.org/index.php/Top_10-2017_A2-Broken_Authentication

````
Confirmation of the user's id, authc, and session management are critical to protect againt authc-related attacks.
There may be authc weaknesses if the app:
-Permits automated attacks such as cred stuffing.
-permits bruteforce attacks.
-permits default, weak or well-known creds, eg admin, admin.
-use plaintext or weakly hashed passwords.
-has missing or ineffective 2FA.
_expose session IDs in the URLs.


Possible Defese include:
-2FA everything whenever possible.
-Do not use default creds.
-Implement weak-passwords checks.
-limit or increasingly delay failed login attempts. Log all failures.
-use server side, secure, built-in session manager that generates new random IDs.

Always be on the look out for token fixation!
````

#OWASP A3-Sensetive Data Exposure: https://www.owasp.org/index.php/Top_10-2017_A3-Sensitive_Data_Exposure

````
The first thing is to determine the protection needs of data in transit and at rest. 
For example, passwords, credit card numbers, health records, personal information and business secrets require extra protection, particularly if that data falls under privacy laws, e.g. EU’s General Data Protection Regulation (GDPR), or regulations, e.g. financial data protection such as PCI Data Security Standard (PCI DSS).

Some of the Defenses for this:
-Encrypt all data in transit and at rest - TLS Encryption
-Classify data processed, stored or transmitted by an app.
-

securityheaders.com - Check for HSTS enforcement.
nmap --script=ssl-enum-ciphers -p443 tesla.com - Using nmap to check for SSL information
````

#OWASP A4-XML External Entities: https://www.owasp.org/index.php/Top_10-2017_A4-XML_External_Entities_(XXE)

````
Attacking systems that parse XML input.
Abuse SYSTEM entity and get malicious.
Attacks include DoS, local file disclosure, RCE, and more.

Attackers can exploit vulnerable XML processors if they can upload XML or include hostile content in an XML document, exploiting vulnerable code, dependencies or integrations.
````

#OWASP A5-Broken Access Control: https://www.owasp.org/index.php/Top_10-2017_A5-Broken_Access_Control

````
Access control enforces policy such that users cannot act outside of their intended permissions. Failures typically lead to unauthorized information disclosure, modification or destruction of all data, or performing a business function outside of the limits of the user. Common access control vulnerabilities include:

Bypassing access control checks by modifying the URL, internal application state, or the HTML page, or simply using a custom API attack tool.
- Allowing the primary key to be changed to another’s users record, permitting viewing or editing someone else’s account.
- Elevation of privilege. Acting as a user without being logged in, or acting as an admin when logged in as a user.
- Metadata manipulation, such as replaying or tampering with a JSON Web Token (JWT) access control token or a cookie or hidden field manipulated to elevate privileges, or abusing JWT invalidation.
- CORS misconfiguration allows unauthorized API access.
- Force browsing to authenticated pages as an unauthenticated user or to privileged pages as a standard user. Accessing API with missing access controls for POST, PUT and DELETE.


Defenses

Access control is only effective if enforced in trusted server-side code or server-less API, where the attacker cannot modify the access control check or metadata.

- With the exception of public resources, deny by default.
- Implement access control mechanisms once and re-use them throughout the application, including minimizing CORS usage.
- Model access controls should enforce record ownership, rather than accepting that the user can create, read, update, or delete any record.
- Disable web server directory listing and ensure file metadata (e.g. .git) and backup files are not present within web roots.
- Log access control failures, alert admins when appropriate (e.g. repeated failures).
- Rate limit API and controller access to minimize the harm from automated attack tooling.
- JWT tokens should be invalidated on the server after logout.

Basic Test From the JuiceShop.
Posting Feedback in another user's name.
Inspect Element and try to delete the hidden text. You can then change the user Id and post a forged feedback.

This a classic example of broken access control, we are not supposed to be able to do that.
````

#OWASP A6-Security Misconfigurations: https://www.owasp.org/index.php/Top_10-2017_A6-Security_Misconfiguration

````
This happens as a result of missing security hardening across an app or system.

The application might be vulnerable if the application is:
- Missing appropriate security hardening across any part of the application stack, or improperly configured permissions on cloud services.
- Unnecessary features are enabled or installed (e.g. unnecessary ports, services, pages, accounts, or privileges).
- Default accounts and their passwords still enabled and unchanged.
- Error handling reveals stack traces or other overly informative error messages to users.
- For upgraded systems, latest security features are disabled or not configured securely.
- The security settings in the application servers, application frameworks (e.g. Struts, Spring, ASP.NET), libraries, databases, etc. not set to secure values.
- The server does not send security headers or directives or they are not set to secure values.
````

#OWASP A7-Cross Site Scripting: https://www.owasp.org/index.php/Top_10-2017_A7-Cross-Site_Scripting_(XSS)

#DOM Based XSS: https://www.scip.ch/en/?labs.20171214

#XSS Game: https://xss-game.appspot.com/

````

````