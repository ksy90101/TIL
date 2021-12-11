# [Comparing Each vs Select vs Map vs Collect in Ruby(루비의 Each vs Select vs Map vs Collect 비교)](https://medium.com/ruby-daily/comparing-each-vs-select-vs-map-vs-collect-in-ruby-b74e9f3b85c6)

소프트웨어 엔지니어는 언어의 모든 메서드를 외울필요는 없습니다. 그러나 이것은 일반적인 개념이 아닌 고급개념에 적용되는 말입니다. 만약 loop를 하기 위해 어떻게 해야 할까요? 루비의  `#each`, `#select`, `#collect` , `#map` 를 살펴보도록 하겠습니다.

## **Each**

간단한 컬렉션을 반복하는 방법입니다.

```ruby
[1,2,3].each {|x| puts x + 1 }
=> 2
=> 3
=> 4
```

그러나 `#map` 과 같이 다른 변수에 할당은 할수가 없습니다.

```ruby
a = [1,2,3].each {|x| x + 1}
puts a.inspect
```

즉, `+1` 이 원래 객체의 영향을 주고 있지 않습니다.

## **Select**

블록내의 조건으로 필터링된 새로운 배열을 반환합니다.

```ruby
a = [1,2,3,4,5]
a = a.select {|x| x > 2}
puts a.inspect
=> [3, 4, 5]
```

`#select!` 를 사용하면 다른변수에 할당하는 방식이 아닌 원래 객체를 변경하는 방식도 사용 가능합니다.

```ruby
a = [1,2,3,4,5]
a.select! {|x| x > 3}
puts a.inspect
=> [4, 5]
```

## **Map**

`#map` 은 블록에서 빌드된 새로운 객체를 반환합니다. 원래 배열의 값에 대한 작업을 수행하고 그에 대한 결과값을 저장할 수 있습니다.

```ruby
a = [1,2,3,4,5,6]
a = a.map {|x| x + 1}
puts a.inspect
=> [2, 3, 4, 5, 6, 7]
```

`#map!` 를 사용하면 동일 변수에 할당할 필요가 없으며, 원래 객체에 값이 할당됩니다.

```ruby
a = [1,2,3,4,5,6]
a.map! {|x| x + 1}
puts a.inspect
=> [2, 3, 4, 5, 6, 7]
```

## **Collect**

단순히 `#map` 의 별칭일 뿐이며, 동일한 동작을 합니다.

```ruby
a = [1,2,3,4,5,6]
a = a.collect {|x| x + 1}
puts a.inspect
=> [2, 3, 4, 5, 6, 7]

a = [1,2,3,4,5,6]
a.collect! {|x| x + 1}
puts a.inspect
=> [2, 3, 4, 5, 6, 7]
```

## **Each_with_index**

파이썬에서 `enumerate()` 와 비슷한 루비의 메서드로 반복되는 값의 인덱스를 확인할 수 있습니다.

```ruby
pets = ['dog','fish','cat','frog','pig']pets.each_with_index do |val, idx|
 puts idx.to_s + ": " + val
end

=> 0: dog
=> 1: fish
=> 2: cat
=> 3: frog
=> 4: pig
```

위의 예제는 인덱스와 값을 연결하였습니다.

현재 값의 앞과 뒤의 값을 연결하였습니다.

```ruby
pets = ['dog','fish','cat','frog','pig']pets.each_with_index do |val, idx|
 puts pets[idx-1] + ' -> ' + val
end

=> pig -> dog
=> dog -> fish
=> fish -> cat
=> cat -> frog
=> frog -> pig
```

전체 배열을 여러번 반복을 피해야 하는 경우에 이 부분을 많이 사용할것 같습니다.

## 결론

이것을 구현할때 차이점을 찾는것은 쉽지만 다른 사람의 코드를 읽고 코드가 재빠르게 작성해야 할 때 이해해야 할때가 필요합니다. 직관적인 이해가 없다면 연결된 메서드에 긴 목록이 실제로 출력값을 예측하는 것이 쉽지 않을 수 있습니다.
