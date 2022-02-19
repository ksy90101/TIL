# devise_invitable를 테스트 해보자.

## 🧐 서론

- 현재 회사에서 **[devise_invitable](https://github.com/scambra/devise_invitable)**를 이용해 사용자에게 초대 이메일을 보내 가입을 시켜주고 있습니다.
- `users/invitation/accept?invitation_token=abcdef` 이러한 URL를 사용해 토큰을 제공해주고, 토큰이 유효가 된다면 토큰이 유효되었다는 안내를 보여주며, root_path로 이동시키고 있습니다.
- 이러한 플로우로 제공하다 보니, 사용자들이 다시 해당 서비스를 들어오려고 메일을 클릭하는 경우가 생기며, `유효하지 않는 토큰입니다.` 라는 메시지와 같이 본인이 원하는 페이지를 보지 못하는 경우가 생겼습니다.
- 사용자가 원하는 페이지는 무조건 로그인을 해야 볼 수 있는 페이지인데, 로그인을 하지 않고 root_path로 가니, 원하지 않는 페이지가 나오는것이였습니다.
- 따라서 유혀하지 않은 토큰이면서, 로그인을 하지 않은 상태일때는 바로 로그인 페이지로 이동시키는 플로우를 추가하였습니다.
- 이러한 플로우를 추가하면서 테스트 코드를 같이 추가했는데, 몇가지 트러블 슈팅이 있어서 그걸 공유해보려고 합니다.

## 🔎 invitations_controller

```ruby
class Devise::InvitationsController < DeviseController
  prepend_before_action :authenticate_inviter!, only: [:new, :create]
  prepend_before_action :has_invitations_left?, only: [:create]
  prepend_before_action :require_no_authentication, only: [:edit, :update, :destroy]
  prepend_before_action :resource_from_invitation_token, only: [:edit, :destroy]

  if respond_to? :helper_method
    helper_method :after_sign_in_path_for
  end

  # GET /resource/invitation/new
  def new
    self.resource = resource_class.new
    render :new
  end

  # POST /resource/invitation
  def create
    self.resource = invite_resource
    resource_invited = resource.errors.empty?

    yield resource if block_given?

    if resource_invited
      if is_flashing_format? && self.resource.invitation_sent_at
        set_flash_message :notice, :send_instructions, email: self.resource.email
      end
      if self.method(:after_invite_path_for).arity == 1
        respond_with resource, location: after_invite_path_for(current_inviter)
      else
        respond_with resource, location: after_invite_path_for(current_inviter, resource)
      end
    else
      respond_with_navigational(resource) { render :new }
    end
  end

  # GET /resource/invitation/accept?invitation_token=abcdef
  def edit
    set_minimum_password_length
    resource.invitation_token = params[:invitation_token]
    render :edit
  end

  # PUT /resource/invitation
  def update
    raw_invitation_token = update_resource_params[:invitation_token]
    self.resource = accept_resource
    invitation_accepted = resource.errors.empty?

    yield resource if block_given?

    if invitation_accepted
      if resource.class.allow_insecure_sign_in_after_accept
        flash_message = resource.active_for_authentication? ? :updated : :updated_not_active
        set_flash_message :notice, flash_message if is_flashing_format?
        resource.after_database_authentication
        sign_in(resource_name, resource)
        respond_with resource, location: after_accept_path_for(resource)
      else
        set_flash_message :notice, :updated_not_active if is_flashing_format?
        respond_with resource, location: new_session_path(resource_name)
      end
    else
      resource.invitation_token = raw_invitation_token
      respond_with_navigational(resource) { render :edit }
    end
  end

  # GET /resource/invitation/remove?invitation_token=abcdef
  def destroy
    resource.destroy
    set_flash_message :notice, :invitation_removed if is_flashing_format?
    redirect_to after_sign_out_path_for(resource_name)
  end

  protected

    def invite_resource(&block)
      resource_class.invite!(invite_params, current_inviter, &block)
    end

    def accept_resource
      resource_class.accept_invitation!(update_resource_params)
    end

    def current_inviter
      authenticate_inviter!
    end

    def has_invitations_left?
      unless current_inviter.nil? || current_inviter.has_invitations_left?
        self.resource = resource_class.new
        set_flash_message :alert, :no_invitations_remaining if is_flashing_format?
        respond_with_navigational(resource) { render :new }
      end
    end

    def resource_from_invitation_token
      unless params[:invitation_token] && self.resource = resource_class.find_by_invitation_token(params[:invitation_token], true)
        set_flash_message(:alert, :invitation_token_invalid) if is_flashing_format?
        redirect_to after_sign_out_path_for(resource_name)
      end
    end

    def invite_params
      devise_parameter_sanitizer.sanitize(:invite)
    end

    def update_resource_params
      devise_parameter_sanitizer.sanitize(:accept_invitation)
    end

    def translation_scope
      'devise.invitations'
    end
end
```

Devise::InvitationsController를 살펴보면 우리가 사용하는 action은 아래와 같습니다.

```ruby
# GET /resource/invitation/accept?invitation_token=abcdef

def edit
  set_minimum_password_length
  resource.invitation_token = params[:invitation_token]
  render :edit
end
```

이 부분을 아무리 살펴봐도 토큰을 검증하는 부분은 보이지 않습니다.

그렇다면 어디서 검증을 할까요?

```ruby
prepend_before_action :resource_from_invitation_token, only: [:edit, :destroy]

def resource_from_invitation_token
  unless params[:invitation_token] && self.resource = resource_class.find_by_invitation_token(params[:invitation_token], true)
    set_flash_message(:alert, :invitation_token_invalid) if is_flashing_format?
    redirect_to after_sign_out_path_for(resource_name)
  end
end
```

위의 코드를 보면, 모든 action이 실행되기 전에 callback으로 `resource_from_invitation_token` 이 메서드가 실행되는걸 확인할 수 있습니다.

그렇다면 유효하지 않은 경우에는 `after_sign_out_path_for` 이곳으로 redirect를 하고 있는걸 확인할 수 있습니다 🙂

```ruby
module Devise
  module Controllers
		...

		def after_sign_out_path_for(resource_or_scope)
		  scope = Devise::Mapping.find_scope!(resource_or_scope)
		  router_name = Devise.mappings[scope].router_name
		  context = router_name ? send(router_name) : self
		  context.respond_to?(:root_path) ? context.root_path : "/"
		end

		...
	end
end
```

해당 메서드의 코드를 보면, 간단하게 말씀드리면 root_path로 이동시키는걸 확인할 수 있습니다.

그래서 `해당 메서드를 override 하면 되겠구나.` 라는 생각을 가지게 되었습니다.

그렇다면 대체 어떻게 로그인이 되어있는지 검증을 할 수 잇을까? 라는 두번째 생각이 들었습니다.

```ruby
#     user_signed_in?     # Checks whether there is a user signed in or not
```

해당 Devise 모듈을 살펴보면 위와 같은 내용이 작성되어 있고, 해석해보면 유저가 singed를 했는지 안했는지를 체크하는 것이라고 되어 잇습니다.

참고로 해당 메서드는 아래와 같이 작성되어 있으며, 해당 mapping된게 있는지 확인하는 메서드였습니다.

```ruby
def #{mapping}_signed_in?
	!!current_#{mapping}
end
```

그렇다면 준비는 모두 끝났습니다. 한번 override 코드를 살펴보시죠!

## 😆`after_sign_out_path_for` override를 해보자

```ruby
def after_sign_out_path_for(resource_or_scope)
  if !user_signed_in?
    user_login_path
  else
    super(resource_or_scope)
  end
end
```

`Devise::InvitationsController` 를 상속하고 있는 Controller에 아래와 같이 override를 하였습니다.

로그인이 되어 있지 않다면 user_login_path(로그인 페이지)를 return하고 그 외적인 경우에는 라이브러리가 구현한 방식인 root_path로 가도록 하였습니다.

## 🎯 로그인 페이지로 잘 이동되는지 테스트를 코드를 작성해보자.

```ruby
RSpec.describe Users::InvitationsController, type: :controller do
  let_it_be(:user) { FactoryBot.create(:user, email: 'marco@gmail.com') }

  before do
    request.env['devise.mapping'] = Devise.mappings[:user]
  end

  describe '#edit' do
    context '로그인을 하지 않은 경우' do
      it '초대 token이 유효하지 않을때 접근하는 경우 로그인 페이지로 이동한다.' do
        get :edit, params: { invitation_token: 'abcdef' }

        expect(response).to have_http_status(:redirect)
        expect(response).to redirect_to user_login_path
      end
    end

    context '로그인을 한 경우' do
      before { sign_in user }

      it '초대 token이 유효하지 않을때 접근하는 경우 루트 페이지로 이동한다.' do
        get :edit, params: { invitation_token: 'abcdef' }

        expect(response).to have_http_status(:redirect)
        expect(response).to redirect_to root_path
      end
    end
  end
end
```

위에 테스트 코드를 살펴보면 충분히 이해가 갈것이라고 생각이 듭니다.

위에서 우리가 코드를 살펴볼때 redirect가 된다고 하였고 그에 맞춰 redirect가 어디로 가는지 확인하는 코드를 작성하였습니다.

테스트 코드를 작성했을때 어려웟던 부분은 before의 부분이였고, 해당 부분이 없으면 테스트가 실행되지 않습니다. 구글링을 한 결과 devise gem에서 아래와 같은 설명이 되어 있습니다.

> If you are testing Devise internal controllers or a controller that inherits from Devise's, you need to tell Devise which mapping should be used before a request. This is necessary because Devise gets this information from the router, but since controller tests do not pass through the router, it needs to be stated explicitly. For example, if you are testing the user scope, simply use:
> 

```ruby
test 'GET new' do
  # Mimic the router behavior of setting the Devise scope through the env.
  @request.env['devise.mapping'] = Devise.mappings[:user]

  # Use the sign_in helper to sign in a fixture `User` record.
  sign_in users(:alice)

  get :new

  # assert something
end
```

Devise 내부 컨트롤러를 테스트 하는 경우에는 요청 전에 어떤 매핑을 사용할지를 Devise에 꼭 알려야 한다고 합니다. Devise가 라우터에서 이 정보를 가져오는데, 테스트를 할때는 라우터를 통과하지 않기 때문에 명시적으로 해줘야 합니다.트하는 경우 다음을 사용하면 됩니다.

즉, user를 사용할지 member를 사용할지 등등 어떤걸 mapping할지를 꼭 정해야 한다는 의미입니다.

## 결론

라이브러리를 어떻게 분석해야 하는지 잘 알게된 케이스가 된거 같습니다.

아울러 테스트 코드에서는 라우터를 타지 않는다는 점과 redirect에 대한 테스트 코드 작성 법을 배운거 같습니다.

좀 더 라이브러리를 잘 읽을 수 있는 개발자가 되고 싶네요 😂
