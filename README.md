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
{"status": 200, "user_key": "1234", "messaged": "logged in"}
```

### LOGOUT
* method: DELETE
```
/api/{version}/user/logout
```
* Return value
```
{"status": 200, "messaged": "logged out"}
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
"status": 200, 
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
"status": 200, 
"data": [{"code_name":"Korea","code_value":"1"},{"code_name":"America","code_value":"2"}, {}]
}
```
#### 차트 상세 페이지
*차트 상세페이지 차트에 대한 차트 리스트를 넘김*
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
"status": 200, 
"data": [{"album": "aaa", "albumartist", "",  "artist": "", "bitrate": "", "genre": "jazz"}, {}]
}
```
### PLAY LIST
#### Playlist 목록
*플레이 리스트 페이지에서 로그인한 유저에 대한 목록을 뿌려줌*
* method: GET
```
/api/{version}/playlist?{range,page}
```
* range: 리스트 개수에 대한 int value 
* page : 현재 페이지에 대한 int value

* Return value
```
{"status": 200,
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
"status": 200, 
"data": [?]
}
```
#### Playlist 제목 수정
*플레이 리스트에 대한 제목 수정 요청*
* method: PUT
```
/api/{version}/playlist/{playlist_no}
```
* playlist_no:수정할 playlist_no int value

* Return value
```
{
"status": 200, 
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
"status": 200, 
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
#### 플레이 리스트 재생 목록에 추가
*플레이 리스트의 음악 리스트를 재생 목록에 추가 *
* method: PUT
```
/api/{version}/play
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### Playlist 생성
*플레이 리스트에 대한 삭제 요청*
* method: POST
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
#### Playlist 공유하기
*플레이 리스트에 대한 공유 요청*
* method: ?
```
/api/{version}/playlist/share
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### 창작자보기
*창작자 상세 보기*
* method: GET
```
/api/{version}/artist/{user_no}
```
* user_no: 클릭한 아티스트 유저에 해당하는 int value

* Return value
```
{
"status": 200, 
"data": {?}
}
```
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
### 음악
#### 음악 상세
*음악 상세 요청*
* method: GET
```
/api/{version}/music/{pid}
```
* pid:음악 상세 정보를 요청할 pid int value

* Return value
```
{
"status": 200, 
"data": {"artist":"test","cover":"img","title":"test","pid":"1","duration":225}
}
```
#### 가사 상세
*가사 요청*
* method: GET
```
/api/{version}/lyric/{pid}
```
* pid:해당 음악에 대한 가사 정보를 요청할 pid int value

* Return value
```
{
"status": 200, 
"data": {"artist":"test","lyric":"its just test content!!its just test content!!its just test content!!its just test content!!its just test content!!","title":"test","pid":"1","duration":225}
}
```
#### 음악 공유하기
*음악에 대한 공유 요청*
* method: ?
```
/api/{version}/music/share
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### playList에 추가하기
*해당 음악 playlist에 추가하기*
* method: PUT
```
/api/{version}/playlist/{playlist_no}
```
* Return value
```
{
"status": "success", 
"data": [?]
}
```
#### 창작자보기
*창작자 상세 보기*
*method: GET
```
/api/{version}/artist/{user_no}
```
* user_no: 클릭한 아티스트 유저에 해당하는 int value

* Return value
```
{
"status": 200, 
"data": {?}
}
```
### 재생
#### 재생 목록
*재생 목록*
* method: GET
```
/api/{version}/play
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
