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
{"app_id": "tagmusic_app_id", "provider": "twitter", "id": "id-in-provider-we-use-this-as-user-indentity", "data": {"name":"aaa", "bio":"user_description", "user_img":"profile_img", "access_token":"abd29ejdksj39fikd"}}
```

*app_id :  발급받은 인증 키값
*provider : login platform 제공자 명 (필수)
*id : login platform 접근 id value (필수)
*data : 해당 user 관련 property data json value

* Return value
* userID로 JWT생성, JWT값은 access_token으로 보내준다.
```
{"status": 200, "user_id": 1234, "message": "logged in", "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJmcmVzaCI6dHJ1ZSwianRpIjoiZjhmNDlmMjUtNTQ4OS00NmRjLTkyOWUtZTU2Y2QxOGZhNzRlIiwidXNlcl9jbGFpbXMiOnt9LCJuYmYiOjE0NzQ0NzQ3OTEsImlhdCI6MTQ3NDQ3NDc5MSw"}
```

### [DELETE] 로그아웃 [jwt인증 필요]
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
* sort: latest, favor, dj, recommend, ... 과 같은 차트 정렬 속성, String, default: latest //현재 sort는 latest, play만 가능 default: play

* Return value
* 기본값 리턴 아이템 적을것
```
{
"status": 200, 
"id": 1,
"name": "imo",
"data": [{
            "album": "Howl's Moving Castle (2004)",
            "artist": "Joe Hisaishi",
            "artist_id": 1,
            "cover": null,
            "filepath": "countries/Joe Hisaishi/Soundtrack/Joe Hisaishi - Howl's Moving Castle (2004)/25. Joe Hisaishi - The Boy Who Drank Stars.mp3",
            "id": 498,
            "rank": 1,
            "play_count" : 0
            "title": "The Boy Who Drank Stars"
        }, {}]
}
```

### 플레이리스트
#### [GET] Playlist 목록 [jwt인증 필요]
*플레이 리스트 페이지에서 로그인한 유저에 대한 목록을 뿌려줌*
```
/api/{version}/playlist?{page, page_count}
```
* page_count: 리스트 개수, int, default: 10
* page : 현재 페이지, int, default: 0

* Return value
```
{
  "data": [
    {
      "count": 3, 
      "deleted": "0", 
      "id": 2, 
      "title": "bbb", 
      "user_id": 1
    }
  ], 
  "status": 200
}
```
#### [POST] Playlist 등록/수정 [jwt인증 필요]
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
#### [DELETE] Playlist 삭제 [jwt인증 필요]
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

#### [POST] Playlist에 곡 추가 [jwt인증 필요]
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

#### [PUT] Playlist에서 곡 삭제 [jwt인증 필요]
*플레이리스트에 곡을 삭제*
```
/api/{version}/playlist/{id}
```
* id:곡이 삭제될 playlist id, int
* [{"song_id": 1234},...] 해당 값이 존재할 경우 플레이리스트에 속해있는 곡 삭제

* Return value
```
{
"status": 200,
"message": "success"
}
```

#### [GET] Playlist 곡 보기 [jwt인증 필요]
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
                  "order_no" : 1,
                  "album": "Melodyphony (2010)",
                  "artist": "Joe Hisaishi",
                  "artist_id": 1,
                  "cover": null,
                  "filepath": "countries/Joe Hisaishi/Albums/Joe Hisaishi - Melodyphony (2010)/03. Joe Hisaishi - Kiki's Delivery Service.mp3",
                  "song_id": 231,
                  "title": "Kiki's Delivery Service"
               }, {
                "order_no" : 2,
                "album": "Starlight Theater",
                "artist": "Starlight Kuku Gumi",
                "artist_id": null,
                "cover": null,
                "filepath": "countries/JAPAN/201804/Starlight Kuku Gumi_Starlight Theater.mp3",
                "song_id": 120,
                "title": "Starlight Theater"
               }],
           "id":1,
           "title":"test",
           "user_id":1
         }
}
```

