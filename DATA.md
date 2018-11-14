
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

## 3. Testing user roles


STEPS
1. Logged in to a client account with the username 'sk'
2. Tried to access /admin.

OUTPUT

![screenshot](https://raw.githubusercontent.com/sayamkanwar/Fabrik_Tests/master/screenshots/Screenshot%202018-11-15%20at%203.40.20%20AM.png?token=AKWDIWOJZcWs7uJroDbMSs0jKdCXb3_zks5b9di2wA%3D%3D)
