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
*login시에는 관리자용 app_token을 이용, 그 외는 user에게 발급된 access_token을 전송

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
{"app_id": "tagmusic_app_id", "provider": "twitter", "id": "id-in-provider-we-use-this-as-user-indentity", "data": {"certified_key": "xxxx", "id": 49236846, "access_token": {"oauth_token": "12345-JEXHIy12eP13pPknknzbbsErDsdLE0GfIFPW0JrWdsssBYq", "oauth_token_secret": "ziAVb58Lfg2cM9saJclkZ7T258ydvQU8rp1f2gySYouEfa", "user_id": "12345", "screen_name": "dddddd", "x_auth_expires": "0", "user_name": "abc"}}
```
*app_id와 app_token은 login시에 정상적인 app을 통한것인지 확인하기 위해 사용. **Authorization: Bearer <app_token>** 주의
* Return value
* userID로 JWT생성, JWT값은 access_token으로 보내준다.
```
{"status": 200, "user_id": 1234, "message": "logged in", "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmcmVzaCI6dHJ1ZSwianRpIjoiZjhmNDlmMjUtNTQ4OS00NmRjLTkyOWUtZTU2Y2QxOGZhNzRlIiwidXNlcl9jbGFpbXMiOnt9LCJuYmYiOjE0NzQ0NzQ3OTEsImlhdCI6MTQ3NDQ3NDc5MSw"}
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
"data": [{"album": "Duality", 
        "artist": "Animata/Sarah Buechi/Christoph Haberer", 
        "cover": null, 
        "filepath": "countries/CHINA/\ucd5c\uc2e0 TOP100/201804/Stets i Truure - Animata,Sarah Buechi,Haberer.mp3", 
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/%EC%B5%9C%EC%8B%A0%20TOP100/201804/Stets%20i%20
        "song_id": 31, 
        "title": "Stets i Truure"}, {}]
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
/api/{version}/playlist
{"id":1,"title": "lovely lovely", "user_id": 123, "deleted" : 0}
```
* id:playlist id, int, id=0이거나 없을 경우 신규등록, id>0 일경우 수정 

* Return value
```
{
"status": 200, 
"message" : "success"
}
```
#### [DELETE] Playlist 삭제
*플레이리스트에 대한 삭제 요청*
```
/api/{version}/playlist
{"id":1}
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
            "song_list":[{"album": "Duality", 
         "artist": "Animata/Sarah Buechi/Christoph Haberer", 
        "cover": null, 
        "filepath": "countries/CHINA/\ucd5c\uc2e0 TOP100/201804/Stets i Truure - Animata,Sarah Buechi,Christoph Haberer.mp3", 
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/%EC%B5%9C%EC%8B%A0%20TOP100/201804/Stets%20i%20Truure%20-%20Animata%2CSarah%20Buechi%2CChristoph%20Haberer.mp3?Expires=1531830610&Signature=jTmx89GmpScwPTyw~P3R4OMA3GbMilbUkbuUh-1uiXarGDE5b2i~iBu04k5RkR-YVn0EaaFNardRPOh6menl4KmKJLw4pdxFiQ4C-g1yA-FmhDCCA-k~XAHfZ7AHh7oWckgotSbJJo6K~rAIuOjWJxPoPkhjEGStEc4esjXJc04zbqcfCQqy50Sjd1yZ9rATOb2nzfZX5KDPPidmO6EuqsmBc6VHgey3vJ4RXjx5zxp1iUJkaih92nTP5tmmtSgCMv3qPR-EEsk6YcChTPJzRN9iDQBRY~V-3IBo1Aj7j1PdWy94NHyNUV8Nha7bBJDfLytOKR3SoGMz7sIbVEuPDA__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q", 
        "song_id": 31, 
        "title": "Stets i Truure"
                           }, 
                           {"album": "Duality", 
        "artist": "Animata/Sarah Buechi/Christoph Haberer", 
        "cover": null, 
        "filepath": "countries/CHINA/\ucd5c\uc2e0 TOP100/201804/Stets i Truure - Animata,Sarah Buechi,Haberer.mp3", 
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/%EC%B5%9C%EC%8B%A0%20TOP100/201804/Stets%20i%20
        "song_id": 31, 
        "title": "Stets i Truure"}],
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
                            "album": "Duality", 
        "artist": "Animata/Sarah Buechi/Christoph Haberer", 
        "cover": null, 
        "filepath": "countries/CHINA/\ucd5c\uc2e0 TOP100/201804/Stets i Truure - Animata,Sarah Buechi,Haberer.mp3", 
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/%EC%B5%9C%EC%8B%A0%20TOP100/201804/Stets%20i%20
        "song_id": 31, 
        "title": "Stets i Truure"
                           }, {"album": "Duality", 
        "artist": "Animata/Sarah Buechi/Christoph Haberer", 
        "cover": null, 
        "filepath": "countries/CHINA/\ucd5c\uc2e0 TOP100/201804/Stets i Truure - Animata,Sarah Buechi,Haberer.mp3", 
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/%EC%B5%9C%EC%8B%A0%20TOP100/201804/Stets%20i%20
        "song_id": 31, 
        "title": "Stets i Truure"
                          }],
           "user_id":1,
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
  "data": {
    "album": null, 
    "artist": null, 
    "cover": null, 
    "filepath": "countries/CHINA/201605 \uc774\uc804/Fade - Alan Walker.mp3", 
    "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/201605%20%EC%9D%B4%EC%A0%84/Fade%20-%20Alan%20Walker.mp3?Expires=1531831138&Signature=OBUxM4S3LQVgaQybvQLW28MtzLzRhXIuBXaFawZww544jKHIK5Py5WUhKzIumTxUfHQuZhOgD5lgm2dxPdkDASK7YDQmk5YBegCAdqcLx8dZrvOxUa~5WKrG5pYb~63UwT~5eH2mUwi743LUuE9RGk8s6DTUuKitZFKwnqkfz15gHbtJddkgvWPIiizAd7dc294JbyEEBOMfYOBUFX64NFqBO-AGBEAC~wROmxx~i8KXe5OBj76h-g-2WUs7ZgniMUr33QCsRH--wAlEq6yi7F0YcWGE9jksLRNVxmtEk5KknD1SFOXhJI5EPX3Ztnh6At9mBMLUmiuzDGNplH2~RA__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q", 
    "lyric": "tetetetetetetete", 
    "song_id": 1, 
    "title": "Fade - Alan Walker.mp3"
  }, 
  "status": 200
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
* search_type : song, artist... 어떤 값을 검색할지에 대한 type, string, default:song /
* search_value : 검색어, string
* 현재 song / artist 두 타입 모두 song만 검색
* search_value = artist에 대해 검색

