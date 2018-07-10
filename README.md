# tagmusic api spec for app
* Current Version: v0.1

```
songs
----playlist
----played
----artist
----album
----sponsor
```

### [POST] LOGIN
*APP에서 소셜 로그인후 provider가 제공하는 유저정보를 POST_DATA넘김*
*sample 확인
```
/api/{version}/user/login
{"provider": "twitter", "data": {"certified_key": "xxxx", "id": 49236846, "access_token": {"oauth_token": "12345-JEXHIy12eP13pPknknzbbsErDsdLE0GfIFPW0JrWdsssBYq", "oauth_token_secret": "ziAVb58Lfg2cM9saJclkZ7T258ydvQU8rp1f2gySYouEfa", "user_id": "12345", "screen_name": "dddddd", "x_auth_expires": "0"}}
```
* Return value
* jwt token이 header로 날라가거나, return value의 json 포맷으로.. 확인
```
{"status": 200, "user_key": "1234", "messaged": "logged in", "token": "abcdefzzzzz"}
```

### [DELETE] LOGOUT
```
/api/{version}/user/logout
```
* Return value
```
{"status": 200, "messaged": "logged out"}
```

### 메인페이지
#### [GET] Chart Menus
*차트 메뉴 리스트*
* imo, world, korea, vietnam, .... 과 같은 차트 속성, default: imo
```
/api/{version}/charts/
```
* Return value
```
{
"status": 200, 
"data": [{"id":1,"name":"Korea"},{"id": 2, "name":"America"},...]
}
```

#### [GET] Chart LIST
*차트에 딸린 음악 리스트*
* method: GET
```
/api/{version}/musicChart/{chart_id}?{page, page_count, sort}
```
* chart_id: chart menu id, default: 1(imo)
* page_count: 리스트 개수, int, default: 10
* page : 현재 페이지, int, default: 0
* sort: latest, favor, dj, recommend, ... 과 같은 차트 정렬 속성, String, default: latest

* Return value
* 기본값 리턴 아이템 적을것
```
{
"status": 200, 
"id": 1,
"name": "imo",
"data": [{"id", 1, "album": "aaa", "albumartist", "",  "artist": "", "bitrate": "", "genre": "jazz"}, {}]
}
```

### PLAY LIST
#### [GET] Playlist 목록
*플레이 리스트 페이지에서 로그인한 유저에 대한 목록을 뿌려줌*
```
/api/{version}/playlist?{page, page_count, sort}
```
* page_count: 리스트 개수, int, default: 10
* page : 현재 페이지, int, default: 0

* Return value
```
{"status": 200,
 "data": [{
          "id": 1
          "user_id": 12
          "title":"lovely",
          "count":12
          },...]
}
```
#### [POST] Playlist 등록/수정
*플레이 리스트에 대한 수정사항 요청*
```
/api/{version}/playlist/{id}
{"title": "lovely lovely", "user_id": 123}
```
* id:playlist id, int, id=0이거나 없을 경우 신규등록, id>0 일경우 수정 

* Return value
```
{
"status": 200, 
"data": [{
          "id": 1
          "user_id": 12
          "title":"lovely",
          "count":12
          }]
}
```
#### [DELETE] Playlist 삭제
*플레이 리스트에 대한 삭제 요청*
```
/api/{version}/playlist/{id}
```
* id:삭제할 playlist_no int value

* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [POST] Playlist에 곡 추가
*플레이 리스트에 곡을 추가*
```
/api/{version}/playlist/{id}
[{"song_id": 1234}, ...]
```
* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [DELETE] Playlist에서 곡 삭제
*플레이 리스트에 곡을 추가*
```
/api/{version}/playlist/{id}
[{"song_id": 1234}, ...]
```
* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [GET] Playlist 곡 보기
*플레이 리스트 목록에서 하나의 플레이리스트에 대한 곡 정보 요청*
```
/api/{version}/playlist/{id}
```
* id:상세 정보를 요청할 playlist_no int value

