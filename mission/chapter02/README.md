** 워크북을 토대로 erd와 쿼리 작성법에 대해 공부하다 보니 1주차 때 제출했던 erd가 많이 미숙하다고 판단하여 다시 생각해보며 쿼리를 작성했습니다! **

#### 1. 내가 진행중, 진행 완료한 미션 모아서 보는 쿼리(페이징 포함)

```sql
-- cursor paging 사용 --

SELECT
    mi.restaurant_id,
    mi.reward,
    mm.status
FROM
    member_mission as mm
    INNER JOIN member as m on mm.member_id = m.id
    INNER JOIN mission as mi on mm.mission_id = mi.id
WHERE
    mm.created_at < (
        SELECT
            created_at
        FROM
            mission
        WHERE
            id = (mission_id)
        )
    order by created_at desc limit ?;
```

<br>

#### 2. 리뷰 작성하는 쿼리 (사진의 경우는 일단 배제)

```sql
INSERT INTO review (
    member_id,
    restaurant_id,
    point,
    content,
    created_at,
    updated_at
) VALUES (
    (member_id),
    (restaurant_id),
    (point),
    (content),
    NOW(),
    NOW()
)
```

<br>

#### 3. 홈 화면 쿼리 (현재 선택된 지역에서 도전이 가능한 미션 목록, 페이징 포함)

```sql
-- offset paging 사용--

SELECT
    m.restaurant_id,
    m.reward,
    m.deadline,
    r.category_id
FROM
    mission as m
    INNER JOIN restaurant as r on m.restaurant_id = r.id
    INNER JOIN area as a on r.area_id = a.id
WHERE
    a.id = (area_id)
LIMIT ? OFFSET ?;
```

<br>

#### 4. 마이 페이지 화면 쿼리

```sql
-- 전화번호 미인증 시 null값 반환 --

SELECT
    m.name,
    m.email,
    m.phone_number,
    m.point
FROM
    member as m
WHERE
    m.id = (member_id);
```