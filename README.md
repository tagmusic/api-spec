# api-spec
Current Version info : 0.1

### LOGIN
* method: POST
```
/api/<version>/user/login
{"provider": "facebook", "data": {"certified_key": "xxxx", "id": "tagmusic"}}
```
*소셜 로그인후 provider가 제공하는 유저정보를 넘김*
* Return value
```
{"status": "success", "user_key": "1234", "messaged": "log in"}
```

### LOGOUT
* method: DELETE
```
/api/<version>/user/logout
```
* Return value
```
{"status": "success", "messaged": "log out"}
```
