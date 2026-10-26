# SQL 실행 결과

실행 순서: 완성한 00-setup → 01-queries → 00-setup → 02-crud → 00-setup → 03-constraints.
각 항목에 실제 실행한 SQL과 실제 결과(텍스트 또는 캡처)를 첨부하세요.
예상 결과를 그대로 복사한 것만으로 실행을 확인할 수 없습니다.

## 준비: 외래 키 1 / 영화 6건 / 찜 8건

```sql
PRAGMA foreign_keys;
```

```
╭──────────────╮
│ foreign_keys │
╞══════════════╡
│ 1 │
╰──────────────╯
```

```sql
SELECT * FROM movies ORDER BY id ASC;
```

```
╭─────┬────────────────────╮
│ id │ title │
╞═════╪════════════════════╡
│ 101 │ 인셉션 │
│ 102 │ 인터스텔라 │
│ 103 │ 다크 나이트 │
│ 104 │ 테넷 │
│ 105 │ 어벤져스 │
│ 106 │ 어벤져스: 엔드게임 │
╰─────┴────────────────────╯
```

```sql
SELECT * FROM favorites ORDER BY id ASC;
```

```
╭────┬────────┬─────────┬─────────────────────╮
│ id │ userId │ movieId │ memo │
╞════╪════════╪═════════╪═════════════════════╡
│ 1 │ user-a │ 101 │ 주말에 볼 영화 │
│ 2 │ user-b │ 101 │ 친구가 추천한 영화 │
│ 3 │ user-a │ 102 │ │
│ 4 │ user-c │ 103 │ 다시 보고 싶은 영화 │
│ 5 │ user-a │ 104 │ 가족과 볼 영화 │
│ 6 │ user-b │ 105 │ │
│ 7 │ user-c │ 101 │ OST가 좋은 영화 │
│ 8 │ user-a │ 106 │ 친구와 볼 영화 │
╰────┴────────┴─────────┴─────────────────────╯
```

## 조건 조회 05·06·07·08

TODO 05
```sql
SELECT userId, memo FROM favorites WHERE movieId = 101;
```

```
╭────────┬────────────────────╮
│ userId │        memo        │
╞════════╪════════════════════╡
│ user-a │ 주말에 볼 영화     │
│ user-b │ 친구가 추천한 영화 │
│ user-c │ OST가 좋은 영화    │
╰────────┴────────────────────╯
```

TODO 06
```sql
SELECT memo FROM favorites WHERE userId = 'user-a' AND movieId = 104;
```

```
╭────────────────╮
│      memo      │
╞════════════════╡
│ 가족과 볼 영화 │
╰────────────────╯
```

TODO 07
```sql
SELECT movieId, memo FROM favorites WHERE userId = 'user-c' ORDER BY movieId DESC LIMIT 1;
```

```
╭─────────┬─────────────────────╮
│ movieId │        memo         │
╞═════════╪═════════════════════╡
│     103 │ 다시 보고 싶은 영화 │
╰─────────┴─────────────────────╯
```

TODO 08
```sql
SELECT movieId, memo FROM favorites WHERE userId = 'user-c' ORDER BY movieId DESC;
```

```
╭─────────┬─────────────────────╮
│ movieId │        memo         │
╞═════════╪═════════════════════╡
│     103 │ 다시 보고 싶은 영화 │
│     101 │ OST가 좋은 영화     │
╰─────────┴─────────────────────╯
```

## 빈 메모 JOIN·전체 집계·사용자별 집계 14·15·16

TODO 14
```sql
SELECT favorites.userId, movies.title, favorites.memo
FROM favorites
JOIN movies ON favorites.movieId = movies.id
WHERE favorites.memo = ''
ORDER BY favorites.id ASC;
```

```
╭────────┬────────────┬──────╮
│ userId │   title    │ memo │
╞════════╪════════════╪══════╡
│ user-a │ 인터스텔라 │      │
│ user-b │ 어벤져스   │      │
╰────────┴────────────┴──────╯
```

TODO 15
```sql
SELECT COUNT(*) AS emptyMemoCount FROM favorites WHERE memo = '';
```

```
╭────────────────╮
│ emptyMemoCount │
╞════════════════╡
│              2 │
╰────────────────╯
```

TODO 16
```sql
SELECT userId, COUNT(*) AS emptyMemoCount
FROM favorites
WHERE memo = ''
GROUP BY userId
ORDER BY userId ASC;
```

```
╭────────┬────────────────╮
│ userId │ emptyMemoCount │
╞════════╪════════════════╡
│ user-a │              1 │
│ user-b │              1 │
╰────────┴────────────────╯
```

## CRUD 09·10·11
추가: 9건 / 수정: id 30의 memo만 변경 / 삭제: 초기 8건 복원

TODO 09 추가
```sql
INSERT INTO favorites (id, userId, movieId) VALUES (30, 'user-b', 106);
SELECT * FROM favorites WHERE id = 30;
```

```
╭────┬────────┬─────────┬──────╮
│ id │ userId │ movieId │ memo │
╞════╪════════╪═════════╪══════╡
│ 30 │ user-b │     106 │      │
╰────┴────────┴─────────┴──────╯
```

