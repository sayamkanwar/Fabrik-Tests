
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

