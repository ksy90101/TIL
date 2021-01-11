# 🤔 CASE WHEN ~ THEN ~ ELSE END

## 서론

- Oracle, MySQL 등 모든 DBMS에서 가능하며  프로그래밍 언어에서 If ~ else if ~ else와 같은 CASE WHEN문에 대해서 알아보도록 하겠습니다.
- 해당 예제는 프로그래머스의 [중성화 여부 파악하기](https://programmers.co.kr/learn/courses/30/lessons/59409?language=oracle) 를 통해 알아보도록 하겠습니다.

## 형식

```sql
CASE 컬럼명(생략가능)
	WHEN 조건식 THEN 결과
	WHEN 조건식 THEN 결과
	ELSE 결과
END
```

- WHEN절에는 해당 조건을, THEN에는 true일 경우에 결과를 ELSE는 모든 조건을 해당하지 않을 경우 결과값을 적어주시면 됩니다.
- 만약 ELSE 부분이 없고 조건이 true가 아니라면 NULL을 반환하게 됩니다.

## 예제

- 위의 문제에서 중성화 여부(SEX_UPON_INTAKE에서 Neutered, Spayed가 있으면 중성화를 한 경우)를 나타내보도록 하겠습니다.

```sql
SELECT ANIMAL_ID, NAME, 
    CASE
        WHEN SEX_UPON_INTAKE LIKE '%Neutered%' OR SEX_UPON_INTAKE LIKE '%Spayed%' THEN 'O'
    ELSE 'X'
    END
AS 중성화
FROM ANIMAL_INS
ORDER BY ANIMAL_ID;
```

![case_when_then_else_end-1](https://github.com/ksy90101/TIL/blob/master/database/image/case_when_then_else_end-1.png?raw=true)
