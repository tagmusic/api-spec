# tagmusic api spec for app
* Current Version: v0.1

```
songs
----chart
----playlist
----played
----artist
----album
----sponsor
```

### 인증(공통)
*로그인을 제외한 모든 request는 header에 다음의 access_token이 포함되어야 함.

*로그인시 response로 리턴된 유저 인증 토큰
```
Authorization: Bearer <access_token>
```
### 인증(에러)
*토큰을 이용한 접근시 발생할 수 있는 에러 상황 정리 
#### 만료된 토큰을 이용한 접근(expired_token)
#Return Value
```
{"status":401, "message":"Token has expired"}
```
#### 부정확한 토큰을 이용한 접근(invalid_token)
#Return Value
```
{"status":422, "message":"{error description:>어떤 이유로 잘못되었는지에 대한 메세지}"}
```
#### 인증헤더가 존재하지 않는 접근(unauthorized)
#Return Value
```
{"status":401, "message":"Missing Authorization Header"}
```

### [POST] 로그인
*APP에서 소셜 로그인후 provider가 제공하는 유저정보를 POST_DATA넘김*
```
/api/{version}/user/login
{"provider": "twitter", "data": {"certified_key": "xxxx", "id": 49236846, "access_token": {"oauth_token": "12345-JEXHIy12eP13pPknknzbbsErDsdLE0GfIFPW0JrWdsssBYq", "oauth_token_secret": "ziAVb58Lfg2cM9saJclkZ7T258ydvQU8rp1f2gySYouEfa", "user_id": "12345", "screen_name": "dddddd", "x_auth_expires": "0", "user_name": "abc"}}
```
* Return value
* userID로 JWT생성, JWT값은 access_token으로 보내준다.
```
{"status": 200, "user_key": "1234", "message": "logged in", "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmcmVzaCI6dHJ1ZSwianRpIjoiZjhmNDlmMjUtNTQ4OS00NmRjLTkyOWUtZTU2Y2QxOGZhNzRlIiwidXNlcl9jbGFpbXMiOnt9LCJuYmYiOjE0NzQ0NzQ3OTEsImlhdCI6MTQ3NDQ3NDc5MSw"}
```

### [DELETE] 로그아웃
```
/api/{version}/user/logout
```
* Return value
```
{"status": 200, "message": "logged out"}
```

### 챠트
#### [GET] Chart Menus
*차트 메뉴 리스트*
* imo, world, korea, vietnam, .... 과 같은 차트 속성, default: imo
```
/api/{version}/charts
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
```
/api/{version}/chart/{chart_id}?{page, page_count, sort}
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
"data": [{"id", 1, "title": "aaa", "artist": "bbb","cover":"img1.jpg", "filepath":"/img/song/", "bitrate": "", "genre": "jazz", "rank":"1"}, {}]
}
```

### 플레이리스트
#### [GET] Playlist 목록
*플레이 리스트 페이지에서 로그인한 유저에 대한 목록을 뿌려줌*
```
/api/{version}/playlist?{page, page_count}
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
*플레이리스트에 대한 수정사항 요청*
```
/api/{version}/playlist/{id}
{"title": "lovely lovely", "user_id": 123}
```
* id:playlist id, int, id=0이거나 없을 경우 신규등록, id>0 일경우 수정 

* Return value
```
{
"status": 200, 
"data": {
          "id": 1
          "user_id": 12
          "title":"lovely",
          "count":0
          }
}
```
#### [DELETE] Playlist 삭제
*플레이리스트에 대한 삭제 요청*
```
/api/{version}/playlist/{id}
```
* id:삭제할 playlist id, int

* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [POST] Playlist에 곡 추가
*플레이리스트에 곡을 추가*
```
/api/{version}/playlist/{id}
[{"song_id": 1234}, ...]
```
* id:곡이 추가될 playlist id, int

* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [DELETE] Playlist에서 곡 삭제
*플레이리스트에 곡을 삭제*
```
/api/{version}/playlist/{id}
[{"song_id": 1234}, ...]
* id:곡이 삭제될 playlist id, int
* [{"song_id": 1234},...] 해당 값이 존재할 경우 플레이리스트에 속해있는 곡 삭제
```
* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [GET] Playlist 곡 보기
*플레이리스트 목록에서 하나의 플레이리스트에 대한 곡 정보 요청*
```
/api/{version}/playlist/{id}
```
* id:상세 정보를 요청할 playlist_no, int

