# RubyOnRails Getting Start(Blog 만들기) 2편

## 💼 서론

- [RubyOnRails Getting Started](https://guides.rubyonrails.org/v5.2/getting_started.html) 에 있는 간단한 게시판 CURD 및 댓글 기능까지 개발한 내용입니다
- Ruby version은 2.6.3을 사용합니다.
- Ruby On Rails version은 5.2.1을 사용합니다.

## 🍄 Article 전체 조회

- `bin/rails routes` 로 api를 조회하면 전체 조회 API는 아래와 같다.

```ruby
Prefix   Verb   URI Pattern         Controller#Action
articles GET    /articles(.:format) articles#index
```

- 그럼 일단 ArticlesController에 index 함수를 추가하는 작업부터 시작해보자.

```ruby
class ArticlesController < ApplicationController
  def index
    @articles = Article.all
  end
end
```

- all 함수를 이용하면 Article의 전체 목록을 가져올 수 있습니다.
- 두번째로 할일은 view를 만드는 것이죠! `index.html.erb` 를 만들어 봅시다.

```ruby
<h1>Listing articles</h1>
 
<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
    <th></th>
  </tr>
 
  <% @articles.each do |article| %>
    <tr>
      <td><%= article.title %></td>
      <td><%= article.text %></td>
      <td><%= link_to 'Show', article_path(article) %></td>
    </tr>
  <% end %>
</table>
```

![ruby_on_rails_getting_start_blog_2-1](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_2-1.png?raw=true)

- `acrticle_path` 는 헬퍼로 article에 관련 url을 알려주게 됩니다.

## 🔗 링크 추가

- 지금까지 했던 작업으로 Article 작성 및 조회를할 수 있습니다.
- 허전했던 root page를 수정해도록 하죠.
- `welcome/index.html.erb`

```ruby
<h1>Hello Rails</h1>
<%= link_to 'My Blog', controller: 'articles' %>
<%= link_to 'New article', new_article_path %>
```

- 그리고 전체 조회 view에서 새로운 글을 작성할 수 있도록 링크를 연결해보도록 하겠습니다.

```ruby
<h1>Listing articles</h1>

<%= link_to 'New article', new_article_path %>

<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
    <th></th>
  </tr>

  <% @articles.each do |article| %>
    <tr>
      <td><%= article.title %></td>
      <td><%= article.text %></td>
      <td><%= link_to 'Show', article_path(article) %></td>
    </tr>
  <% end %>
</table>
```

- 작성 중에 뒤로 돌아가는 link도 연결해보도록 하겠습니다.

```ruby
<h1>New Article</h1>

<%= form_with scope: :article, url: articles_path, local: true do |form| %>  <p>
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

<%= link_to 'Back', articles_path %>
```

- 하나의 글을 보고 있다가 전체 글을 확인할 수 있도록 링크를 연결해보도록 하겠습니다.

```ruby
<h1>New Article</h1>

<%= form_with scope: :article, url: articles_path, local: true do |form| %>  <p>
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

<%= link_to 'Back', articles_path %>
```

### Tip

- 동일한 controller를 연결할 경우에는 컨트롤러를 설정해줄 필요가 없습니다.
- 그러나 `welcome/index.html.erb` 이 파일은 다른 컨트롤러를 사용하기 떄문에 아래와 같이 설정해줘야 합니다.

```ruby
<%= link_to 'My Blog', controller: 'articles' %>
```

## 👨‍⚕️ 유효성 검사

- 간단하게 제목이 5글자 이상이여야 하고 빈값이 아니여야 할 경우 vailidation을 작성해보도록 하겠습니다.
- Article 모델에 대해 validation 이기 때문에 Article 모델 코드를 작성해줘야 합니다.
- `app/models/article.rb`

```ruby
class Article < ApplicationRecord
  validates :title, presence: true,
           length: {minimum: 5}
end
```

- 이후에 글 작성을 하면 아래와 같이 에러메시지가 발생합니다.

![ruby_on_rails_getting_start_blog_2-2](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_2-2.png?raw=true)

- 쉽게 생각하면 validate가 되었지만, 그 이후에 작업이 설정되어 있지 않다는 것입니다.
- validate가 될 경우 후속 작업을 다시 new view로 돌아가도록 해보겠습니다.
- `app/controllers/articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
	def new
    @article = Article.new
  end

  def create
    @article = Article.new(article_params)

    if @article.save
      redirect_to @article
    else
      render 'new'
    end

  end

  def article_params
    params.require(:article).permit(:title, :text)
  end
end
```

- 유효성 검사 이후에 에러가 발생한 메시지를 view에 알려줘야 합니다. 관련 로직을 작성해보도록 하겠습니다.
- `app/views/articles/new.html.erb`

```ruby
<h1>New Article</h1>

<%= form_with scope: :article, url: articles_path, local: true do |form| %>

  <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2>
        <%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:
      </h2>
      <ul>
        <% @article.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

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

<%= link_to 'Back', articles_path %>
```

- 에러 메시지가 있다면 에러 메시지를 보여주게 했으며, new 함수에 @article 변수를 선언함으로써 설정할 수 있다.

![ruby_on_rails_getting_start_blog_2-3](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_2-3.png?raw=true)

- 위와 같이 예쁘게 에러 메시지가 나오는걸 확인할 수 있다.

## 🆙 Article Update

- `bin/rails routes` 로 필요한 API를 확인해보자.

```ruby
Prefix       Verb   URI Pattern                    Controller#Action
edit_article GET    /articles/:id/edit(.:format)   articles#edit
article      PATCH  /articles/:id(.:format)        articles#update

```

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def edit
    @article = Article.find(params[:id])
  end

	def update
    @article = Article.find(params[:id])

    if @article.update(article_params)
      redirect_to @article
    else
      render 'edit'
    end
  end
  private

  def article_params
    params.require(:article).permit(:title, :text)
  end
end
```

- `app/views/articles/edit.html.erb`

```ruby
<h1>Edit article</h1>
 
<%= form_with(model: @article, local: true) do |form| %>
 
  <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2>
        <%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:
      </h2>
      <ul>
        <% @article.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
      </ul>
    </div>
  <% end %>
 
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
 
<%= link_to 'Back', articles_path %>
```

- model에 @article 을 건네주면 자동으로 값들을 채워 주게 됩니다.
- 전체 조회 페이지에서 edit도 할 수 있도록 link를 추가해줍니다.

```ruby
<h1>Edit article</h1>

<%= form_with(model: @article, local: true) do |form| %>

  <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2>
        <%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:
      </h2>
      <ul>
        <% @article.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

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

<%= link_to 'Back', articles_path %>
```

- 단일 글을 보는 show.html.erb에도 Edit를 추개해주면 끝입니다.

```ruby
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<%= link_to 'Edit', edit_article_path %>
<%= link_to 'Back', articles_path %>
```

## view 중복 제거

- 일단 edit와 new는 굉장히 비슷하게 코드가 작성되어 있습니다.
- 중복 부분만 따로 빼도록 하겠습니다.
- `app/views/articles/_form.html.erb`

```ruby
<%= form_with model: @article, local: true do |form| %>

  <% if @article.errors.any? %>
    <div id="error_explanation">
      <h2>
        <%= pluralize(@article.errors.count, "error") %> prohibited
        this article from being saved:
      </h2>
      <ul>
        <% @article.errors.full_messages.each do |msg| %>
          <li><%= msg %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

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

- form_with을 제외한 부분에서 모두 비슷한데, 여기서 공통적인 부분은 @article을 사용한다는 점이기 떄문에 model로 선언하면 Rails가 알아서 사용할 URI와 메서드를 추론합니다.
- `app/views/articles/new.html.erb`

```ruby
<h1>New Article</h1>

<%= render 'from' %>

<%= link_to 'Back', articles_path %>
```

- `app/views/articles/edit.html.erb`

```ruby
<h1>Edit article</h1>

<%= render 'from' %>

<%= link_to 'Back', articles_path %>
```

## ⚰️ Delete Article

- `bin/rails routes` 로 api를 조회하면 전체 조회 API는 아래와 같다.

```ruby
Prefix   Verb      URI Pattern                Controller#Action
articles DELECT    /articles/:id(.:format)    articles#destory
```

- `articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  def destroy
    @article = Article.find(params[:id])
    @article.destroy

    redirect_to articles_path
  end
end
```

- `app/views/articles/index.html.erb`

```ruby
<h1>Listing articles</h1>

<%= link_to 'New article', new_article_path %>

<table>
  <tr>
    <th>Title</th>
    <th>Text</th>
    <th></th>
  </tr>

  <% @articles.each do |article| %>
    <tr>
      <td><%= article.title %></td>
      <td><%= article.text %></td>
      <td><%= link_to 'Show', article_path(article) %></td>
      <td><%= link_to 'Edit', edit_article_path(article) %></td>
      <td><%= link_to 'Delete', article_path(article),method: :delete, data: { confirm: 'Are you sure?' } %></td>
    </tr>
  <% end %>
</table>
```

## 😇 결론

- 루비가 확실히 기본적인 API들을 모두 만들어 주기 떄문에 개발 생산성은 늘어나게 되는것 같다.
- 또한 Link 연결도 helper들이 모두 도와주기 때문에 궅이 url을 명시하지 않아도 쉽게 설정할 수 있다는 장점이 있기도 한거 같다.
- validate도 모델에서 쉽게 정의할수 있기 때문에 좀더 모델 친화적으로 코드를 작성할 수 있을거 같다.
- 다음편에서는 Article CRUD를 만들었으니, Commenet 기능을 개발해보도록 하겠습니다 😂

## 출처

[Getting Started with Rails - Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/getting_started.html#starting-up-the-web-server)