### 재생목록
#### [GET] 재생 목록 보기 [jwt인증 필요]
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
                "order_no" : 1,
                "album": null,
                "artist": null,
                "artist_id": 1,
                "cover": null,
                "filepath": "countries/CHINA/201605 이전/Fade - Alan Walker.mp3",
                "song_id": 1,
                "title": "Fade - Alan Walker.mp3"
                           }, {
                "order_no" : 2,
                "album": "古筝古琴十大金曲01",
                "artist": "",
                "artist_id": null,
                "cover": null,
                "filepath": "countries/CHINA/201710/高山流水（古筝独奏） - 群星.mp3",
                "song_id": 12,
                "title": "高山流水（古筝独奏）"
                          }],
           "user_id":1,
           "repeat_yn":"Y"
         }
}
```
#### [POST] 재생 목록에 playlist 음악 추가 [jwt인증 필요]
*플레이리스트의 음악 리스트를 재생 목록에 추가*
```
/api/{version}/played/playlist
{"playlist_id": 2}
```
* Return value
```
{
"status": 200, 
"message": "success"
}
```
#### [POST] 재생 목록에 음악 추가 [jwt인증 필요]
*플레이리스트의 음악 리스트를 재생 목록에 추가*
```
/api/{version}/played/song
[{"song_id": 300}, {"song_id":301}]
```
* Return value
```
{
"status": 200, 
"message": "success"
}
```
#### [PUT] 재생목록에서 곡 삭제 [jwt인증 필요]
*재생목록에서 선택된 곡 삭제*
```
/api/{version}/played
[{"song_id": 300}, {"song_id":301}]
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
* 로그인 여부에 따라 1분 미리듣기 또는 전곡 듣기

* Return value
```
{
  "data": {
        "album": null,
        "artist": null,
        "artist_id": 1,
        "cover": null,
        "filepath": "countries/CHINA/201605 이전/Fade - Alan Walker.mp3",
        "fileurl": "https://d16ku82ikb3chl.cloudfront.net/countries/CHINA/201605%20%EC%9D%B4%EC%A0%84/Fade%20-%20Alan%20Walker.mp3?Expires=1532463180&Signature=ZR8Pc2J8Z0qSTfeO1ylPo01Oa4Hwh-gexcWJggH6GMq5tVElg4Ohrq48dDKvRGTnq~ybWeuA9eosxVRYB6SqJtQvLQfMY1n64wNXgz2b5a2yXwyOAUIoI1wSIr9jDmvEHzTPQQTcQy8cr806ArueyAxQd1-e7HTDllxu2DQGpVKgV86RiHaFM05P0WS0IIeaJh-iT9OAZEHMSxKScejpFMDWry5JB0YKg-Ni~-v6QaqZrurVnyNQXgHjz12nYFuty~R9fZyxsYKm8LR4AXI3xa5z5dqeuz2BtVX4TMsYqCSBGfeAUWPng1KOtYjwpQ5rlQJPxhOToxnR6tO2Hw1qnA__&Key-Pair-Id=APKAIU7WAW37WWYKW33Q",
        "lyric": "tetetetetetetete",
        "song_id": 1,
        "title": "Fade - Alan Walker.mp3"
  }, 
  "status": 200
}
```
#### [GET] 음악 재생 [jwt인증 필요]
*음악 재생 버튼 클릭 시 로깅*
```
/api/{version}/song/{id}/play
```
* pid:음악 상세 정보를 요청할 pid, int

* Return value
```
{
"status" : 200,
"message" : "success"
}
```
#### [GET] 음악 중지 [jwt인증 필요]
*음악 정지 버튼 클릭 시 로깅*
```
/api/{version}/song/{id}/stop
```
* pid:로깅시 음악 상세 정보를 요청할 pid, int

* Return value
```
{
"status" : 200,
"message" : "success"
}
```
#### [GET] 음악 재시작 [jwt인증 필요]
*음악 재시작 버튼 클릭 시 로깅*
```
/api/{version}/song/{id}/restart
```
* pid:로깅시 음악 상세 정보를 요청할 pid, int

