
## 1. Spidering


  COMMAND

````
wget -S --spider http://localhost:8000
````

OUTPUT

```
Spider mode enabled. Check if remote file exists.
--2018-11-13 04:32:11--  http://localhost:8000/
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Remote file exists and could contain further links,
but recursion is disabled -- not retrieving.
```

RESULT \
Failed


## 2. Checking the server for metafiles


COMMAND

````
wget http://localhost:8000/robots.txt
````

OUTPUT

```
--2018-11-15 02:59:33--  http://localhost:8000/robots.txt
Resolving localhost (localhost)... ::1, 127.0.0.1
Connecting to localhost (localhost)|::1|:8000... connected.
HTTP request sent, awaiting response... 404 Not Found
2018-11-15 02:59:33 ERROR 404: Not Found.
```

 RESULT \
 Failed

## 3. Testing user roles


STEPS
1. Logged in to a client account with the username 'sk'
2. Tried to access /admin.

OUTPUT

![screenshot](https://raw.githubusercontent.com/sayamkanwar/Fabrik_Tests/master/screenshots/Screenshot%202018-11-15%20at%203.40.20%20AM.png?token=AKWDIWOJZcWs7uJroDbMSs0jKdCXb3_zks5b9di2wA%3D%3D)

RESULT \
Failed

## 3. Identifying application entry point

I fetched the 'csrftoken' from /accounts/login/ from the csrf token cookie and inspected the source code of /accounts/login/ for the value of 'csrfmiddlewaretoken'.

COMMAND
```
curl -v --cookie "csrftoken=E6b3FaVLMrVahxVyeJYmTy8yQyxMe1c5OCLceu0T54sWFaLSHT8WwUWTrQTSwAsW" --data "csrfmiddlewaretoken=dwmoCtxiIFeEveNYoTXPj2nfn1cNDiYbn2WxbNCq1iLqTRDiR37pWobAYjyTVRe2&login=sk&password=PASSWORD" http://0.0.0.0:8000/accounts/login/
```

OUTPUT

```
Trying 0.0.0.0...
* TCP_NODELAY set
* Connected to 0.0.0.0 (127.0.0.1) port 8000 (#0)
> POST /accounts/login/ HTTP/1.1
> Host: 0.0.0.0:8000
> User-Agent: curl/7.52.1
> Accept: */*
> Cookie: csrftoken=E6b3FaVLMrVahxVyeJYmTy8yQyxMe1c5OCLceu0T54sWFaLSHT8WwUWTrQTSwAsW
> Content-Length: 109
> Content-Type: application/x-www-form-urlencoded
> 
* upload completely sent off: 109 out of 109 bytes
< HTTP/1.1 302 Found
< Transfer-Encoding: chunked
< Content-Type: text/html; charset=utf-8
< X-Frame-Options: SAMEORIGIN
< Vary: Cookie
< Location: /
< Set-Cookie:  csrftoken=2G9M8QdL0iCXz8trPdy9lYazQJdEfH92CdSqa9HUgjq7uwDoAk7asAH1FlgSPBk2; expires=Wed, 13-Nov-2019 22:33:32 GMT; Max-Age=31449600; Path=/
< Set-Cookie:  sessionid=69spzrp078tnpikskur48qaa6eieewdc; httponly; Path=/
< Set-Cookie:  messages="499dd26cc54ae1701ec701a749fa42c23008760e$[[\"__json_message\"\0540\05425\054\"Successfully signed in as sk.\"]]"; httponly; Path=/
< 
* Curl_http_done: called premature == 0
* Connection #0 to host 0.0.0.0 left intact
```

RESULT \
Passed

## 4. Identifying open ports

COMMAND
```
nmap -p 8000 --open 0.0.0.0
```

OUTPUT

```
Starting Nmap 7.70 ( https://nmap.org ) at 2018-11-16 03:46 IST
Nmap scan report for 0.0.0.0
Host is up (0.00019s latency).

PORT     STATE SERVICE
8000/tcp open  http-alt

Nmap done: 1 IP address (1 host up) scanned in 0.13 seconds
```

## 5. Checking for Certificate information, Weak Ciphers and SSL

COMMAND
```
nmap --script ssl-cert,ssl-enum-ciphers -p 443,465,993,995 0.0.0.0
```

OUTPUT

```
Starting Nmap 7.70 ( https://nmap.org ) at 2018-11-16 04:04 IST
Nmap scan report for 0.0.0.0
Host is up (0.00036s latency).

PORT    STATE  SERVICE
443/tcp closed https
465/tcp closed smtps
993/tcp closed imaps
995/tcp closed pop3s

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

## 6. SQL Injection

I entered the username 'sk' as it was already registered and in the password field, I entered `` ' OR 1=1 -- ``.

OUTPUT

```
The username and/or password you specified are not correct.
```

I tried entering `` ') OR 1=1 -- `` to check if there was any encryption function called but it didn't work too.


### SQLMAP ###

COMMAND
````
python sqlmap.py -u "http://0.0.0.0:8000/accounts/login/"
````

OUTPUT
````
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.2.11.9#dev}
|_ -| . [,]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V          |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 08:54:53 /2018-11-17/

[08:54:53] [WARNING] you've provided target URL without any GET parameters (e.g. 'http://www.site.com/article.php?id=1') and without providing any POST parameters through option '--data'
do you want to try URI injections in the target URL itself? [Y/n/q] y
[08:55:01] [INFO] testing connection to the target URL
[08:55:01] [CRITICAL] previous heuristics detected that the target is protected by some kind of WAF/IPS
[08:55:01] [INFO] testing if the target URL content is stable
[08:55:02] [WARNING] target URL content is not stable (i.e. content differs). sqlmap will base the page comparison on a sequence matcher. If no dynamic nor injectable parameters are detected, or in case of junk results, refer to user's manual paragraph 'Page comparison'
how do you want to proceed? [(C)ontinue/(s)tring/(r)egex/(q)uit] c
[08:55:05] [INFO] searching for dynamic content
[08:55:05] [INFO] dynamic content marked for removal (1 region)
[08:55:05] [INFO] testing if URI parameter '#1*' is dynamic
[08:55:05] [WARNING] URI parameter '#1*' does not appear to be dynamic
[08:55:05] [WARNING] heuristic (basic) test shows that URI parameter '#1*' might not be injectable
[08:55:05] [INFO] testing for SQL injection on URI parameter '#1*'
[08:55:05] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[08:55:05] [WARNING] reflective value(s) found and filtering out
[08:55:06] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[08:55:06] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
[08:55:06] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[08:55:07] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[08:55:07] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[08:55:07] [INFO] testing 'MySQL >= 5.0 error-based - Parameter replace (FLOOR)'
[08:55:07] [INFO] testing 'MySQL inline queries'
[08:55:07] [INFO] testing 'PostgreSQL inline queries'
[08:55:07] [INFO] testing 'Microsoft SQL Server/Sybase inline queries'
[08:55:07] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[08:55:08] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[08:55:08] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[08:55:08] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind'
[08:55:08] [INFO] testing 'PostgreSQL > 8.1 AND time-based blind'
[08:55:09] [INFO] testing 'Microsoft SQL Server/Sybase time-based blind (IF)'
[08:55:09] [INFO] testing 'Oracle AND time-based blind'
[08:55:09] [INFO] testing 'Generic UNION query (NULL) - 1 to 10 columns'
[08:55:12] [WARNING] URI parameter '#1*' does not seem to be injectable
[08:55:12] [CRITICAL] all tested parameters do not appear to be injectable. Try to increase values for '--level'/'--risk' options if you wish to perform more tests. You can give it a go with the switch '--text-only' if the target page has a low percentage of textual content (~14.30% of page content is text). If you suspect that there is some kind of protection mechanism involved (e.g. WAF) maybe you could try to use option '--tamper' (e.g. '--tamper=space2comment')
[08:55:12] [WARNING] HTTP error codes detected during run:
404 (Not Found) - 126 times

[*] ending @ 08:55:12 /2018-11-17/
````