* Return value
```
{"status": 200, 
 "data": {
            "count":30,
            "song_list":[{
                            "artist":"test",
                            "cover":"img",
                            "title":"test",
                            "song_id":1
                           }, {"artist":"test",
                           "cover":"img",
                           "title":"test",
                           "song_id":2
                          }],
           "id":1,
           "title":"test",
           "user_id":1
         }
}
```

### 재생목록
#### [GET] 재생 목록 보기
*재생 목록*
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
*플레이리스트의 음악 리스트를 재생 목록에 추가*
```
/api/{version}/played
{"playlist_id": 1234}
```
* Return value
```
{
"status": 200, 
"message": "success"
}
```
#### [DELETE] 재생목록에서 곡 삭제
*재생목록에서 선택된 곡 삭제*
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
#### [GET] 음악 상세
*음악 상세 요청*
```
/api/{version}/song/{id}
```
* pid:음악 상세 정보를 요청할 pid, int

* Return value
```
{
"status": 200, 
"data": {"artist":"test","cover":"img","title":"test","pid":"1","duration":225, "lyric": "lydkdkdkdkdk"}
}
```
#### playList에 추가하기
*playlist 참조*


### 검색
#### [GET]검색 완료
*검색 완료*
```
/api/{version}/search?{search_type,search_value}
```
* search_type : song, artist... 어떤 값을 검색할지에 대한 type, string, default:song
* search_value : 검색어, string

* Return value
```
{
"status": 200, 
"data": [{"artist":"test","cover":"img","title":"test","pid":"1","duration":225},...]
}
```


### 프로필
#### [GET] 개인 프로필
*개인 프로필 조회에 따른 데이터 요청*
```
/api/{version}/{me,id}
```
* id : me 또는 자신의 아이디일 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다, string or int

* Return value
```
{
"status": 200, 
"data": {"name":"test","points":1500,"pid":1,"sponsor_cnt":225, "support_cnt":100, "song_cnt":10, "rank":2}
}
```
#### [GET]나의 작품 목록(창작자)
*개인 프로필 조회에 따른 개인 작품 목록 요청*
```
/api/{version}/me/song
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
#### [GET]나의 후원자 목록
*개인 프로필 조회에 따른 개인 후원자 목록 요청*
```
/api/{version}/{me, id}/sponsor
```
* id : me 또는 자신의 아이디일 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다, string or int

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
#### [GET]내가 후원한 목록
*개인 프로필 조회에 따른 개인 후원 목록 요청*
```
/api/{version}/{me, id}/support
```
* id : me 또는 자신의 아이디일 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), string or int

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
#### [GET]개인 포인트 목록 요청
*개인 포인트 사용 목록 요청*
```
/api/{version}/{me, id}/point
```
* id : me 또는 자신의 아이디일 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), string or int

* Return value
```
{"status": 200, 
 "data": {
            "point_list":[{
                            "name":"test",
                            "points":5,
                            "type":"sponsor"
                            "pid":1,
                            "date":"2018-01-01"
                           }, {
                            "name":"test",
                            "type":"support"
                            "points":5,
                            "pid":1,
                            "date":"2018-01-01"                          
                          }],
         }
}
```
#### [PUT]포인트 후원
*개인 포인트 후원에 따른 후원*
```
/api/{version}/support/{id}
{"points":10}
```
* id : 후원 받는 유저에 pid, int

* Return value
```
{"status": 200, 
 "message": "success"
}
```


### 공지사항
#### [GET]공지사항 목록
*공지사항 목록 요청*
```
/api/{version}/notice?{page_count,page}
```
* page_count: 리스트 개수, int, default: 10
* page : 현재 페이지, int, default: 0

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
#### [GET]공지사항 상세
*공지사항 상세 요청*
```
/api/{version}/notice/{id}
```
* id:상세 정보를 요청할 id, int

* Return value
```
{
"status": 200, 
"data": {"content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!","date":"2018-01-01","title":"test","txt_no":1}
}
```
### 이벤트
#### [GET]이벤트 목록
*이벤트 목록 요청*
```
/api/{version}/event?{page_count,page}
```
* page_count: 리스트 개수, int, default: 10
* page : 현재 페이지, int, default: 0

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
#### [GET]이벤트 상세
*이벤트 상세 요청*
```
/api/{version}/event/{id}
```
* id:상세 정보를 요청할 id, int

* Return value
```
{
"status": 200, 
"data": {"content":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!","date":"2018-01-01","title":"test","txt_no":1}
}
```
### 작품
#### [POST]작품 올리기
*자신의 작품 올리기*
```
/api/{version}/song
{"title":"aaa", "file":"aaa.mp3", "cover":"bbb.jpg", "desc":"really Good Soung"}
```
* Return value
```
{
"status": 200, 
"message": "success"
}
```