* Return value
```
{
"status" : 200,
"message" : "success"
}
```
#### [GET] 음악 재생 완료 [jwt인증 필요]
*음악 재생 완료 시 로깅*
```
/api/{version}/song/{id}/end
```
* pid:로깅시 음악 상세 정보를 요청할 pid, int
* 해당 음악이 재생 완료될 경우 play_count를 1 증가시킨다.

* Return value
```
{
"status" : 200,
"message" : "success"
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
            "album": null,
            "artist": null,
            "artist_id": 1,
            "cover": null,
            "filepath": "countries/CHINA/201605 이전/Fade - Alan Walker.mp3",
            "song_id": 1,
            "title": "Fade - Alan Walker.mp3"
            "genre_name": "test",
            "imo": 1,
            "updated_at": "2018-01-01" 
        },
        {
            "album": "Howl's Moving Castle (2004)",
            "artist": "Joe Hisaishi",
            "artist_id": null,
            "cover": null,
            "filepath": "countries/Joe Hisaishi/Soundtrack/Joe Hisaishi - Howl's Moving Castle (2004)/21. Joe Hisaishi - Family.mp3",
            "song_id": 494,
            "title": "Family"
            "genre_name": "test",
            "imo": 1,
            "updated_at": "2018-01-01"
        }
  ], 
  "status": 200
}

search_type = artist
{
    "data": [
        {
            "id": 2,
            "name": "test2",
            "song_cnt": 0,
            "user_img": "test2"
        },
        {
            "id": 1,
            "name": "test",
            "song_cnt": 5,
            "user_img": "test"
        }
    ],
    "status": 200
}

```


### 프로필
#### [GET] 자신 프로필 [jwt인증 필요]
*개인(자신) 프로필 조회에 따른 데이터 요청*
```
/api/{version}/profile/my
```
* Return value
```
private(자신)
{
    "data": {
        "bio": "test",
        "creator_id": 1,
        "data": "test",
        "id": 1,
        "name": "test",
        "points": 0,
        "rank": 0,
        "song_cnt": 5,
        "sponsor_cnt": 2,
        "support_cnt": 2,
        "user_img": "test"
    },
    "status": 200
}
```
#### [GET] 개인 프로필
*개인 프로필 조회에 따른 데이터 요청*
```
/api/{version}/profile/info/{id}
```
* id : 서버의 저장된 아이디와 해당 파라미터가 동일할 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다, int

* Return value
```
private(자신)
{
    "data": {
        "bio": "test",
        "creator_id": 1,
        "data": "test",
        "id": 1,
        "name": "test",
        "points": 0,
        "rank": 0,
        "song_cnt": 5,
        "sponsor_cnt": 2,
        "support_cnt": 2,
        "user_img": "test"
    },
    "status": 200
}
public(타인)
{
    "data": {
        "bio": "test",
        "creator_id": 1,
        "data": "test",
        "id": 1,
        "name": "test",
        "points": 0,
        "rank": 0,
        "song_cnt": 5,
        "sponsor_cnt": 2,
        "user_img": "test"
    },
    "status": 200
}
```
#### [GET]아티스트 프로필
*아티스트 프로필 조회(artist_id를 통한 조회)에 따른 데이터 요청*
```
/api/{version}/profile/artist/{artist_id}
```
* artist_id : 해당하는 artist_no를 통해 해당유저가 imo artist유저인지 일반 artist 유저인지(회원가입하지 않은 가수의 데이터) 판별하기 위한 value, int