* Return value
```
search_type = song
{
  "data": [
    {
      "album": "NCS: The Best of 2015", 
      "artist": "Alan Walker", 
      "cover": null, 
      "filepath": "countries/CHINA/201605 \uc774\uc804/Spectre - Alan Walker.mp3", 
      "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/201605%20%EC%9D%B4%EC%A0%84/Spectre%20-%20Alan%20Walker.mp3?Expires=1531833292&Signature=kbQ7nSdhz-UbdYmzkJXUDfbPss2ezHn6CjJgsMYfVrC~dD5etuFwia53He0CYT5c8lb0gmO00RlsxPr9Dyg2S7XunUGaqYaqhm9jZEIUGeODeqXU60P5HnwW-fp7IN-1F8N~g7fzXgoK0SFVSmEAfGIyJN78aM9~hLniHvy4BU5M6JomHL3vqJIuj7zXa9H6Xb8npsCcKPCtTRuSSzoiRIIyDgWDrDsUNiW2NRZXi-dx3fuhzljHK4bs7JdaoeG7rVgbCvzTnjddqQP0lVmPokhwI4j8X9T-B3fFdYXjn~hyawPEbWYtBrUDlaGOEXYCfJo-6stQQcM1RX3J3A-p3Q__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q", 
      "song_id": 4, 
      "title": "Spectre"
    }
  ], 
  "status": 200
}

search_type = artist

```


### 프로필
#### [GET] 개인 프로필
*개인 프로필 조회에 따른 데이터 요청*
```
/api/{version}/profile/info/{id}
```
* id : 서버의 저장된 아이디와 해당 파라미터가 동일할 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다, int


