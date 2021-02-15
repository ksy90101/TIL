# rails new File & Folder

# 서론

- ruby on rails는 `rails new {프로젝트명}` 을 통해 프로젝트가 만들어 진다.
- `rails new` 명령어를 이용해 프로젝트를 만들면 기본 파일과 폴더 세팅을 해주게 된다.

# Default File & Folder

## app

- cotnroller, model, view, helper, mailer, channel, job, asset 등이 포함되어 있는 폴더
- 개발 시 가장 중요한 폴더
- `concerns` 폴더는 공통 모듈

### Controllers

- `application_controller.rb` 는 애플리케이션 공통 컨트롤러

### assets

- JS, CSS, Image 등 파일

### layouts

- `application.html.erb` 는 애플리케이션 공통 레이아웃

### helpers

- `application_helper.rb` 는 애플리케이션 공통 뷰 헬퍼

## bin

- 코드 생성 또는 개발 서버 실행에 사용되는 헬퍼 스크립트

## config

- 애플리케이션의 경로, 데이터베이스 등을 구성하는 공간
- 초기화, 국제화, 환경 단위 설정, 라우팅 등을 설정한다.

## config.ru

- 애플리케이션을 시작하는 데 사용되는 Rack 서버 구성
- 애플리케이션 엔트리 포인트

## db

- 데이터베이스 스크마와 데이터베이스 마이그레이션을 포함

## Gemfile & Gemfile.lock

- 필요한 gem 파일 설정
- Bundler gem에서 사용합니다.

## lib

- 사용자 정의 라이브러리

## log

- 로그 출력 위치

## package.json

- node module 정보

## public

- 공개 폴더

## Rakefile

- 터미널로부터 사용 가능한 작업

## README.md

- 애플리케이션 간략한 사용 설명서

## test

- 테스트 스크립트 등

## tmp

- 임시 파일
- 캐시, pid ....

## vendor

- third-party 코드
- 전형적인 Rails 애플리케이션 gems vendor가 포함되어 있다.

## .gitignore

- git ignore 목록

## .ruby-version

- ruby의 기본 버전이 작성되어 있디.

# 결론

- 폴더와 파일이 많이 보이지만, 기본적으로 모든 종류의 폴더와 파일을 만들어 주기 때문에 초기 세팅이 편리하다고 생각합니다.

# 출처

[Ruby on Rails Guides](https://guides.rubyonrails.org/v5.2/)