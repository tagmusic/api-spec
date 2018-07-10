# tagmusic api spec for app
* Current Version: v0.1

### LOGIN
*소셜 로그인후 provider가 제공하는 유저정보를 넘김*
* method: POST
```
/api/{version}/user/login
{"provider": "facebook", "data": {"certified_key": "xxxx", "id": "tagmusic"}}
```
* Return value
```
{"status": "success", "user_key": "1234", "messaged": "logged in"}
```

### LOGOUT
* method: DELETE
```
/api/{version}/user/logout
```
* Return value
```
{"status": "success", "messaged": "logged out"}
```

### 메인페이지
#### Imo Chart
*메인페이지 IMO차트에 대한 차트 리스트를 넘김*
* method: GET
```
/api/{version}/musicChart/{type}/{subtype}?{range,page}
```
* type: imo, world, korea, vietnam, .... 과 같은 차트 속성 String value
* subtype: latest, favor, dj recommend, ... 과 같은 차트 정렬 속성 String value
* range: 리스트 개수에 대한 int value
* page : 현재 페이지에 대한 int value

* Return value
```
{
"status": "success", 
"data": [{"album": "aaa", "albumartist", "",  "artist": "", "bitrate": "", "genre": "jazz"}, {}]
}
```
#### World Chart nameList
*메인페이지 세계 차트 목록에 대한 차트 타입 코드 리스트를 넘김*
* method: GET
```
/api/{version}/code/{code_type}
```
* code_type:chartType ... 차트 속성을 가져오기 위한 code_type String value

* Return value
```
{
"status": "success", 
"data": [{"code_name":"Korea","code_value":"1"},{"code_name":"America","code_value":"2"}, {}]
}
```
#### 차트 상세 페이지
*차트 상세페이지 차트에 대한 차트 리스트를 넘김*
* method: GET
```
/api/{version}/musicChart/{type}/{subtype}?{range}
```
* type: imo, world, korea, vietnam, .... 과 같은 차트 속성 String value
* subtype: latest, favor, dj recommend, ... 과 같은 차트 정렬 속성 String value
* range: 리스트 개수에 대한 int값 

* Return value
```
{
"status": "success", 
"data": [{"album": "aaa", "albumartist", "",  "artist": "", "bitrate": "", "genre": "jazz"}, {}]
}
```
### PLAY LIST
#### Playlist 목록
*플레이 리스트 페이지에서 로그인한 유저에 대한 목록을 뿌려줌*
* method: GET
```
/api/{version}/playlist
```
* Return value
```
{"status": "success",
 "data": [{
          "track_cnt":30,
          "playList_no":1,
          "playlist_name":"test",
          "user_no":0
          },{
          "track_cnt":10,
          "playList_no":2,
          "playlist_name":"a",
          "user_no":0
          }, {
          }]
}
```
#### Playlist 수정
*플레이 리스트에 대한 수정사항 요청*
* method: PUT
```
/api/{version}/playlist/{playlist_no}
```
* playlist_no:수정할 playlist_no int value

* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### Playlist 삭제
*플레이 리스트에 대한 삭제 요청*
* method: DELETE
```
/api/{version}/playlist/{playlist_no}
```
* playlist_no:삭제할 playlist_no int value

* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### Playlist 상세
*플레이 리스트 목록에서 하나의 플레이 리스트에 대한 상세 정보 요청*
* method: GET
```
/api/{version}/playlist/{playlist_no}
```
* playlist_no:상세 정보를 요청할 playlist_no int value

* Return value
```
{"status": "success", 
 "data": {
            "count":30,
            "music_list":[{
                            "creator":"test",
                            "img":"img",
                            "music_name":"test",
                            "music_no":1
                           }, {"creator":"test",
                           "img":"img",
                           "music_name":"test",
                           "music_no":1
                          }],
           "playList_no":1,
           "playlist_name":"test",
           "repeat_yn":"Y"
         }
}
```
#### Playlist 생성
*플레이 리스트에 대한 삭제 요청*
* method: PUT
```
/api/{version}/playlist
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
### 공지사항
####  공지사항 목록
*공지사항 목록 요청*
* method: GET
```
/api/{version}/notice?{range}
```
* range: 리스트 개수에 대한 int value

* Return value
```
{
"status": "success", 
"data": [?]
}
```
