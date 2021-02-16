# RubyOnRails Getting Start(Blog 만들기) 1편

## 💼 서론

- [RubyOnRails Getting Started](https://guides.rubyonrails.org/v5.2/getting_started.html) 에 있는 간단한 게시판 CURD 및 댓글 기능까지 개발한 내용입니다
- Ruby version은 2.6.3을 사용합니다.
- Ruby On Rails version은 5.2.1을 사용합니다.

## 🍄 프로젝트 생성

- RubyMine을 이용해 `File -> new -> Project... -> Application` 를 이용해 생성할 수 있습니다.
- 또한 CLI을 이용해 `rails new 프로젝트명` 을 통해 생명할 수 있습니다.
- 저는 프로젝트 명을 blog라고 설정했습니다.

## 🎏 default Fild & Folder 구조

[[RubyOnRails] rails new File & Folder](https://rutgo-letsgo.tistory.com/265)

## 🎬 webServer Start

- bin폴더에 rails가 있고 rails에는 실행할 수 있도록 설정이 이미 되어 있습니다.
- 아래의 명령어를 이용하면 쉽게 webServer를 실행할 수 있습니다.

```shell
bin/rails server
```

- dafult port는 3000을 사용합니다.
- 아래 주소로 접속하면 아래와 같은 화면이 나오게 될것 입니다.

```
http://localhost:3000
```

![ruby_on_rails_getting_start_blog_1-1](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-1.png?raw=true)

- 많은 사람들과 심지어 고양이까지 여러분들이 레일즈를 사용하는것을 반기고 있습니다 😎
- `ctrl + c`를 통해 쉽게 서버를 중지할 수 있으며, 가장 큰 장점 중 하나는 서버가 구동 중에도 코드가 변경되면 자동으로 변경 사항을 업데이트 해주기 때문에 개발 시에는 중지할 필요가 많이 있지 않다는 점입니다.

## 👋 Hello, Rails

- 새로운 언어 및 프레임워크를 사용하면 Hello, World를 먼저 띄어 봐야 겠죠!?
- rails의 또 장점 중 하나는 `generate(g)` 로 controller와 view를 만들어 줍니다.
- 아래와 같이 명령어를 실행하면 Welcome이라는 컨트롤러와 index의 뷰를 생성해주며, 경로를 설정해줍니다.

```jsx
bin/rails generate controller Welcome index
```

- welcome_controller.rb

```ruby
class WelcomeController < ApplicationController
  def index
  end
end
```

- view(index.html.erb)에 아래와 같이 내용을 작성합니다.

```ruby
<h1>Hello, Rails!</h1>
```

## √ Root 설정

- `Root(/)` 에 접속하면 방금 작성한 Hello, Rails가 화면에 보이도록 설정하려고 한다.
- 이때 사용하는 파일은 `config/routes.rb` 입니다.

```ruby
Rails.application.routes.draw do
  get 'welcome/index'

  root 'welcome/index'
end
```

- 이렇게 하면 `Hello, Rails!` 화면이 나오게 될 겁니다.

## 📰 resource 설정

- Rails는 resources를 설정해 표준 REST API를 구성해줍니다.
- `config/routes.rb` 에 아래와 같이 추가한 후 routes 목록을 보면 REST API가 만들어진 걸 확인할 수 있습니다.

```ruby
Rails.application.routes.draw do
  get 'welcome/index'

  resources :articles

  root 'welcome#index'
end
```

- `bin/rails routes` 실행

```ruby
Prefix Verb   URI Pattern                                                                              Controller#Action
            welcome_index GET    /welcome/index(.:format)                                                                 welcome#index
                 articles GET    /articles(.:format)                                                                      articles#index
                          POST   /articles(.:format)                                                                      articles#create
                  article GET    /articles/:id(.:format)                                                                  articles#show
                          PATCH  /articles/:id(.:format)                                                                  articles#update
                          PUT    /articles/:id(.:format)                                                                  articles#update
                          DELETE /articles/:id(.:format)                                                                  articles#destroy
                     root GET    /                                                                                        welcome#index
```

## 📰 Article Create 만들기

- 일단 Article Contoller를 한번 만들어 보겠습니다.

```ruby
bin/rails g controller Articles
```

- articles_controller.rb

```ruby
class ArticlesController < ApplicationController
end
```

- 아래와 같이 new라는 함수를 정의하고 `/articles/new` 로 접속하면 아래와 같은 에러메시지가 나오게 됩니다.

```ruby
class ArticlesController < ApplicationController
  def new
    
  end
end
```

![ruby_on_rails_getting_start_blog_1-2](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-2.png?raw=true)

- 간단하게 설명하자면 new 라는 view를 가질것으로 예상하지만, new 라는 view가 없기 때문입니다.
- 그렇다면 간단하게 처리할 수 있는 방법은 당연히 view를 만들어 주는 것이죠
- views/articles/new.html.erb

```ruby
<h1>New Article</h1>
```

### Form

- form을 만들어 보겠습니다. `views/articles/new.html.erb` 에 아래와 같이 form을 추가해줍니다.

```ruby
<%= form_with scope: :article, local: true do |form| %>
  <p>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
  </p>
 
  <p>
    <%= form.label :text %><br>
    <%= form.text_area :text %>
  </p>
 
  <p>
    <%= form.submit %>
  </p>
<% end %>
```

![ruby_on_rails_getting_start_blog_1-3](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-3.png?raw=true)

- rails는 form_with으로 폼을 만들수 있습니다. 이때 scope로 무엇을 의미하는 form인지 알려주며 text_filed, text_area에게 어떤 필드를 저장할지 알려주면 됩니다.
- 다른 url로 설정하려고 할때 `url` 값을 넣어주면 됩니다.

```ruby
<%= form_with scope: :article, url: articles_path, local: true do |form| %>
```

- 기본적으로 rails는 form action에 REST API의 POST를 설정해주게 됩니다.
- 따라서 버튼을 누르면 create가 없다고 나오게 되는데, 이것은 위에서도 봤던 routes에서도 보면 create를 의미하는 POST를 발견할 수 있습니다. 이떄 우리는 아직 create를 정의해주지 않았습니다.

```ruby
Prefix Verb   URI Pattern             Controller#Action
articles GET    /articles(.:format)   articles#index
				 POST   /articles(.:format)   articles#create
```

### 게시글 생성 create 함수

- `articles_controller.rb` 에게 아래와 같이 create 함수를 정의해준다.

```ruby
class ArticlesController < ApplicationController
  def new

  end

  def create
    render plain: params[:article].inspect
  end
end
```

- 폼을 제출하면 아래와 같이 나오게 될 것입니다.

```ruby
<ActionController::Parameters {"title"=>"1234", "text"=>"1234"} permitted: false>
```

- 폼에서 입력한 article의 전체 param을 만아 올 수 있으며, inspect는 디버깅용으로 클래스 이름, 인스턴스의 object id 등을 알려주는 함수입니다.

## 👨‍🎨 모델 생성

- Rails의 모델은 단수를 사용하며 DB 테이블은 복수를 사용합니다.
- 아래의 명령어로 쉽게 model을 생성할 수 있습니다.

```ruby
$ bin/rails generate model Article title:string text:text
```

- 아래와 같이 model와 db migrate가 생성됩니다.
- migrate 파일명은 `YYYYMMDDHHMMSS_create_모델명.rb` 입니다.

```ruby
class Article < ApplicationRecord
end
```

```ruby
class CreateArticles < ActiveRecord::Migration[5.2]
  def change
    create_table :articles do |t|
      t.string :title
      t.text :text

      t.timestamps
    end
  end
end
```

- 루비는 위의 migrate 파일로 쉽게 DB 테이블을 만들고 수정할 수 있습니다. 아래의 명령어로 마이그레이션을 실행해봅시다.

```ruby
$ bin/rails db:migrate
```

```ruby
==  CreateArticles: migrating ==================================================
-- create_table(:articles)
   -> 0.0019s
==  CreateArticles: migrated (0.0020s) =========================================
```

## 🏠 article create DB 저장

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(params[:article])

    @article.save

    redirect_to @article
  end
end
```

- :article param을 이용해서 Article 모델을 만들고 save 함수로 DB로 저장
- 그러나 실행하면 에러가 발생합니다.

![https://s3-us-west-2.ama# RubyOnRails Getting Start(Blog 만들기) 1편

## 💼 서론

- [RubyOnRails Getting Started](https://guides.rubyonrails.org/v5.2/getting_started.html) 에 있는 간단한 게시판 CURD 및 댓글 기능까지 개발한 내용입니다
- Ruby version은 2.6.3을 사용합니다.
- Ruby On Rails version은 5.2.1을 사용합니다.

## 🍄 프로젝트 생성

- RubyMine을 이용해 `File -> new -> Project... -> Application` 를 이용해 생성할 수 있습니다.
- 또한 CLI을 이용해 `rails new 프로젝트명` 을 통해 생명할 수 있습니다.
- 저는 프로젝트 명을 blog라고 설정했습니다.

## 🎏 default Fild & Folder 구조

[[RubyOnRails] rails new File & Folder](https://rutgo-letsgo.tistory.com/265)

## 🎬 webServer Start

- bin폴더에 rails가 있고 rails에는 실행할 수 있도록 설정이 이미 되어 있습니다.
- 아래의 명령어를 이용하면 쉽게 webServer를 실행할 수 있습니다.

```shell
bin/rails server
```

- dafult port는 3000을 사용합니다.
- 아래 주소로 접속하면 아래와 같은 화면이 나오게 될것 입니다.

```
http://localhost:3000
```

![ruby_on_rails_getting_start_blog_1-1](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-1.png?raw=true)

- 많은 사람들과 심지어 고양이까지 여러분들이 레일즈를 사용하는것을 반기고 있습니다 😎
- `ctrl + c`를 통해 쉽게 서버를 중지할 수 있으며, 가장 큰 장점 중 하나는 서버가 구동 중에도 코드가 변경되면 자동으로 변경 사항을 업데이트 해주기 때문에 개발 시에는 중지할 필요가 많이 있지 않다는 점입니다.

## 👋 Hello, Rails

- 새로운 언어 및 프레임워크를 사용하면 Hello, World를 먼저 띄어 봐야 겠죠!?
- rails의 또 장점 중 하나는 `generate(g)` 로 controller와 view를 만들어 줍니다.
- 아래와 같이 명령어를 실행하면 Welcome이라는 컨트롤러와 index의 뷰를 생성해주며, 경로를 설정해줍니다.

```jsx
bin/rails generate controller Welcome index
```

- welcome_controller.rb

```ruby
class WelcomeController < ApplicationController
  def index
  end
end
```

- view(index.html.erb)에 아래와 같이 내용을 작성합니다.

```ruby
<h1>Hello, Rails!</h1>
```

## √ Root 설정

- `Root(/)` 에 접속하면 방금 작성한 Hello, Rails가 화면에 보이도록 설정하려고 한다.
- 이때 사용하는 파일은 `config/routes.rb` 입니다.

```ruby
Rails.application.routes.draw do
  get 'welcome/index'

  root 'welcome/index'
end
```

- 이렇게 하면 `Hello, Rails!` 화면이 나오게 될 겁니다.

## 📰 resource 설정

- Rails는 resources를 설정해 표준 REST API를 구성해줍니다.
- `config/routes.rb` 에 아래와 같이 추가한 후 routes 목록을 보면 REST API가 만들어진 걸 확인할 수 있습니다.

```ruby
Rails.application.routes.draw do
  get 'welcome/index'

  resources :articles

  root 'welcome#index'
end
```

- `bin/rails routes` 실행

```ruby
Prefix Verb   URI Pattern                                                                              Controller#Action
            welcome_index GET    /welcome/index(.:format)                                                                 welcome#index
                 articles GET    /articles(.:format)                                                                      articles#index
                          POST   /articles(.:format)                                                                      articles#create
                  article GET    /articles/:id(.:format)                                                                  articles#show
                          PATCH  /articles/:id(.:format)                                                                  articles#update
                          PUT    /articles/:id(.:format)                                                                  articles#update
                          DELETE /articles/:id(.:format)                                                                  articles#destroy
                     root GET    /                                                                                        welcome#index
```

## 📰 Article Create 만들기

- 일단 Article Contoller를 한번 만들어 보겠습니다.

```ruby
bin/rails g controller Articles
```

- articles_controller.rb

```ruby
class ArticlesController < ApplicationController
end
```

- 아래와 같이 new라는 함수를 정의하고 `/articles/new` 로 접속하면 아래와 같은 에러메시지가 나오게 됩니다.

```ruby
class ArticlesController < ApplicationController
  def new
    
  end
end
```

![ruby_on_rails_getting_start_blog_1-2](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-2.png?raw=true)

- 간단하게 설명하자면 new 라는 view를 가질것으로 예상하지만, new 라는 view가 없기 때문입니다.
- 그렇다면 간단하게 처리할 수 있는 방법은 당연히 view를 만들어 주는 것이죠
- views/articles/new.html.erb

```ruby
<h1>New Article</h1>
```

### Form

- form을 만들어 보겠습니다. `views/articles/new.html.erb` 에 아래와 같이 form을 추가해줍니다.

```ruby
<%= form_with scope: :article, local: true do |form| %>
  <p>
    <%= form.label :title %><br>
    <%= form.text_field :title %>
  </p>
 
  <p>
    <%= form.label :text %><br>
    <%= form.text_area :text %>
  </p>
 
  <p>
    <%= form.submit %>
  </p>
<% end %>
```

![ruby_on_rails_getting_start_blog_1-3](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-3.png?raw=true)

- rails는 form_with으로 폼을 만들수 있습니다. 이때 scope로 무엇을 의미하는 form인지 알려주며 text_filed, text_area에게 어떤 필드를 저장할지 알려주면 됩니다.
- 다른 url로 설정하려고 할때 `url` 값을 넣어주면 됩니다.

```ruby
<%= form_with scope: :article, url: articles_path, local: true do |form| %>
```

- 기본적으로 rails는 form action에 REST API의 POST를 설정해주게 됩니다.
- 따라서 버튼을 누르면 create가 없다고 나오게 되는데, 이것은 위에서도 봤던 routes에서도 보면 create를 의미하는 POST를 발견할 수 있습니다. 이떄 우리는 아직 create를 정의해주지 않았습니다.

```ruby
Prefix Verb   URI Pattern             Controller#Action
articles GET    /articles(.:format)   articles#index
				 POST   /articles(.:format)   articles#create
```

### 게시글 생성 create 함수

- `articles_controller.rb` 에게 아래와 같이 create 함수를 정의해준다.

```ruby
class ArticlesController < ApplicationController
  def new

  end

  def create
    render plain: params[:article].inspect
  end
end
```

- 폼을 제출하면 아래와 같이 나오게 될 것입니다.

```ruby
<ActionController::Parameters {"title"=>"1234", "text"=>"1234"} permitted: false>
```

- 폼에서 입력한 article의 전체 param을 만아 올 수 있으며, inspect는 디버깅용으로 클래스 이름, 인스턴스의 object id 등을 알려주는 함수입니다.

## 👨‍🎨 모델 생성

- Rails의 모델은 단수를 사용하며 DB 테이블은 복수를 사용합니다.
- 아래의 명령어로 쉽게 model을 생성할 수 있습니다.

```ruby
$ bin/rails generate model Article title:string text:text
```

- 아래와 같이 model와 db migrate가 생성됩니다.
- migrate 파일명은 `YYYYMMDDHHMMSS_create_모델명.rb` 입니다.

```ruby
class Article < ApplicationRecord
end
```

```ruby
class CreateArticles < ActiveRecord::Migration[5.2]
  def change
    create_table :articles do |t|
      t.string :title
      t.text :text

      t.timestamps
    end
  end
end
```

- 루비는 위의 migrate 파일로 쉽게 DB 테이블을 만들고 수정할 수 있습니다. 아래의 명령어로 마이그레이션을 실행해봅시다.

```ruby
$ bin/rails db:migrate
```

```ruby
==  CreateArticles: migrating ==================================================
-- create_table(:articles)
   -> 0.0019s
==  CreateArticles: migrated (0.0020s) =========================================
```

## 🏠 article create DB 저장

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(params[:article])

    @article.save

    redirect_to @article
  end
end
```

- :article param을 이용해서 Article 모델을 만들고 save 함수로 DB로 저장
- 그러나 실행하면 에러가 발생합니다.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1573c66a-db81-49e1-8a53-8a128cc8955a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1573c66a-db81-49e1-8a53-8a128cc8955a/Untitled.png)

- Rails는 몇가지 보안사항을 적용해놨는데, 지금 문제는 strong paramters라는 문제로 정확한 매개변수를 알려줘야 한다는 의미입니다.
- 따라서 아래와 같이 각 파라미터들을 모두 알려줘야 합니다.

```ruby
@article = Article.new(params.require(:article).permit(:title, :text))
```

- 위의 코드는 보안적으로 문제가 될수도 있기 때문에 외부에서 호출될 수 없도록 private으로 만들기도 합니다.

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(article_params)

    @article.save

    redirect_to @article
  end

  private
  def article_params
    params.require(:article).permit(:title, :text)
  end
end
```

- 이후에 양식을 제출하면 `show` Action을 찾지 못한다고 한다.
- 그렇다면 이제 show Action을 정의해보자.

## 🎫 Article Show

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def show
    @article = Article.find(params[:id])
  end
end
```

- find를 이용해 id를 가져오면 쉽게 모델을 찾을수 있는걸 확인할 수 있습니다.
- show view를 정의해봅시다.

- show.html.erb

```ruby
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>
 
<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>
```

- 폼을 제출하면 아래와 같이 결과가 나오는걸 확인할 수 있습니다.

![ruby_on_rails_getting_start_blog_1-4](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-4.png?raw=true)

## 결론

- RubyOnRails는 기본적으로 REST API들을 모델을 생성하면 생성해 주기 때문에 비즈니스 로직에 훨씬 더 집중 할수 있습니다.
- 또한 ORM 기능으로 DB 쿼리도 간단하게 처리할 수 있다는 장점이 있습니다.
- 개인적으로 처음 사용해보면서 에러 메시지가 바로 나온다는 점과 에러메시지를 따라 가기만 하면 된다는 것과 개발 환경에서는 서버를 내렸다 올리지 않아도 변경사항이 적용되는 점도 개발 시간을 단축해주는 것 같습니다.
- 마지막으로 controller Action과 view의 매핑이 폴더 및 파일명으로 되니, 기본 라우팅 설정들이 간단하는 걸 느끼게 되었습니다.
- 2편에서는 모든 Article을 조회하고 수정하고 삭제하고 검증하는 방법을 알아보도록 하겠습니다.

## 출처

[Getting Started with Rails - Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/getting_started.html#starting-up-the-web-server)zonaws.com/secure.notion-static.com/1573c66a-db81-49e1-8a53-8a128cc8955a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1573c66a-db81-49e1-8a53-8a128cc8955a/Untitled.png)

- Rails는 몇가지 보안사항을 적용해놨는데, 지금 문제는 strong paramters라는 문제로 정확한 매개변수를 알려줘야 한다는 의미입니다.
- 따라서 아래와 같이 각 파라미터들을 모두 알려줘야 합니다.

```ruby
@article = Article.new(params.require(:article).permit(:title, :text))
```

- 위의 코드는 보안적으로 문제가 될수도 있기 때문에 외부에서 호출될 수 없도록 private으로 만들기도 합니다.

```ruby
class ArticlesController < ApplicationController
  def create
    @article = Article.new(article_params)

    @article.save

    redirect_to @article
  end

  private
  def article_params
    params.require(:article).permit(:title, :text)
  end
end
```

- 이후에 양식을 제출하면 `show` Action을 찾지 못한다고 한다.
- 그렇다면 이제 show Action을 정의해보자.

## 🎫 Article Show

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def show
    @article = Article.find(params[:id])
  end
end
```

- find를 이용해 id를 가져오면 쉽게 모델을 찾을수 있는걸 확인할 수 있습니다.
- show view를 정의해봅시다.

- show.html.erb

```ruby
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>
 
<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>
```

- 폼을 제출하면 아래와 같이 결과가 나오는걸 확인할 수 있습니다.

![ruby_on_rails_getting_start_blog_1-5](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_1-5.png?raw=true)

## 결론

- RubyOnRails는 기본적으로 REST API들을 모델을 생성하면 생성해 주기 때문에 비즈니스 로직에 훨씬 더 집중 할수 있습니다.
- 또한 ORM 기능으로 DB 쿼리도 간단하게 처리할 수 있다는 장점이 있습니다.
- 개인적으로 처음 사용해보면서 에러 메시지가 바로 나온다는 점과 에러메시지를 따라 가기만 하면 된다는 것과 개발 환경에서는 서버를 내렸다 올리지 않아도 변경사항이 적용되는 점도 개발 시간을 단축해주는 것 같습니다.
- 마지막으로 controller Action과 view의 매핑이 폴더 및 파일명으로 되니, 기본 라우팅 설정들이 간단하는 걸 느끼게 되었습니다.
- 2편에서는 모든 Article을 조회하고 수정하고 삭제하고 검증하는 방법을 알아보도록 하겠습니다.

## 출처

[Getting Started with Rails - Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/getting_started.html#starting-up-the-web-server)