```sql
SELECT COUNT(*) AS favoriteCount FROM favorites;
```

```
╭───────────────╮
│ favoriteCount │
╞═══════════════╡
│             9 │
╰───────────────╯
```

TODO 10 수정
```sql
SELECT * FROM favorites WHERE id = 30;
```

```
╭────┬────────┬─────────┬──────╮
│ id │ userId │ movieId │ memo │
╞════╪════════╪═════════╪══════╡
│ 30 │ user-b │     106 │      │
╰────┴────────┴─────────┴──────╯
```

```sql
UPDATE favorites SET memo = '주말에 다시 보기' WHERE id = 30;
SELECT * FROM favorites WHERE id = 30;
```

```
╭────┬────────┬─────────┬──────────────────╮
│ id │ userId │ movieId │       memo       │
╞════╪════════╪═════════╪══════════════════╡
│ 30 │ user-b │     106 │ 주말에 다시 보기 │
╰────┴────────┴─────────┴──────────────────╯
```

TODO 11 삭제
```sql
SELECT * FROM favorites WHERE id = 30;
```

```
╭────┬────────┬─────────┬──────────────────╮
│ id │ userId │ movieId │       memo       │
╞════╪════════╪═════════╪══════════════════╡
│ 30 │ user-b │     106 │ 주말에 다시 보기 │
╰────┴────────┴─────────┴──────────────────╯
```

```sql
DELETE FROM favorites WHERE id = 30;
SELECT * FROM favorites WHERE id = 30;
```

```
╭────┬────────┬─────────┬──────╮
│ id │ userId │ movieId │ memo │
╘════╧════════╧═════════╧══════╛
```
0행

```sql
SELECT * FROM favorites ORDER BY id ASC;
```

```
╭────┬────────┬─────────┬─────────────────────╮
│ id │ userId │ movieId │        memo         │
╞════╪════════╪═════════╪═════════════════════╡
│  1 │ user-a │     101 │ 주말에 볼 영화      │
│  2 │ user-b │     101 │ 친구가 추천한 영화  │
│  3 │ user-a │     102 │                     │
│  4 │ user-c │     103 │ 다시 보고 싶은 영화 │
│  5 │ user-a │     104 │ 가족과 볼 영화      │
│  6 │ user-b │     105 │                     │
│  7 │ user-c │     101 │ OST가 좋은 영화     │
│  8 │ user-a │     106 │ 친구와 볼 영화      │
╰────┴────────┴─────────┴─────────────────────╯
```

## 제약조건 A·B·C·D
각 SQL, 성공/실패, 실제 오류 메시지, 이유를 기록하세요.
D 직후 찜 9건과 영화 106 유지, 정리 후 초기 8건을 확인하세요.

A
```sql
INSERT INTO favorites (id, userId, movieId) VALUES (40, 'user-a', 101);
```

결과: 실패

```
Error near line 1: UNIQUE constraint failed: favorites.userId, favorites.movieId
```

이유: user-a가 101번 영화를 이미 찜하고 있어서 UNIQUE(userId, movieId)에 걸렸습니다.

B
```sql
INSERT INTO favorites (id, userId, movieId) VALUES (41, 'user-b', 106);
```

결과: 성공

```
출력 없음
```

이유: 106번은 user-a가 찜했지만 사용자가 달라서 들어갔습니다.

C
```sql
INSERT INTO favorites (id, userId, movieId) VALUES (42, 'user-b', 999);
```

결과: 실패

```
Error near line 1: FOREIGN KEY constraint failed
```

이유: movies에 999번 영화가 없어서 외래 키에 걸렸습니다.

D
```sql
DELETE FROM movies WHERE id = 106;
```

결과: 실패

```
Error near line 1: FOREIGN KEY constraint failed
```

이유: favorites에서 106번 영화를 참조하고 있어서 삭제되지 않았습니다.

D 실행 후 확인
```sql
SELECT * FROM favorites ORDER BY id ASC;
SELECT COUNT(*) AS favoriteCount FROM favorites;
SELECT * FROM movies WHERE id = 106;
```

```
(여기에 위 세 줄을 실행한 결과를 붙여 넣으세요)
```

정리
```sql
DELETE FROM favorites WHERE id = 41;
SELECT * FROM favorites ORDER BY id ASC;
```

```
╭────┬────────┬─────────┬─────────────────────╮
│ id │ userId │ movieId │        memo         │
╞════╪════════╪═════════╪═════════════════════╡
│  1 │ user-a │     101 │ 주말에 볼 영화      │
│  2 │ user-b │     101 │ 친구가 추천한 영화  │
│  3 │ user-a │     102 │                     │
│  4 │ user-c │     103 │ 다시 보고 싶은 영화 │
│  5 │ user-a │     104 │ 가족과 볼 영화      │
│  6 │ user-b │     105 │                     │
│  7 │ user-c │     101 │ OST가 좋은 영화     │
│  8 │ user-a │     106 │ 친구와 볼 영화      │
╰────┴────────┴─────────┴─────────────────────╯
```
