# NamedRoute의 _path vs _url

## 서론

- RubyOnRails는 NamedRoute라는 쉽게 경로를 작성할 수 있는 방법을 제공합니다.

```ruby
# In config/routes.rb
root to: 'blogs#index'

# would recognize http://www.example.com/ as
params = { controller: 'blogs', action: 'index' }

# and provide these named routes
root_url   # => 'http://www.example.com/'
root_path  # => '/'
```

- routes를 작성할때 `as`라는 옵션을 제공해줘서 커스텀도 가능합니다.

```ruby
# In config/routes.rb
get '/login', to: 'accounts#login', as: 'login'

# With render, redirect_to, tests, etc.
redirect_to login_url
```

- 거기서도 2가지 방법을 제공하해주는데 _path와 _url 입니다.

## 상대 경로 / 절대 경로

- 상대 경로는 현재 주소를 기준으로 자신의 경로를 지정하게 됩니다.

```ruby
/login
```

- 위의 예제처럼 현재 URL을 기준으로 뒤에 경로를 붙이게 됩니다.
- 절대 경로는 프로토콜 및 서버 이름을 포함한 부분입니다.

```ruby
https://test.co.kr/login
```

## _path vs _url

- _path는 사이트 루트 상대 경로를 제공하게 됩니다.
- _url은 절대 경로를 제공하게 됩니다.

```ruby
# In config/routes.rb
root to: 'blogs#index'

# would recognize http://www.example.com/ as
params = { controller: 'blogs', action: 'index' }

# and provide these named routes
root_url   # => 'http://www.example.com/'
root_path  # => '/'
```

- _url의 절대 경로는 default는 현재 경로가 들어가며, `host` 매개변수를 이용해 host를 변경할 수 있습니다.

## 뭘 더 사용해야 하나?

- _path는 a링크의 href와 같이 URL에 암시적으로 연결된다는 의미로 사용하며, _url을 사용한다면 적지만 용량적으로 낭비가 될겁니다. (코드가 더 많아지기 때문에)
- [HTTP rfc2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.30)에 따르면 `Location` 는 기본 URI인 절대경로를 제공해줘야 한다고 나와있습니다. 따라서 이런 경우에는 _url을 써야 합니다.
- 또한 서브 도메인을 통해 여러개의 서비스를 운영하지만 앱은 하나인 경우에는 _url을 통해 서브 도메인에 맞게 호출하게 만드는 것이 좋습니다. (이 경우가 현업에서 실제 사용한 부분입니다.)

## 결론

- rails을 사용할때 다른 경로를 사용할 때 일반적으로 Named Route를 사용하지만, _path와 _url의 차이를 단순히 `host` 존재 여부로만 사용하였지만, 각각 사용해야 할 상황이 있다는것을 알게 되었습니다 🙂
