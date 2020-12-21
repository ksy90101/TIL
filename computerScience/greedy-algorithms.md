# Greedy Algorithms(탐욕 알고리즘)

## Greedy Algorithms(탐욕 알고리즘)

- 문제를 해결하는 과정에서 그 순간순간마다 최적이라고 생각되는 결정을 하는 방식으로 진행하여 최종 해답에 도달하는 문제 해결 방식

![greedy-algorithms-1.png](https://github.com/ksy90101/TIL/blob/master/computerScience/image/greedy-algorithms-1.png?raw=true)

- 위의 사진에서 큰 요소를 찾을때 분기마다 제일 큰 값을 찾아 최종 해답을 찾아 나갈때 결과는 12이다. 실제는 99가 가장 크다. 이처럼 문제해결에서 최적 해답을 찾지 못한다.
- 즉, 순간 순간 마다 최선의 결정에 전체 문제의 최선의 해결책이 되지는 않는다.
- 위와 같은 단점이 있다만 가장 큰 장점은 속도이다. 위의 문제를 완전탐색으로 풀었다면 7개를 모두 확인했어야 하지만, 실제 Greedy를 사용하면 3개만 확인하면 된다는 장점이 있다.
- 따라서 이 방법이 몇몇 문제에서 최적화를 빠르게 산출해낼수 있다.
- 이러한 점은 DP(Dynamic Programming)과 서로 보완하는 개념으로 알려져 있다.

### 알고리즘 진행 순서

1. 해 선택(Selection Procedure) : 지금 당시에 가장 최적인 해를 구한 뒤, 이를 부분해 집합에 추가
2. 적절성 검사 (Feasibility Check) : 새로운 부분해 집합이 적절한지 검사
3. 해 검사(Solution Check): 새로운 부분해 집합이 문제의 해가 되는지 검사한다. 아직 문제의 해가 완성되지 않았다면 다시 1번으로 시작한다.

## 활용법

### 최소한의 동전으로 거스름돈 거슬러주기

- 예를들어 거스름돈이 550원일때, 50짜리 11개를 주는것보다는 50원짜리 1개, 500짜리 1개를 주는것이 더 좋다.
- 이때 거스름돈 총액은 동전의 집합이며 최적해는 동전의 개수가 최소가 되는 집합

### 풀이 방법

- 해선택
    - 가장 가치가 높은 동전을 우선 거스름돈으로 구성하면 동전의 개수가 줄어든다. 현재 고를수 있는 가장 가치가 높은 동전을 골라서 부분해 집합에 추가
- 적절성 검사
    - 부분해 집합이 거슬러 줄 금액을 초과하는지 검사. 초과한다면 가장 최근에 추가한 동전을 삭제하고 1로 돌아가서 현재보다 한 단계 작은 동전을 추가
- 해 검사
    - 부분해 집합이 거슬러 줄 금액과 일치하는지 검사하고 모자르면 1번으로 다시 돌아가라.

## 참고자료

[동적 계획법(Dynamic Programming)과 탐욕법(Greedy Algorithm)](https://velog.io/@cyranocoding/%EB%8F%99%EC%A0%81-%EA%B3%84%ED%9A%8D%EB%B2%95Dynamic-Programming%EA%B3%BC-%ED%83%90%EC%9A%95%EB%B2%95Greedy-Algorithm-3yjyoohia5)

[[알고리즘] Greedy Algorithm (탐욕 알고리즘)](https://janghw.tistory.com/entry/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-Greedy-Algorithm-%ED%83%90%EC%9A%95-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98)
