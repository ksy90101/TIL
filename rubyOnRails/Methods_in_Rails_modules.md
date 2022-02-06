# **Methods in Rails modules**

> 해당 글은 **[Methods in Rails modules](https://jadhavkavita.medium.com/methods-in-rails-modules-59021590b4c5)** 을 번역한 글입니다.
> 

모듈은 일반적으로 속성 값을 보유할수 있도록 상수를 그룹화 하는 데 사용합니다. 아래 예제에서 Order 클래스와 status 속성이 있다고 해봅시다.

Status는 pending(보류), confirmed(확인), cacelled(취소) 등을 가지고 있습니다.

```ruby
classOrder < ApplicationRecord

  module Status
    PENDING='Pending'
    CONFIRMED='Confirmed'
    CANCELLED='Cancelled'
    DECLINED='Declined'
    COMPLETED='Completed'
  end
	
  validates_inclusion_of :status, in: [Status::PENDING,Status::CONFIRMED,Status::CANCELLED,Status::DECLINED,Status::COMPLETED]

end

```

새로운 Order가 생기거나, Order가 업데이트 되면, Status 속성에 유효한 값이 포함되도록 해야 합니다. 이를 위해 Rails의 validation method인 validates_inclusion_of를 사용하고 있습니다. 새로운 Status를 추가하면, 유효성 검사도 변경해야 합니다.

위와 같은 상황을 피하기 위해 Status라는 모듈에 all이라는 메소드를 추가합니다.

```ruby
classOrder < ApplicationRecord

  module Status
    PENDING='Pending'
    CONFIRMED='Confirmed'
    CANCELLED='Cancelled'
    DECLINED='Declined'
    COMPLETED='Completed'

    def self.all
      constants.map { |const| const_get(const) }
    end
  end

  validates_inclusion_of :status, in: Status.all

end

```

다른 곳에서 사용할 메서드를 더 추가할 수도 있습니다.

```ruby
classOrder < ApplicationRecord

  module Status
    PENDING='Pending'
    CONFIRMED='Confirmed'
    CANCELLED='Cancelled'
    DECLINED='Declined'
    COMPLETED='Completed'

    def self.all
      constants.map { |const| const_get(const) }
    end

    def self.active
      [Status::PENDING, Status::CONFIRMED]
    end
  end

  validates_inclusion_of :status, in: Status.all

  def active?
    status.in?(Status.active)
  end
end

```

이러한 최소한의 코드로 실수를 피하기를 바래봅니다.
