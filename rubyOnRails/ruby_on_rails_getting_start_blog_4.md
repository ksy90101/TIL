# RubyOnRails Getting Start(Blog 만들기) 4편(마지막👨‍🎨)

## 💼 서론

- [RubyOnRails Getting Started](https://guides.rubyonrails.org/v5.2/getting_started.html) 에 있는 간단한 게시판 CURD 및 댓글 기능까지 개발한 내용입니다
- Ruby version은 2.6.3을 사용합니다.
- Ruby On Rails version은 5.2.1을 사용합니다.

## 보안

- 현재는 누구나 삭제할수 있고 글을 작성할 수 있고 글을 수정할 수 있습니다.
- 이러한 접근을 간단하게 차단을 해 보안 관련 설정을 진행해보도록 하겠습니다.
- `app/controllers/articles_controller.rb`

```ruby
class ArticlesController < ApplicationController
  http_basic_authenticate_with name: "rutgo", password: "1234", except: [:index, :show]

  def new
    @article = Article.new
  end
	
	...
end
```

- `app/controllers/comments_controller.rb`

```ruby
class CommentsController < ApplicationController
  http_basic_authenticate_with name: "rutgo", password: "1234", only: :destroy

  def create
    @article = Article.find(params[:article_id])
    @comment = @article.comments.create(comment_params)

    redirect_to article_path(@article)
  end
	...
end
```

- 두개의 설정은 인증된 사용자만 글을 추가, 수정, 삭제할 수 있으며, 댓글은 인증된 사용자만 삭제할 수 있도록 설정했습니다.
- 아주 간단하게 인증 인가를 설정한 것으로 기본적인 Rails의 보안 방법을 보는것 뿐이라고 생각하면 좋습니다.
- 저렇게 설정한 이후 인증 인가가 필요한 기능을 실행하면 아래와 같이 인증창이 나오게 됩니다.

![ruby_on_rails_getting_start_blog_4-1](https://github.com/ksy90101/TIL/blob/master/rubyOnRails/image/ruby_on_rails_getting_start_blog_4-1.png?raw=true)

- 아주 간단한 설정 방법으로 잘 사용은 되지 않습니다.

## 😇 결론

- 아주 간단하게 Getting started를 진행해봤는데, 개인적으로 개발자가 비지니스 로직에 집중할 수 있도록 최대한 많은 기능들을 지원해주는걸 느낄수 있었습니다.
- 또한 많은 helper들로 손쉽게 쿼리 기능이나 url 기능들을 처리할 수 있었습니다.
- 지금까지 Ruby On Rails가 생산성이 높다는 이야기를 많이 들엇는데, 직접 사용해보니 그런 이유를 알 수 있었습니다 😂
- 최근 회사에서 ruby On Rails를 사용하게 되어서 꾸준히 공부해야 되는데, 일단 Guide를 돌파 하는 것으로 목표를 잡아서 진행해봐야 겠네요!

## 출처

[Getting Started with Rails - Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/getting_started.html#starting-up-the-web-server)