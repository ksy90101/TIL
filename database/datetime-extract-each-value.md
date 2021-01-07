# ORACLE, MYSQL 날짜에서 각 값들을 추출하기 🧐

## 서론

- 프로그래머스의 [입양 시간 구하기(1)](https://programmers.co.kr/learn/courses/30/lessons/59412?language=oracle) 문제로 DATETIME 컬럼의 값에서 시간만 추출해서 데이터를 처리해야 하는 문제였습니다.
- 이 문제를 풀려면 DATETIME의 값을 시간을 추출하는 방법을 알아야 했으며, 이번 기회에 전체적으로 한번 알아보자고 느꼈습니다.
- 근데 큰 문제는 ORACLE과 MYSQL과 추출방법이 다르다는 점입니다.
- 아래에서 각자 살펴보도록 하겠습니다.
- 모든 테스트는 저 위의 프로그래머스 문제를 기준으로 합니다.(프로그래머스 문제를 직접 실행해 값을 살펴보도록 하겠습니다.
- 아래의 사진처럼 데이터가 있습니다.(아래 사진보다 더 많은 데이터가 있습니다.)

![datetime-extract-each-value-1](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-1.png?raw=true)

- 보기 편하도록 GROUP BY, ORDER BY를 사용합니다.

## MySQL

- 개인적으로 직관적으로 DATETIME의 일정 값을 추출할 수 있다고 생각합니다.
- YEAR(), MONTH(), DAY()==DAYOFMONTH(), HOUR(), MINUTE(), SECOND()
- 연도, 월, 일, 시, 분, 초
- 위의 함수를 이용해 아주 간단하게 추출할 수 있었습니다.

### 연도 추출 - YEAR(컬럼명)

```sql
SELECT YEAR(DATETIME) AS YEAR
FROM ANIMAL_OUTS
GROUP BY YEAR(DATETIME)
ORDER BY YEAR
```

![datetime-extract-each-value-2](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-2.png?raw=true)

### 월 추출 - MONTH(컬럼명)

```sql
SELECT MONTH(DATETIME) AS MONTH
FROM ANIMAL_OUTS
GROUP BY MONTH(DATETIME)
ORDER BY MONTH
```

![datetime-extract-each-value-3](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-3.png?raw=true)

### 일 추출 - DAY(컬럼명) == DATOFMONTH(컬럼명)

```sql
SELECT DAY(DATETIME) AS DAY
FROM ANIMAL_OUTS
GROUP BY DAY(DATETIME)
ORDER BY DAY
```

```sql
SELECT DAYOFMONTH(DATETIME) AS DAY
FROM ANIMAL_OUTS
GROUP BY DAYOFMONTH(DATETIME)
ORDER BY DAY
```

![datetime-extract-each-value-4](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-4.png?raw=true)

### 시간 추출 - HOUR(컬럼명)

```sql
SELECT HOUR(DATETIME) AS HOUR
FROM ANIMAL_OUTS
GROUP BY HOUR(DATETIME)
ORDER BY HOUR
```

![datetime-extract-each-value-5](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-5.png?raw=true)

### 분 추출 - MINUTE(컬럼명)

```sql
SELECT MINUTE(DATETIME) AS MINUTE
FROM ANIMAL_OUTS
GROUP BY MINUTE(DATETIME)
ORDER BY MINUTE
```

![datetime-extract-each-value-6](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-6.png?raw=true)

### 초 추출 - SECOND(컬럼명)

```sql
SELECT SECOND(DATETIME) AS SECOND
FROM ANIMAL_OUTS
GROUP BY SECOND(DATETIME)
ORDER BY SECOND
```

![datetime-extract-each-value-7](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-7.png?raw=true)

## ORACLE

- MySQL 함수보다는 직관적이지는 않지만, 그래도 추출(EXTACT)함수를 사용하며, 함수 안에 추출할 값 FROM 컬럼명 만 적으면 되기 때문에 그렇게 어렵지는 않습니다.
- 또한 추가적으로 DATE 타입은 날짜만, TIMESTAMP는 시간관련된 값이 되어 있다. 이 두개를 합쳐 놓은게 DATETIME인데, EXTRACT를 하기 위해서는 각 타입에 맞게 변경(CAST) 해줘야 한다.

### 연도 추출 - EXTRACT(YEAR FROM 컬럼명)

- 이때 컬럼 타입은 DATE

```sql
SELECT EXTRACT(YEAR FROM CAST(DATETIME AS DATE)) AS YEAR
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(YEAR FROM CAST(DATETIME AS DATE))
ORDER BY YEAR
```

![datetime-extract-each-value-2](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-2.png?raw=true)

### 월 추출 - EXTRACT(MONTH FROM 컬럼명)

- 이때 컬럼 타입은 DATE

```sql
SELECT EXTRACT(MONTH FROM CAST(DATETIME AS DATE)) AS MONTH
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(MONTH FROM CAST(DATETIME AS DATE))
ORDER BY MONTH
```

![datetime-extract-each-value-3](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-3.png?raw=true)

### 일 추출 -  EXTRACT(DAY FROM 컬럼명)

- 이때 컬럼 타입은 DATE

```sql
SELECT EXTRACT(DAY FROM CAST(DATETIME AS DATE)) AS DAY
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(DAY FROM CAST(DATETIME AS DATE))
ORDER BY DAY
```

![datetime-extract-each-value-4](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-4.png?raw=true)

### 시간 추출 - EXTRACT(HOUR FROM 컬럼명)

- 이때 컬럼 타입은 TIMESTAMP

```sql
SELECT EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP)) AS HOUR
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(HOUR FROM CAST(DATETIME AS TIMESTAMP))
ORDER BY HOUR
```

![datetime-extract-each-value-5](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-5.png?raw=true)

### 분 추출 - EXTRACT(MINUTE FROM 컬럼명)

- 이때 컬럼 타입은 TIMESTAMP

```sql
SELECT EXTRACT(MINUTE FROM CAST(DATETIME AS TIMESTAMP)) AS MINUTE
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(MINUTE FROM CAST(DATETIME AS TIMESTAMP))
ORDER BY MINUTE
```

![datetime-extract-each-value-6](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-6.png?raw=true)

### 초 추출 - EXTRACT(MINUTE FROM 컬럼명)

- 이때 컬럼 타입은 TIMESTAMP

```sql
SELECT EXTRACT(SECOND FROM CAST(DATETIME AS TIMESTAMP)) AS SECOND
FROM ANIMAL_OUTS A
GROUP BY EXTRACT(SECOND FROM CAST(DATETIME AS TIMESTAMP))
ORDER BY SECOND
```

![datetime-extract-each-value-7](https://github.com/ksy90101/TIL/blob/master/database/image/datetime-extract-each-value-7.png?raw=true)