* Return value
* return value에 creator_id가 null이 아닌경우 창작자로 후원하기 버튼이 활성화, 아닐경우 후원이 불가능하다. 해당 id가 null인경우 일반 artist유저이다.
```
imo유저 + 자신의 프로필
{
    "data": {
        "bio": "test",
        "creator_id": 1,
        "data": "test",
        "id": 1,
        "name": "test",
        "points": 0,
        "song_cnt": 5,
        "sponsor_cnt": 2,
        "support_cnt": 2,
        "user_id": 1,
        "user_img": "test"
    },
    "status": 200
}
imo유저 + 타인의 프로필
{
    "data": {
        "bio": "test",
        "creator_id": 1,
        "data": "test",
        "id": 1,
        "name": "test",
        "points": 0,
        "song_cnt": 5,
        "sponsor_cnt": 2,
        "user_id": 1,
        "user_img": "test"
    },
    "status": 200
}
일반 artist 유저
{
    "data": {
        "bio": "test2",
        "creator_id": null,
        "id": 2,
        "name": "test2",
        "song_cnt": 0,
        "sponsor_cnt": 0,
        "user_img": "test2"
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
            "song_list":[            {
                "album": "Howl's Moving Castle (2004)",
                "artist": "Joe Hisaishi",
                "artist_id": 1,
                "cover": null,
                "filepath": "countries/Joe Hisaishi/Soundtrack/Joe Hisaishi - Howl's Moving Castle (2004)/25. Joe Hisaishi - The Boy Who Drank Stars.mp3",
                "song_id": 498,
                "title": "The Boy Who Drank Stars"
            },
            {
                "album": "TonArt die 2te",
                "artist": "Sebastian Stritzl/Aries Caces",
                "artist_id": 1,
                "cover": null,
                "filepath": "countries/CHINA/최신 TOP100/201804/Konzert für Tuba und Klavier_ III. - Sebastian Stritzl,Aries Caces.mp3",
                "song_id": 30,
                "title": "Konzert für Tuba und Klavier: III."
            }],
         }
}
```

#### [GET]아티스트의 작품 목록
*아티스트 프로필(artist_id를 통한) 조회에 따른 개인 작품 목록 요청*
```
/api/{version}/profile/artist/song/{artist_id}
* artist_id : 해당하는 artist_no 가진 유저의 창작곡을 가져온다, int
```
* Return value
```
{"status": 200, 
 "data": {
            "song_list":[            {
                "album": "Howl's Moving Castle (2004)",
                "artist": "Joe Hisaishi",
                "artist_id": 1,
                "cover": null,
                "filepath": "countries/Joe Hisaishi/Soundtrack/Joe Hisaishi - Howl's Moving Castle (2004)/25. Joe Hisaishi - The Boy Who Drank Stars.mp3",
                "song_id": 498,
                "title": "The Boy Who Drank Stars"
            },
            {
                "album": "TonArt die 2te",
                "artist": "Sebastian Stritzl/Aries Caces",
                "artist_id": 1,
                "cover": null,
                "filepath": "countries/CHINA/최신 TOP100/201804/Konzert für Tuba und Klavier_ III. - Sebastian Stritzl,Aries Caces.mp3",
                "song_id": 30,
                "title": "Konzert für Tuba und Klavier: III."
            }],
         }
}
```

#### [GET]나의 후원자 목록
*개인 프로필 조회에 따른 개인 후원자 목록 요청*
```
/api/{version}/profile/sponsor/{id}
```
* id : 내 id로 artist_id를 찾아 , int

* Return value
```
{
  "data": {
    "sponsor_list": [
      {
                "created_at": "Wed, 18 Jul 2018 14:24:35 GMT",
                "name": "test",
                "points": 10,
                "user_id": 1,
                "user_img": "test"
      }
    ]
  }, 
  "status": 200
}
```

#### [GET]아티스트의 후원자 목록
*아티스트 프로필(artist_id) 조회에 따른 개인 후원자 목록 요청*
```
/api/{version}/profile/artist/sponsor/{artist_id}
```
* artist_id : 해당 artist에 대한 후원자 목록을 가져올 id, int

* Return value
```
{
  "data": {
    "sponsor_list": [
      {
                "created_at": "Tue, 17 Jul 2018 14:46:35 GMT",
                "name": "test",
                "points": 10,
                "user_id": 1,
                "user_img": "test"
      }
    ]
  }, 
  "status": 200
}
```
#### [GET]내가 후원한 목록 [jwt인증 필요]
*개인 프로필 조회에 따른 개인 후원 목록 요청*
*개인 프로필 또는 아티스트 프로필 조회에서 얻은 user_id값을 이용하여 호출한다.*
```
/api/{version}/profile/support/{id}
```
* id : 서버의 저장된 아이디와 해당 파라미터가 동일할 경우 나의 프로필, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), int
* id가 현재 유저의 아이디와 다를경우 호출시 json value로 에러 데이터를 반환한다.