* Return value
```
{"status": 200, 
 "data": {
            "count":30,
            "song_list":[{
                            "artist":"test",
                            "cover":"img",
                            "title":"test",
                            "pid":1
                           }, {"artist":"test",
                           "cover":"img",
                           "title":"test",
                           "pid":1
                          }],
           "playList_no":1,
           "playlist_name":"test",
           "repeat_yn":"Y"
         }
}
```

### 재생목록
#### 재생 목록 보기
*재생 목록*
* method: GET
```
/api/{version}/played
```
* Return value
```
{
 "status": 200, 
 "data": {
            "count":30,
            "song_list":[{
                            "artist":"test",
                            "cover":"img",
                            "title":"test",
                            "pid":1
                           }, {"artist":"test",
                           "cover":"img",
                           "title":"test",
                           "pid":1
                          }],
           "user_no":1,
           "repeat_yn":"Y"
         }
}
```

#### [POST] 재생 목록에 추가
*플레이 리스트의 음악 리스트를 재생 목록에 추가 *
```
/api/{version}/played
id=123
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### [DELETE] 재생목록에서 곡 삭제
*삭제*
```
/api/{version}/played
[{"song_id": 1234}, ...]
```
* Return value
```
{
"status": 200,
"message": "success"
}
```


### 음악
#### 음악 상세
*음악 상세 요청*
* method: GET
```
/api/{version}/song/{id}
```
* pid:음악 상세 정보를 요청할 pid int value

* Return value
```
{
"status": 200, 
"data": {"artist":"test","cover":"img","title":"test","pid":"1","duration":225, "lyric": "lydkdkdkdkdk"}
}
```
#### playList에 추가하기
*playlist 참조*





### 공지사항
####  공지사항 목록
*공지사항 목록 요청*
* method: GET
```
/api/{version}/notice?{range,page}
```
* range: 리스트 개수에 대한 int value
* page : 현재 페이지에 대한 int value

* Return value
```
{
"status": 200, 
"data": [{
         "content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!",
         "date":"2018-01-01",
         "title":"test",
         "txt_no":1
         },{
         "content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!",
         "date":"2018-01-01",
         "title":"test",
         "txt_no":1
         }]
}
```
#### 공지사항 상세
*공지사항 상세 요청*
* method: GET
```
/api/{version}/notice/{notice_no}
```
* notice_no:상세 정보를 요청할 notice_no int value

* Return value
```
{
"status": 200, 
"data": [{"content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!","date":"2018-01-01","title":"test","txt_no":1}]
}
```
### 이벤트
####  이벤트 목록
*이벤트 목록 요청*
* method: GET
```
/api/{version}/event?{range,page}
```
* range: 리스트 개수에 대한 int value
* page : 현재 페이지에 대한 int value

* Return value
```
{
"status": 200, 
"data": [{
         "content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!",
         "date":"2018-01-01",
         "title":"test",
         "txt_no":1
         },{
         "content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!",
         "date":"2018-01-01",
         "title":"test",
         "txt_no":1
         }]
}
```
#### 이벤트 상세
*이벤트 상세 요청*
* method: GET
```
/api/{version}/event/{event_no}
```
* event_no:상세 정보를 요청할 event_no int value

* Return value
```
{
"status": 200, 
"data": [{"content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!","date":"2018-01-01","title":"test","txt_no":1}]
}
```

#### 재생 목록 편집
*재생 목록에서 노래를 추가하거나 제거한다*
* method: PUT
```
/api/{version}/play
```
* Return value
```
{
 "status": 200, 
 "data": {?}
```
### 검색
#### 검색 완료
*검색 완료*
* method: GET
```
/api/{version}/search/{search_type}?{search_value}
```
* search_type : song, artist... 어떤 값을 검색할지에 대한 type string value
* search_value : 검색어 string value

* Return value
```
{
"status": 200, 
"data": {"artist":"test","cover":"img","title":"test","pid":"1","duration":225}
}
```
### 유저프로필(자신)
#### 개인 프로필 요청
*개인 프로필 조회에 따른 데이터 요청*
* method: GET
```
/api/{version}/myinfo
```
* Return value
```
{
"status": 200, 
"data": {"name":"test","point":1500,"pid":1,"sponsor_cnt":225, "support_cnt":100, "song_cnt":10}
}
```
#### 개인 작품 목록 요청
*개인 프로필 조회에 따른 개인 작품 목록 요청*
* method: GET
```
/api/{version}/myinfo/song
```
* Return value
```
{"status": 200, 
 "data": {
            "song_list":[{
                            "artist":"test",
                            "cover":"img",
                            "title":"test",
                            "pid":1,
                            "play_cnt":100
                           }, {
                           "artist":"test",
                           "cover":"img",
                           "title":"test",
                           "pid":2,
                           "play_cnt":100                           
                          }],
         }
}
```
#### 개인 후원자 목록 요청
*개인 프로필 조회에 따른 개인 후원자 목록 요청*
* method: GET
```
/api/{version}/myinfo/sponsor
```
* Return value
```
{"status": 200, 
 "data": {
            "sponsor_list":[{
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"
                           }, {
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"                          
                          }],
         }
}
```
#### 개인 후원 목록 요청
*개인 프로필 조회에 따른 개인 후원 목록 요청*
* method: GET
```
/api/{version}/myinfo/support
```
* Return value
```
{"status": 200, 
 "data": {
            "support_list":[{
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"
                           }, {
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"                          
                          }],
         }
}
```
#### 개인 포인트 목록 요청
*개인 포인트 사용 목록 요청*
* method: GET
```
/api/{version}/myinfo/point
```
* Return value
```
{"status": 200, 
 "data": {
            "point_list":[{
                            "name":"test",
                            "point":5,
                            "type":"sponsor"
                            "pid":1,
                            "date":"2018-01-01"
                           }, {
                            "name":"test",
                            "type":"support"
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"                          
                          }],
         }
}
```
### 유저프로필(타인)
#### 개인 프로필 요청
*개인 프로필 조회에 따른 데이터 요청*
* method: GET
```
/api/{version}/user/info/{pid}
```
* pid : 클릭한 user의 해당하는 int value

* Return value
```
{
"status": 200, 
"data": {"name":"test","pid":1,"sponsor_cnt":100, "song_cnt":10}
}
```
#### 개인 작품 목록 요청
*개인 프로필 조회에 따른 개인 작품 목록 요청*
* method: GET
```
/api/{version}/user/song/{pid}
```
* pid : 클릭한 user의 해당하는 int value

* Return value
```
{"status": 200, 
 "data": {
            "song_list":[{
                            "artist":"test",
                            "cover":"img",
                            "title":"test",
                            "pid":1,
                            "play_cnt":100
                           }, {
                           "artist":"test",
                           "cover":"img",
                           "title":"test",
                           "pid":2,
                           "play_cnt":100                           
                          }],
         }
}
```
#### 개인 후원자 목록 요청
*개인 프로필 조회에 따른 개인 후원자 목록 요청*
* method: GET
```
/api/{version}/user/sponsor/{pid}
```
* pid : 클릭한 user의 해당하는 int value

* Return value
```
{"status": 200, 
 "data": {
            "sponsor_list":[{
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"
                           }, {
                            "name":"test",
                            "point":5,
                            "pid":1,
                            "date":"2018-01-01"                          
                          }],
         }
}
```
#### 개인 포인트 후원
*개인 포인트 후원에 따른 후원*
* method: PUT
```
/api/{version}/user/support/{pid}
```
* pid : 클릭한 user의 해당하는 int value

* Return value
```
{"status": 200, 
 "data": {?}
}
```
