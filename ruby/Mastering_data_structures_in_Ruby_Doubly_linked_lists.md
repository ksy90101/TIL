# [Mastering data structures in Ruby — Doubly linked lists](https://medium.com/amiralles/mastering-data-structures-in-ruby-doubly-linked-lists-e5e5aff5ff67)(Ruby의 데이터 구조 마스터 하기 - 이중 연결 목록)

Single Linked List와 Doubly Linked List의 큰 차이점은각 요소들이 선행 요소에 대한 포인터를 보여한다는 점입니다. 이것으로 꼬리에서 머리로 리스트를 통과하고 일정 시간에 요소를 제거할 수 있습니다.

[Singly Linked List](https://medium.com/amiralles/mastering-data-structures-in-ruby-linked-lists-708347a30360)에 대해서는 이 글을 읽어보세요.

위 글을 바탕으로 이 글을 작성했습니다.

Doubly Linked List 요소는 세 가지 속동을 포함하는 노드로 표시됩니다.

- 요소가 보유하는 값입니다.
- 목록에 다음 노드에 대한 포인터입니다.
- 목록의 이전 노드에 대한 포인터 입니다.

Sigly Linked List와 마찬가지로 첫번째 요소를 머리라고 부르고 마지막 요소를 꼬리라고 합니다.

Doubly Linked List의 경계는 첫 번째 요소의 이전 포인터와 마지막 요소의 다음 포인터로 구분되며 두 포인터 모두 `nil` 로 설정해야 합니다.

```
X <-[prev|data|next] <-> [prev|data|next] <-> [prev|data|next] -> X
```

연산의 복잡성에 관해서는 Singly와 Doubly Linked List가 거의 유사하게 동작하지만 유일하게 다른 점은 `remove` 메서드입니다. Doubly Likned List에서는 각 노드의 이전 노드에 대한 포인터를 보유함으로 제거(remove)에 관해서 O(1)을 가지게 됩니다.

## **Doubly linked lists interface**

아래 표를 보면 Doubly Linked List의 인터페이스는 Singly Liked List와 거의 같다는걸 볼 수 있습니다. 즉, [Singly Linked List](https://gist.github.com/amiralles/48a3198f8800b42f0b1de4dc00a3e4c8)로 Doubly Linked List를 확장할 수 있다는 것입니다.

(추가하거나 재 정의 해야 하는 메서드는 굵은 텍스트로 강조되어 있습니다.)

![https://miro.medium.com/max/1400/1*GoSwiA-2tQNhjv7wlxAszw.png](https://miro.medium.com/max/1400/1*GoSwiA-2tQNhjv7wlxAszw.png)

Singly Linked List에서 상속된 메서드에 대한 자세한 내용은 [이 글](https://medium.com/amiralles/mastering-data-structures-in-ruby-linked-lists-708347a30360)을 참고해주세요.

### **Insert**

List에 값을 삽입할때 새 노드를 만듭니다. List에 노드가 없으면 새로운 노드가 List의 헤드가 됩니다. 그렇지 않으면 List의 맨 뒤에 추가합니다.

List는 머리와 꼬리에 대한 포인터를 유지하므로 이 메서드는 O(1) 입니다.

List에 요소가 포함되어 있으면 삽입하려는 노두의 이전 포인터가 목록의 꼬리를 가리키도록 하면 됩니다.

```ruby
def insert data
    node = Node.new data
    
		unless head
        self.head = node
    else
        node.prev = self.tail
        self.tail.next = node
    end
		
    self.tail = node
    self.length += 1
end
```

### **Remove**

List에서 노드를 제거하고 요소를 함께 유지하도록 포인터를 조정합니다.

제거해야 하는 노드가 목록의 헤드인 경우에는 아래 두 가지 상황을 처리해야 합니다.

1. 헤드는 List의 유일한 노드입니다. 머리와 꼬리를 nil로 설정하고 완료합니다.
2. 헤드는 List의 유일한 노드가 아닙니다. 헤드 옆에 있는 노드가 새 헤드가 되고 원래 헤드가 범위를 벗어납니다.

제거해야 하는 노드가 List의 맨 위에 있지 않으면 대상 노드를 범위에서 벗어나도록 포인터만 조장하면 됩니다. 먼저 대상 노드 앞에 있는 노드의 다음 포인터가 대상 옆에 있는 노드를 가리키도록 설정하고, 대상 노드 옆에 있는 이전 포인터가 이전 노드를 가리키도록 설정하면 됩니다.

```ruby
def remove node
    return nil unless node 
		
		if node == head
        if head.next.nil?
            self.head = self.tail = nil
        else
            self.head = self.head.next
        end
    else
        p = node.prev
        n = node.next
        p&.next = n
        n&.prev = p
    end
		
    self.length -= 1
end
```

위 코드에서 제거하려는 요소 앞에 있는 노드를 찾기 위해 List를 탐색할 필요 없기 때문에 이 작업은 O(1) 입니다.

### **Cat**

이 메서드는 두 개의 List를 결합하기 때문에 4 단계로 나눠집니다.

1. 현재 List의 꼬리에 추가할 List의 머리의 이전 포인터를 가리킵니다.
2. 현재 List의 꼬리 부분의 다음 포인터가 추가할 List의 머리 부분을 가리키도록 합니다.
3. 현재 List의 꼬리를 우리가 추가할 List의 꼬리로 설정합니다.
4. 현재 List의 길이를 조정합니다.

이는 O(1) 입니다.

```ruby
def cat list
    return nil unless list    

		list.head.prev = self.tail
    self.tail.next = list.head
    self.tail = list.tail
    self.length += list.length
end
```

## **Find Last**

이 메서드는 마지막 요소를 찾을수 있습니다. (또는 꼬리에서 시작하는 첫번째 요소)

마지막 요소를 찾는 방법은 해당 메서드가 true이거나 List가 끝날때까지 각 요소에 조건문을 걸어 꼬리에서 머리로 이동하면 됩니다.

이 메서드는 List를 순회해야 하기 때문에 O(n) 입니다.

```ruby
def find_last &predicate
    return nil unless block_given?

    current = self.tail

    while current
        return current if predicate.call(current)
        current = current.prev
    end
end
```

이 방법이 잘 동작하는지 보려면 숫자 목록이 있고 3의 마지막 항목을 찾으려고 한다면

일반적으로 아래와 같은 방법을 사용할 것입니다.

```ruby
e = nil
    current = list.tail
    while current
        if current.data == 3
            e = current.data
            break
        end
        current = current.prev
    end
```

그러나 이제 우아하게 관용적인 Ruby의 방법을 이용하는게 좋습니다.

```ruby
e = list.find_last { |item| item.data == 3; }
```

### **Reverse Each**

이 메서드는 List의 끝에 도달할 때까지 한번에 하나의 항목을 생성하면서 꼬리에서 머리로 List를 탐색합니다.

이 방법은 Singly Linked List에서 사용한 방법과 유사합니다. 차이점은 List의 머리에서 시작하는 대신 꼬리에서 시작해 현재 노드가 0이 될 때까지 뒤로 이동합니다.

- 이전 요소를 생성하는 복잡성은 O(1)입니다.
- 전체 목록을 생성하는 복잡성은 O(n)입니다.

```ruby
def reverse_each
    return nil unless block_given?

    current = self.tail

    while current
        yield current
        current = current.prev
    end
end
```

### **Reverse Print**

이 메서드는 현재 List의 내용을 거꾸로 하는 것입니다.

```ruby
def reverse_print
    if self.length == 0
        puts "empty"
    else
        self.reverse_each { |item| puts item.data }
    end
end
```

# **Doubly Linked List를 사용하는 경우**

Doubly Linked List는 wraparound 없이 (작은) 요소 시퀀스를 앞뒤로 이동해야 할 때 사용합니다.

마지막으로 remove가 O(1)이기 떄문에 많은 삭제 작업을 처리해야 할 경우 좋은 선택일 수 있습니다.

코드가 궁금하다면 아래를 참고해주세요.

[https://gist.github.com/amiralles/874feaa611c1316b7f99155b70822fa3](https://gist.github.com/amiralles/874feaa611c1316b7f99155b70822fa3)