* Return value
```
{
  "data": {
    "support_list": [
      {
        "artist_id": 1, 
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test",
        "user_img": "test",
        "points": 10
      }
    ]
  }, 
  "status": 200
}
```
#### [GET] 아티스트가 후원한 목록
*아티스트 프로필 조회에 따른 아티스트 후원 목록 요청*
```
/api/{version}/profile/artist/support/{artist_id}
```
* artist_id : 해당 id로 user_id를 찾아 user_id와 현재 나의 id가 같을 경우 나의 후원 목록을, 그외의 경우 타인의 프로필에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), int
* id가 현재 유저의 아이디와 다를경우 호출시 json value로 에러 데이터를 반환한다.

* Return value
```
{
  "data": {
    "support_list": [
      {
        "artist_id": 1, 
        "created_at": "Tue, 17 Jul 2018 14:46:35 GMT", 
        "name": "test",
        "user_img": "test",
        "points": 10
      }
    ]
  }, 
  "status": 200
}
```
#### [GET]개인 포인트 목록 요청 [jwt인증 필요]
*개인 포인트 사용 목록 요청*
*개인 프로필 또는 아티스트 프로필 조회에서 얻은 user_id값을 이용하여 호출한다.*
```
/api/{version}/profile/point/{id}
```
* id : 자신의 아이디일 경우 나의 포인트내역, 그외의 경우 타인의 포인트내역에 해당하는 값을 가져온다. (현재는 자신만이 가져 올 수 있음), int
* id가 현재 유저의 아이디와 다를경우 호출시 json value로 에러 데이터를 반환한다.

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
#### [PUT]포인트 후원 [jwt인증 필요]
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
  "data": [
    {
         "content": "testtesttesttest", 
        "created_at": "Thu, 19 Jul 2018 21:53:12 GMT", 
        "id": 1, 
        "title": "test", 
        "updated_at": "Thu, 19 Jul 2018 21:53:12 GMT"
    }
  ], 
  "status": 200
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
  "data": {
    "content": "testtesttesttest", 
    "created_at": "Thu, 19 Jul 2018 21:53:12 GMT", 
    "id": 1, 
    "title": "test", 
    "updated_at": "Thu, 19 Jul 2018 21:53:12 GMT"
  }, 
  "status": 200
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
#### [POST]작품 올리기 [jwt인증 필요]
*자신의 작품 올리기*
```
/api/{version}/song
{"title":"aaa", "file":"aaa.mp3", "cover":"bbb.jpg", "desc":"really Good Soung"}
```
* title, file, cover 필수 값
* Return value
```
{
"status": 200, 
"message": "success"
}
```
### 창작자
#### [POST]창작자 등록 [jwt인증 필요]
*창작자 등록 하기*
```
/api/{version}/creator
{"name":"aaa", "gender":0, "bio":"gexekjdlksjdkl", "birth" : "20101010", "phone":"01055555555", "email":"test@test.com",
 "provider":"facebook", "sns_id" : "asdiop", "homepage" :"aaa.com", "belongto":"aaa"
}
```
* name, gender, bio, birth, phone, email, provider, sns_id 필수 값 
* Return value
```
{
"status" : 200,
"message" : "success"
}
```

### 약관
#### [GET]이용약관 조회
*이용 약관 조회하기*
```
/api/{version}/term/{term}
```

term : 약관 종류 번호, int
*이용약관 0
*유료서비스 약관 1
*개인정보 수집 약관 2
*이벤트서비스 안내 수신 3

* Return value
```
{
"status" : 200,
"data" : {
     "pid" : 1,
     "title" : "test",
     "content" : "testtest"
     "created_id" : 1,
     "term" : 0
}
```
