# ****dicts의 get() 메소드와 default 인자****

```
# The get() method on dicts
# and its "default" argument

name_for_userid = {
    **382**: "Alice",
    **590**: "Bob",
    **951**: "Dilbert",
}

**def** **greeting**(userid):
    **return** "Hi %s!" % name_for_userid.get(userid, "there")

>>> greeting(**382**)
"Hi Alice!"

>>> greeting(**333333**)
"Hi there!"
```

### dictionary

-   딕셔너리 타입은 immutable한 키(key)와 mutable한 값(value)으로 맵핑되어 있는 순서가 없는 집합입니다.
-   일반적인 딕셔너리 타입의 모습입니다. 중괄호로 되어 있고 키와 값이 있습니다.

```
>>> {"a" : 1, "b":2}
{'a': 1, 'b': 2}

```

-   키로는 immutable한 값은 사용할 수 있지만, mutable한 객체는 사용할 수 없습니다.

### get(key\[, default\])

-   get() 메서드를 호출할때, dict에 키가 있는지 없는지 확인합니다.
-   만약 키가 없다면 default 값을 리턴해줍니다.
-   default값의 생략이 가능한데, 이 경우엔 None 입니다.
-   따라서, 키 값이 없어도 KeyError를 발생시키지 않는 장점을 얻을 수 있습니다.

### 참고글

-   [참고글 1](https://t.dripemail2.com/c/eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJkZXRvdXIiLCJpc3MiOiJtb25vbGl0aCIsInN1YiI6ImRldG91cl9saW5rIiwiaWF0IjoxNjY3MDcxMTc1LCJuYmYiOjE2NjcwNzExNzUsImFjY291bnRfaWQiOiI2MjE0NTAwIiwiZGVsaXZlcnlfaWQiOiJzYm1yYzNxODl1b3F0aDBuamlqNSIsInVybCI6Imh0dHBzOi8vd3d3LmdldGRyaXAuY29tL2RlbGl2ZXJpZXMvc2JtcmMzcTg5dW9xdGgwbmppajU_dmlld19pbl9icm93c2VyPXRydWUmX19zPWM5ZHRwaW9nNzZzdTZ2MWJyZ28yIn0.H86Aq7JhtX8Iz1xd8mRJ171NqxzeO1RmR6Px1aMm3sw)
