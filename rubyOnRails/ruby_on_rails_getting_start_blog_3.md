# RubyOnRails Getting Start(Blog 만들기) 3편

## 💼 서론

- [RubyOnRails Getting Started](https://guides.rubyonrails.org/v5.2/getting_started.html) 에 있는 간단한 게시판 CURD 및 댓글 기능까지 개발한 내용입니다
- Ruby version은 2.6.3을 사용합니다.
- Ruby On Rails version은 5.2.1을 사용합니다.

## Comment 모델 추가

- 모델 추가 명령어

```ruby
$ bin/rails generate model Comment commenter:string body:text article:references
```

- `db / migrate / 20140120201010_create_comments.rb`

```ruby
class CreateComments < ActiveRecord::Migration[5.2]
  def change
    create_table :comments do |t|
      t.string :commenter
      t.text :body
      t.references :article, foreign_key: true

      t.timestamps
    end
  end
end
```

- `app/models/comment.rb`

```ruby
class Comment < ApplicationRecord
  belongs_to :article
end
```

- 일단 belongs_to를 이용해서 연관관계를 매핑한것이라고 이해하면 좋습니다.

```ruby
bin/rails db:migrate
```

- 위 명령어를 통해 DB Migrate를 진행한다.

## 🔗  모델 연결

- Comment는 하나의 Article에 속하고 하나의 Article은 여러개의 Comment로 연결되어 있습니다.
- 따라서 Article 모델에 연관관계 매핑을 진행해줘야 합니다.

```ruby
class Article < ApplicationRecord
  has_many :comments
  validates :title, presence: true,
           length: {minimum: 5}
end
```

## ➕ 경로 추가

- Rails가 article와 Comments의 관계를 알도록 아래와 같이 수정해줍니다.

```ruby
Rails.application.routes.draw do
  get 'welcome/index'

  resources :articles do
    resources :comments
  end

  root 'welcome#index'
end
```

## Controller 생성

```ruby
$ bin/rails generate controller Comments
```

- `bin/rails routes` 로 API 확인해보자.

```ruby
Prefix Verb   URI Pattern                                                                              Controller#Action
         article_comments GET    /articles/:article_id/comments(.:format)                                                 comments#index
                          POST   /articles/:article_id/comments(.:format)                                                 comments#create
      new_article_comment GET    /articles/:article_id/comments/new(.:format)                                             comments#new
     edit_article_comment GET    /articles/:article_id/comments/:id/edit(.:format)                                        comments#edit
          article_comment GET    /articles/:article_id/comments/:id(.:format)                                             comments#show
                          PATCH  /articles/:article_id/comments/:id(.:format)                                             comments#update
                          PUT    /articles/:article_id/comments/:id(.:format)                                             comments#update
                          DELETE /articles/:article_id/comments/:id(.:format)                                             comments#destroy
                 articles GET    /articles(.:format)                                                                      articles#index
                          POST   /articles(.:format)                                                                      articles#create
              new_article GET    /articles/new(.:format)                                                                  articles#new
             edit_article GET    /articles/:id/edit(.:format)                                                             articles#edit
                  article GET    /articles/:id(.:format)                                                                  articles#show
                          PATCH  /articles/:id(.:format)                                                                  articles#update
                          PUT    /articles/:id(.:format)                                                                  articles#update
                          DELETE /articles/:id(.:format)                                                                  articles#destroy
```

## 댓글 작성 기능

- app/views/articles/show.html.erb

```ruby
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<h2>Comments</h2>
<% @article.comments.each do |comment| %>
  <p>
    <strong>Commenter:</strong>
    <%= comment.commenter %>
  </p>

  <p>
    <strong>Comment:</strong>
    <%= comment.body %>
  </p>
<% end %>

<h2>Add a comment:</h2>
<%= form_with(model: [@article, @article.comments.build], local: true) do |form| %>
  <p>
    <%= form.label :commenter %><br>
    <%= form.text_field :commenter %>
  </p>
  <p>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
  </p>
  <p>
    <%= form.submit %>
  </p>
<% end %>

<%= link_to 'Edit', edit_article_path %>
<%= link_to 'Back', articles_path %>
```

- `app/controllers/comments_controller.rb`

```ruby
class CommentsController < ApplicationController
  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)

    redirect_to article_path(@article)
  end

  private
  def comment_params
    params.require(:comment).permit(:commenter, :body)
  end
end
```

- 연관관계로 매핑되어 있기 때문에 위와 같이 create를 할때 Article을 찾아서 comment를 create 해줘야 합니다.

![ruby_on_rails_getting_start_blog_3-1](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_3-1.png?raw=true)

## comment 리팩토링

- 프론트에서 컴포넌트를 분리하는것 처럼 분리를 진행해보도록 하겠습니다.
- 코멘트가 보여지는 부분과 입력하는 form을 분리해보도록 하겠습니다.
- `app/views/comments/_comment.html.erb`

```ruby
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>

<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>
```

- `app/views/comments/_form.html.erb`

```ruby
<%= form_with(model: [ @article, @article.comments.build ], local: true) do |form| %>
  <p>
    <%= form.label :commenter %><br>
    <%= form.text_field :commenter %>
  </p>
  <p>
    <%= form.label :body %><br>
    <%= form.text_area :body %>
  </p>
  <p>
    <%= form.submit %>
  </p>
<% end %>
```

- `app/views/articles/show.html.erb`

```ruby
<p>
  <strong>Title:</strong>
  <%= @article.title %>
</p>

<p>
  <strong>Text:</strong>
  <%= @article.text %>
</p>

<h2>Comments</h2>
<%= render @article.comments %>

<h2>Add a comment:</h2>
<%= render 'comments/form' %>

<%= link_to 'Edit', edit_article_path(@article) %> |
<%= link_to 'Back', articles_path %>
```

## ⚰️ 코멘트 삭제 기능

- `bin/rails routes` 로 delete 기능 api를 확인합니다.

```ruby
Prefix          Verb   URI Pattern                                  Controller#Action
article_comment DELETE /articles/:article_id/comments/:id(.:format) comments#destroy
```

- controller 로직과 view 로직을 작성해보겠습니다.
- `app/views/comments/_comment.html.erb`

```ruby
<p>
  <strong>Commenter:</strong>
  <%= comment.commenter %>
</p>

<p>
  <strong>Comment:</strong>
  <%= comment.body %>
</p>

<p>
  <%= link_to '삭제', [comment.article, comment],
              method: :delete,
              data: { confirm: '삭제하시겠습니까?' } %>
</p>
```

- `app/controllers/comments_controller.rb`

```ruby
class CommentsController < ApplicationController
  def destroy
    @article = Article.find(params[:article_id])
    @comment = @article.comments.find(params[:id])
    @comment.destroy

    redirect_to article_path(@article)
  end
end
```

## 📦 Article 삭제 시 Comment도 같이 삭제

- dependent 옵션을 이용해 손쉽게 삭제할 수 있습니다.
- `app/models/article.rb`

```ruby
class Article < ApplicationRecord
  has_many :comments, dependent: :destroy
  validates :title, presence: true,
           length: {minimum: 5}
end
```

## 😇 결론

- 연관관계도 손쉽게 할수 있으며, CASECAD도 dependent 하나로 설정할 수 있다는 장점이 돋보이기도 했다.
- 또한 좀더 직관적으로 article안에 comment라는 의미로 article.comment로 직접 조회, 삭제를 할 수 있다는 장점이 있는것 같다.
- 마지막 4편은 보안관련해서 간단하게 이야기 해볼려고 합니다

## 출처

[Getting Started with Rails - Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/getting_started.html#starting-up-the-web-server)