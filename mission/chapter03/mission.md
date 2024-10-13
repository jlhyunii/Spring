### 홈 화면

로그인한 유저만의 홈 화면이 떠야 한다고 생각해서 {user-id}를 추가함.
따라서 로그인 된 사용자가 **나 로그인 된 상태야!** 하고 알려줘야 함. >> Request Header 필요

1. API Endpoint
```
GET /api/home/{user-id}
```

2. Query String
```
없음
```

3. Request Body
```
없음
```

4. Request Header
```
Authorization : accessToken (String)
```

<br>

### 마이 페이지 리뷰 작성

유저마다 마이 페이지에서 리뷰 작성이 가능하기에 로그인 된 상태여야 함. >> Request Header 필요

1. API Endpoint
```
POST /api/my-page/reviews/registration
```

2. Query String
```
없음
```

3. Request Body
```json
{
  "point" : "별점",
  "content" : "리뷰내용",
  "image" : "사진"
}
```

4. Request Header
```
Authorization : accessToken (String)
```

<br>

### 미션 목록 조회(진행중, 진행 완료)

1. API Endpoint
```
GET /api/users/missions
```

2. Query String
```
completed : ?success='1', progressing : ?success='0'
```

3. Request Body
```
없음
```

4. Request Header
```
Authorization : accessToken (String)
```

<br>

### 미션 성공 누르기

미션의 성공여부 부분만 1로 바꾸어 성공 상태로 갱신함. >> PATCH

1. API Endpoint
```
PATCH /api/stores/request/{mission-id}
```

2. Query String
```
없음
```

3. Request Body
```json
{
    "success" : "1"
}
```
4. Request Header
```
Authorization : accessToken (String)
```

<br>

### 회원 가입 하기(소셜 로그인 고려 X)

1. API Endpoint
```
POST /api/users/sign-up
```

2. Query String
```
없음
```

3. Request Body
```json
{
    "email" : "이메일",
    "password" : "비밀번호",
    "name" : "이름",
    "gender" : "성별",
    "birth" : "생년월일",
    "address" : "주소",
    "preferred-food-category" : "음식 카테고리"
}
```

4. Request Header
```
없음
```