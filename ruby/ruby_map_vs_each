# Ruby의 map vs each

![image](https://user-images.githubusercontent.com/53366407/123496051-21130b00-d661-11eb-9772-d1d7a7b6c629.png)

## 🧐 두 개의 가장 큰 차이점

- 두 개의 가장 큰 차이점은 새로운 배열 반환 여부입니다.
- map은 새로운 배열을 반환하고, each는 새로운 배열을 반환하지 않습니다.
- 간단한 예제를 통해 살펴보도록 하겠습니다

## 1️⃣ map

```jsx
names  = [ "Dan", "Stella", "Elly" ]
upcase_names = names.map { |name| name.upcase }

puts names // [ "Dan", "Stella", "Elly" ]
puts upcase_names // [ "DAN", "STELLA", "ELLY" ]
```

- map은 새로운 배열을 반환하기 때문에 변환된 값이 나오는걸 확인할 수 있습니다.
- 즉, 원래 배열을 변환하지 않는 장점이 있습니다.
- 원래 배열도 같이 변경하고 싶다면 `map!` 을 사용하면 됩니다.

```jsx
names  = [ "Dan", "Stella", "Elly"]
names.map! { |name| name.upcase }

puts names // [ "DAN", "STELLA", "ELLY" ]
```

## 2️⃣ each

```jsx
names  = [ "Dan", "Stella", "Elly" ]
upcase_names = names.each { |name| name.upcase }

puts names // [ "Dan", "Stella", "Elly" ]
puts upcase_names // [ "Dan", "Stella", "Elly" ]
```

- each는 반환하지 않기 때문에 둘이 동일한 값이 나오는걸 확인할 수 있습니다.

## 3️⃣  collect

- map과 동일한 기능을 하는 collect도 있습니다.

```jsx
names  = [ "Dan", "Stella", "Elly" ]
upcase_names = names.collect { |name| name.upcase }

puts names // [ "Dan", "Stella", "Elly" ]
puts upcase_names // [ "DAN", "STELLA", "ELLY" ]
```
