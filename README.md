# tagmusic api spec for app
* Current Version: v0.1

### LOGIN
*소셜 로그인후 provider가 제공하는 유저정보를 넘김*
* method: POST
```
/api/<version>/user/login
{"provider": "facebook", "data": {"certified_key": "xxxx", "id": "tagmusic"}}
```
* Return value
```
{"status": "success", "user_key": "1234", "messaged": "logged in"}
```

### LOGOUT
* method: DELETE
```
/api/<version>/user/logout
```
* Return value
```
{"status": "success", "messaged": "logged out"}
```

### 메인페이지
#### Imo Chart
```
/api/<version>/musicChart/<type>/<subtype>?range=<intVal>
```
* type: imo, world, korea, ....
* subtype: latest, 리스트
* range: 뭐하는 놈인지 설명

* Return value
```
{
"status": "success", 
"data": [{"album": "aaa", "albumartist", "",  "artist": "", "bitrate": "", "genre": "jazz"}, {}]
}
```
#### World Chart nameList
```
/api/<version>/code/<code_type>/<sub_type>
```