* Return value
* return value에 creator_id가 null이 아닌경우 창작자로 후원하기 버튼이 활성화, 아닐경우 후원이 불가능하다.
```
{
  "data": {
    "bio": "test", 
    "data": "test", 
    "name": "test", 
    "points": 0, 
    "rank": 0, 
    "song_cnt": 0, 
    "sponsor_cnt": 0, 
    "support_cnt": 0, 
    "user_id": 1, 
    "creator_id" : 1 
    "user_img": "test"
  }, 
  "status": 200
}
```
#### [GET]나의 작품 목록(창작자)
*개인 프로필 조회에 따른 개인 작품 목록 요청*
```
/api/{version}/profile/song/{id}
* id : 해당하는 user_no값을 가진 유저의 창작곡을 가져온다, int
```
* Return value
```
{"status": 200, 
 "data": {
            "song_list":[{
                            "album": null, 
                            "artist": null, 
                            "cover": null, 
                            "filepath": "countries/CHINA/201605 \uc774\uc804/Fade - Alan Walker.mp3", 
                            "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/201605%20%EC%9D%B4%EC%A0%84/Fade%20-%20Alan%20Walker.mp3?Expires=1531837310&Signature=cIHI9r3KhxdWnosk2e0J1e4CL91FMP5Z5DcZ4UwXzbGpfy7cxbrkbp1H8T5ZLwA6JhgoXXI2MwKsElu~q8CMUYH0oideJ0hJqWYYQx-PEYGK4Tr2lOOwZS5ai5Bk~PMggSZR275rTUrFOOfKaErqEjOzbcrzdFpPaSPm0brboEqw2l90FQMUCgqeRTUNK52PCop-C-36VTKxgbQOx0rj52wN29DOZcpiTv~OR0p-2pRbt1LYCDUTqRmyNLsUUIOOQlJa1KlUxxXMbqlif64m4z4SZAYvMicKaDQRBsjjFLtPw5y8ZDCVSXT2Smm7bnQhUYSAoob0hg-VMwAGlSnqCQ__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q", 
                            "song_id": 1, 
                            "title": "Fade - Alan Walker.mp3"
                           }, {
                            "album": null, 
                            "artist": null, 
                            "cover": null, 
                            "filepath": "countries/CHINA/201605 \uc774\uc804/Fade - Alan Walker.mp3", 
                            "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/201605%20%EC%9D%B4%EC%A0%84/Fade%20-%20Alan%20Walker.mp3?Expires=1531837310&Signature=cIHI9r3KhxdWnosk2e0J1e4CL91FMP5Z5DcZ4UwXzbGpfy7cxbrkbp1H8T5ZLwA6JhgoXXI2MwKsElu~q8CMUYH0oideJ0hJqWYYQx-PEYGK4Tr2lOOwZS5ai5Bk~PMggSZR275rTUrFOOfKaErqEjOzbcrzdFpPaSPm0brboEqw2l90FQMUCgqeRTUNK52PCop-C-36VTKxgbQOx0rj52wN29DOZcpiTv~OR0p-2pRbt1LYCDUTqRmyNLsUUIOOQlJa1KlUxxXMbqlif64m4z4SZAYvMicKaDQRBsjjFLtPw5y8ZDCVSXT2Smm7bnQhUYSAoob0hg-VMwAGlSnqCQ__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q", 
                            "song_id": 2, 
                            "title": "Fade - Alan Walker.mp3"                         
                          }],
         }
}
```
#### [GET]나의 후원자 목록
*개인 프로필 조회에 따른 개인 후원자 목록 요청*
```
/api/{version}/profile/sponsor/{id}
```
* id : 서버의 저장된 아이디와 해당 파라미터가 동일할 경우 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다, int

* Return value
```
{
  "data": {
    "sponsor_list": [
      {
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test", 
        "points": 10, 
        "user_id": 1
      }
    ]
  }, 
  "status": 200
}
```
#### [GET]내가 후원한 목록
*개인 프로필 조회에 따른 개인 후원 목록 요청*
```
/api/{version}/profile/support/{id}
```
* id : 서버의 저장된 아이디와 해당 파라미터가 동일할 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), int

* Return value
```
{
  "data": {
    "support_list": [
      {
        "artist_id": 1, 
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test", 
        "points": 10
      }
    ]
  }, 
  "status": 200
}
```
#### [GET]개인 포인트 목록 요청
*개인 포인트 사용 목록 요청*
```
/api/{version}/profile/point/{id}
```
* id : 자신의 아이디일 경우 나의 포인트내역, 그외의 경우 타인의 포인트내역에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), int

* Return value
```
{
  "data": {
    "point_list": [
      {
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test", 
        "points": 10, 
        "type": "sponsor", 
        "user_id": 1
      }, 
      {
        "artist_id": 1, 
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test", 
        "points": 10, 
        "type": "support"
      }
    ]
  }, 
  "status": 200
}
```
#### [PUT]포인트 후원
*개인 포인트 후원에 따른 후원*
```
/api/{version}/support/{creator_id}
{"points":10}
```
* creator_id : 후원 받는 유저(creator)에 pid(creator_id), int (* 개인 프로필 요청 return type 참고)


```
* Return value
{
    "message": "success",
    "status": 200
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
### 창작자
#### [POST]창작자 등록
*창작자 등록 하기*
```
/api/{version}/creator
{"title":"aaa", "gender":0, "bio":"gexekjdlksjdkl", "birth" : "20101010", "phone":"01055555555", "email":"test@test.com",
 "provider":"facebook", "sns_id" : "asdiop", "hompage" :"aaa.com", "belongto":"aaa"
}
```
* Return value
```
{
"status" : 200,
"message" : "success"
}
```

