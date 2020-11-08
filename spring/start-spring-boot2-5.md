# 5장. 스프링 부트 시큐리티 + OAuth2

> [처음배우는 스프링 부트 2](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=168752840)  
> [예제코드](https://github.com/ksy90101/book-spring-boot-start)

## 5.1. 배경지식 소개

-   스프링 부트 시큐리티는 인증과 인가에 관련된 강력한 기능을 제공하며, 스프링 시큐리티의 번거로운 설정을 간소화시켜주는 프레임워크이다.
-   1.5버전에서 지원하는 스프링 시큐리티, OAuth2 API를 사용해 소셜 미디어 인증을 빠르고 쉽게 적용이 가능하며, 2.0 부터는 스프링 시큐리티 내부에 OAuth2 API가 포함되는 등 구조가 많이 변경되어 더 쉽게 OAuth2를 구현할 수 있다.
-   인증과 인가

[ksy90101/TIL](https://github.com/ksy90101/TIL/blob/master/web/authentication-vs-authorization.md)

-   OAuth2 종류

[ksy90101/TIL](https://github.com/ksy90101/TIL/blob/master/web/oauth2-authentication-kinds.md)

## 5.2. 스프링 부트 시큐리티 + OAuth2 설계하기

1.  사용자가 애플리케이션에 접속하면 해당 사용자에 대한 이전 로그인 정보(세션)의 유무를 체크
2.  세션이 있으면 그대로 세션을 사용, 없으면 OAuth2 인증 과정을 거침
3.  이메일을 키값으로 사용하여 이미 가입된 사용자인지 체크. 이미 가입된 사용자라면 등록된 정보를 반환하여 요청한 URL로의 접근을 허용, 아니라면 새롭게 User 정보 저장하는 과정을 진행
4.  각 소셜 미디어에서 제공하는 User 정보가 다르기 때문에 소셜 미디어에 따라 User 객체를 생성한 후 DB에 저장
5.  세션이 있거나 4번까지 성공한 사용자는 요청한 URL로의 접근을 하용

## 5.2.1. 도메인 생성

-   SocialType : 소셜 미디어의 정보를 나타냄

```java
public enum SocialType {
    FACEBOOK("facebook"),
    GOOGLE("google"),
    KAKAO("kakao");

    private final static String ROLE_PREFIX = "ROLE_";
    private final String name;

    SocialType(final String name) {
        this.name = name;
    }

    public String getRoleType() {
        return ROLE_PREFIX + name.toUpperCase();
    }

    public String getName() {
        return name;
    }

    public boolean isEquals(final String authority) {
        return this.getRoleType().equals(authority);
    }
}
```

-   User

```java
import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import spring.boot.security.ex.domain.enums.SocialType;

@Entity
@NoArgsConstructor
@Getter
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String name;

    @Column
    private String email;

    @Column
    private String principal;

    @Column
    @Enumerated(EnumType.STRING)
    private SocialType socialType;

    @Column
    private LocalDateTime createdDate;

    @Column
    private LocalDateTime updatedDate;

    @Builder
    public User(
            final String name, final String email, final String principal, final SocialType socialType,
            final LocalDateTime createdDate,
            final LocalDateTime updatedDate) {
        this.name = name;
        this.email = email;
        this.principal = principal;
        this.socialType = socialType;
        this.createdDate = createdDate;
        this.updatedDate = updatedDate;
    }
}
```

## 5.3. 스프링 부트 시큐리티 + OAuth2 의존성 설정하기

```java
plugins {
    id 'org.springframework.boot' version '1.5.14.RELEASE'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
}

dependencies {
    implementation 'org.springframework.security.oauth:spring-security-oauth2'
}
```

-   1.5 버전과 2.0 버전에서의 차이가 있기때문에 먼저 1.5버전에서 작업을 하고 2.0으로 마이그레이션을 해보도록 하겠습니다.

## 5.4. 스프링 부트 시큐리티 + OAuth2 구현하기

-   구현 절차
    1.  SNS 프로퍼티 설정 및 바인딩
    2.  시큐리티 + OAuth2 설정하기
    3.  어노테이션 기반으로 User 정보 불러오기
    4.  인증 동작 확인하기
    5.  페이지 권한 분리하기

### 5.4.1. SNS 프로퍼티 설정 및 바인딩

-   clintId : OAuth 클라이언트 사용자명. OAuth 공급자가 클라이언트를 식별하는 데 사용
    
-   clintSecret : OAuth 클라이언트 시크릿 키값
    
-   accessTokenUri : 엑세스 토큰을 제공하는 OAuth의 URI
    
-   userAuthorizationUri : 사용자가 리소스에 접근하는 걸 승인하는 경우 리다이렉션할 URI. 소셜 미디어에 따라 필요 없는 경우도 있다.
    
-   scope : 리소스에 대한 접근 범위를 지정하는 문자열.
    
-   userInfoUri : 사용자의 개인정보를 조회를 위한 URI
    
-   application.yml
    

```yaml
facebook:
  client:
    clientId: clientIdTest
    clientSecret: clientSecretTest
    accessTokenUri: https://graph.facebook.com/oauth/access_token
    userAuthorizationUri: https://www.facebook.com/dialog/oauth?display=popup
    tokenName: oauth_token
    authenticationScheme: query
    clientAuthenticationScheme: form
    scope: email
  resource:
    userInfoUri: https://graph.facebook.com/me?fields=id,name,email,link

google:
  client:
    clientId: clientIdTest
    clientSecret: clientSecretTest
    accessTokenUri: https://accounts.google.com/o/oauth2/token
    userAuthorizationUri: https://accounts.google.com/o/oauth2/auth
    scope: email, profile
  resource:
    userInfoUri: https://www.googleapis.com/oauth2/v2/userInfo

kakao:
  client:
    clientId: clientIdTest
    accessTokenUri: https://kauth.kakao.com/oauth2/token
    userAuthorizationUri: https://kauth.kakao.com/oauth2/authorize
  resource:
    userInfoUri: https://kapi.kakako.com/v1/user/me
```

-   ClientResources
    -   소셜 미디어 리소스 프로퍼티를 객체로 매핑

```java
import org.springframework.boot.autoconfigure.security.oauth2.resource.ResourceServerProperties;
import org.springframework.boot.context.properties.NestedConfigurationProperty;
import org.springframework.security.oauth2.client.token.grant.code.AuthorizationCodeResourceDetails;

public class ClientResources {
    @NestedConfigurationProperty
    private final AuthorizationCodeResourceDetails client = new AuthorizationCodeResourceDetails();

    @NestedConfigurationProperty
    private final ResourceServerProperties resource = new ResourceServerProperties();

    public AuthorizationCodeResourceDetails getClient() {
        return client;
    }

    public ResourceServerProperties getResource() {
        return resource;
    }
}
```

-   @NestedConfigurationProperty
    -   해당 필드가 단일값이 아닌 중복으로 바인딩되는 것을 표시함
    -   `application.yml` 파일을 확인을 하면 `client` key값이 여러개인걸 확인할 수 있다. 중복으로 바인딩 되는걸 표시한다는 의미이다.
-   AuthorizationCodeResourceDetails
    -   프로퍼티값 중 client를 기준으로 하위 key-value를 매핑
-   ResourceServerProperties
    -   OAuth2 리소스값을 매핑하는데 사용하지만, userInfoUri 값을 받는데 사용

### 5.4.2. 시큐리티 + OAuth2 설정하기

-   SecurityConfig
    -   시큐리니 인증, 인가 부분 설정
    -   각 소셜 미디어 리소스 정보를 빈으로 등록

```java

import java.util.ArrayList;
import java.util.List;

import javax.servlet.Filter;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.oauth2.client.OAuth2ClientContext;
import org.springframework.security.oauth2.client.OAuth2RestTemplate;
import org.springframework.security.oauth2.client.filter.OAuth2ClientAuthenticationProcessingFilter;
import org.springframework.security.oauth2.client.filter.OAuth2ClientContextFilter;
import org.springframework.security.oauth2.config.annotation.web.configuration.EnableOAuth2Client;
import org.springframework.security.web.authentication.LoginUrlAuthenticationEntryPoint;
import org.springframework.security.web.authentication.www.BasicAuthenticationFilter;
import org.springframework.security.web.csrf.CsrfFilter;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.filter.CompositeFilter;

import lombok.RequiredArgsConstructor;
import spring.boot.security.ex.domain.enums.SocialType;

@RequiredArgsConstructor
@Configuration
@EnableWebSecurity // 시큐리티 기능 사용
@EnableOAuth2Client // OAuth2 기능 사
public class SecurityConfig extends WebSecurityConfigurerAdapter { // 요청, 권한, 기타 설정에 대해서 필수적으로 최적화한 설정을 위해 상속

    // OAuth2 AccessToken이 들어있는 곳
    private final OAuth2ClientContext oAuth2ClientContext;

    //@formatter:off
    @Override
    protected void configure(final HttpSecurity http) throws Exception { // 요청, 권한, 기타 설정에 대한 필수적 설정
        // 문자 인코딩 설정
        final CharacterEncodingFilter filter = new CharacterEncodingFilter();

        http
                .authorizeRequests()
                // 요청을 패턴 형식으로 정해 누구나 접근할 수 있도록 설정
                .antMatchers("/", "/login/**", "/css/**", "/images/**", "/js/**", "/console/**").permitAll()
                // 위에서 설정한 패턴을 제외한 나머지는 인증된 사용자만 사용 가능
                .anyRequest().authenticated()
            .and()
                // 응답 헤더
                .headers()
                // XFrameOptionsHeaderWriter의 최적화 설정을 허용하지 않음.
                .frameOptions().disable()
            .and()
                .exceptionHandling()
                // 인증되지 않은 사용자가 허용되지 않은 리소스를 접근할 경우 "/login" 경로로 이동
                .authenticationEntryPoint(new LoginUrlAuthenticationEntryPoint("/login"))
            .and()
                // 로그인 폼
                .formLogin()
                // 성공시 해당 Url로 이동
                .successForwardUrl("/board/list")
            .and()
                // 로그아웃 설정
                .logout()
                // 로그아웃 Url
                .logoutUrl("/logout")
                // 로그아웃 성공 Url
                .logoutSuccessUrl("/")
                // 로그아웃 시 해당 쿠기 삭제
                .deleteCookies("JSESSIONID")
                // 설정된 섹션의 무효화
                .invalidateHttpSession(true)
            .and()
                // 첫번째 인자보다 먼저 시작될 필터
                .addFilterBefore(filter, CsrfFilter.class)
                .addFilterBefore(oauth2Filter(), BasicAuthenticationFilter.class)
                // csrf 사용 하지 않는다는 의미
                .csrf().disable();
    }
    //@formatter:on

    // OAuth21ClientContextFilter를 불러와서 올바른 순서로 필터가 동작하도록 설정한다.
    // 스프링 시큐리티 필터가 실행되기 전에 충분히 낮은 순서로 필터를 등록해야 한다.
    @Bean
    public FilterRegistrationBean oauth2ClientFilterRegistration(final OAuth2ClientContextFilter filter) {
        final FilterRegistrationBean registrationBean = new FilterRegistrationBean();

        registrationBean.setFilter(filter);
        registrationBean.setOrder(-100);

        return registrationBean;
    }

    // 각 소셜 미디어 타입을 받아 필터를 설정
    // 각 소셜 미디어 필터를 리스트 형식으로 한꺼전에 설정
    private Filter oauth2Filter() {
        final CompositeFilter filter = new CompositeFilter();
        final List<Filter> filters = new ArrayList<>();

        filters.add(oauth2Filter(facebook(), "/login/facebook", SocialType.FACEBOOK));
        filters.add(oauth2Filter(google(), "/login/google", SocialType.GOOGLE));
        filters.add(oauth2Filter(kakao(), "/login/kakao", SocialType.KAKAO));
        filter.setFilters(filters);

        return filter;
    }

    private Filter oauth2Filter(final ClientResources client, final String path, final SocialType socialType) {
        // 인증이 수행 될 경로를 넣어 OAuth2 클라이언트 인증 처리 필터를 생성
        final OAuth2ClientAuthenticationProcessingFilter filter = new OAuth2ClientAuthenticationProcessingFilter(path);
        // 권한 서버와의 통신을 위해 생성하며, client 프로퍼티와 OAuth2ClientContext가 필요.
        final OAuth2RestTemplate template = new OAuth2RestTemplate(client.getClient(), oAuth2ClientContext);

        filter.setRestTemplate(template);
        // User의 권한을 최적화해서 생성하고자 UserInfoTokenService를 상속받은 UserTokenService를 생성하여 OAuth2 AccessToken 검증을 위해 생성한 UserTokenService를 필터의 토큰 서비스 등록
        filter.setTokenServices(new UserTokenService(client, socialType));
        // 성공하면 리다이렉트 URL
        filter.setAuthenticationSuccessHandler(((request, response, authentication) -> response.sendRedirect(
                "/" + socialType.getName() + "/complete")));
        // 실패하면 리다이렉트 URL
        filter.setAuthenticationFailureHandler(((request, response, exception) -> response.sendRedirect("/error")));

        return filter;
    }

    @Bean
    @ConfigurationProperties("facebook")
    public ClientResources facebook() {
        return new ClientResources();
    }

    @Bean
    @ConfigurationProperties("google")
    public ClientResources google() {
        return new ClientResources();
    }

    @Bean
    @ConfigurationProperties("kakao")
    public ClientResources kakao() {
        return new ClientResources();
    }
}
```

-   UserTokenService
    -   User정보를 비동기 통신으로 가져오는 REST Service인 UserInfoTokenServices를 커스텀
    -   소셜 미디어 원격 서버와 통신하는 로직은 이미 구현되어 있어 통신에 필요한 값만 넣어주어 설정하면 됨

```java
package spring.boot.security.ex.security;

import java.util.List;
import java.util.Map;

import org.springframework.boot.autoconfigure.security.oauth2.resource.AuthoritiesExtractor;
import org.springframework.boot.autoconfigure.security.oauth2.resource.UserInfoTokenServices;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.AuthorityUtils;

import spring.boot.security.ex.domain.enums.SocialType;

// User 정보를 얻기 위해 소셜 서버와 통신하는 역할
public class UserTokenService extends UserInfoTokenServices {

    public UserTokenService(final ClientResources resources, final SocialType socialType) {
        // 통신을 위해 clientId, URI가 필요하다.
        super(resources.getResource().getUserInfoUri(), resources.getClient().getClientId());
        setAuthoritiesExtractor(new OAuth2AuthoritiesExtractor(socialType));
    }

    // 인증된 사용자의 인가에 대한 권한을 설정하는 곳이다.
    public static class OAuth2AuthoritiesExtractor implements AuthoritiesExtractor {

        private final String sociType;

        public OAuth2AuthoritiesExtractor(final SocialType socialType) {
            this.sociType = socialType.getRoleType();
        }

        @Override
        public List<GrantedAuthority> extractAuthorities(final Map<String, Object> map) {
            return AuthorityUtils.createAuthorityList(this.sociType);
        }
    }
}
```

### 5.4.3. 어노테이션 기반으로 User 정보 불러오기

-   인증 완료 후 개인정보를 넘겨준 후 User에 대한 처리 순서

OAuth2 인증 성공 시 → UserArgumentResolver(filter) → supportsParameter 체크 → (yes) → 세션에 User가 있는지 여부 체크 → (yes) → User 파라미터 마인딩

```
                             → (no) → User 객체 생성 후 권한 부여 → User 세션 저장 → User 파라미터 바인딩
```

-   LoginController
    -   인증된 User 정보를 세션에 저장해주는 기능 생성

```java
package spring.boot.security.ex.controller;

import java.time.LocalDateTime;
import java.util.Map;

import javax.servlet.http.HttpSession;

import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.oauth2.provider.OAuth2Authentication;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import spring.boot.security.ex.domain.User;
import spring.boot.security.ex.domain.enums.SocialType;

@Controller
public class LoginController {

    @GetMapping("/login")
    public String login() {
        return "login";
    }

    @GetMapping(value = "/{facebook|google|kakao}/complete") // 인증 성공 후 리다이렉트 경로 설정
    public String loginComplete(final HttpSession session) {
        // 인증된 정보를 OAuth2Authentication 형태로 받아온다.
        final OAuth2Authentication authentication = (OAuth2Authentication)SecurityContextHolder.getContext()
                .getAuthentication();

        // 리소스 서버에서 받아온 개인정보를 Map타입으로 가져온다.
        final Map<String, String> map = (Map<String, String>)authentication.getUserAuthentication().getDetails();

        final User user = User.builder()
                .name(map.get("name"))
                .email(map.get("emial"))
                .principal(map.get("id"))
                .socialType(SocialType.FACEBOOK)
                .createdDate(LocalDateTime.now())
                .build();

        // 세션에 설정
        session.setAttribute("user", user);

        return "redirect:/board/list";
    }
}
```

-   위의 컨트롤러에는 두가지 문제점이 존재한다.
    
    -   컨트롤러에 불필요한 로직 존재
    -   페이스북 인증에만 사용이 가능
    -   실제로 카카오 경우에는 getDetails()를 사용하여 개인정보를 가져와도 해당 키값이 다른 소셜 미디어와 다르기 때문에 다르게 처리해야 한다.
-   위의 문제점을 해결할 수 있는 것이 AOP이다.
    
-   특정 파라미터 형식을 취하면 병렬적으로 User 객체에 인증된 정보를 가져올 수 있다는 것이다.
    
-   LoginController 변경
    

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import spring.boot.security.ex.annotation.SocialUser;
import spring.boot.security.ex.domain.User;

@Controller
public class LoginController {

    @GetMapping("/login")
    public String login() {
        return "login";
    }

    @GetMapping(value = "/{facebook|google|kakao}/complete") // 인증 성공 후 리다이렉트 경로 설정
    public String loginComplete(@SocialUser final User user) {
        return "redirect:/board/list";
    }
}
```

-   파라미터로 AOP를 구현하는 데는 두 가지 방법이 존재
    
    -   직접 AOP 로직을 작성하는 방법
    -   스프링의 전략 인터페이스 중 하나인 HandlerMethodArgumentResolver를 사용
-   HandlerMethodArgumentResolver
    
    -   전략 패턴(특정 전략을 인터페이스로 만들고 이를 여러 전략 객체로 구현, 그리고 현재 클래스 레벨에서 전략 인터페이스를 의존하도록 한다. 이런 식으로 느슨하게 연결된 전략 클래스를 찾아 의존하도록 하는 방식)의 인종으로 컨트롤러 메서드에서 특정 조건에 해당하는 파라미터가 있으면 생성한 로직을 처리한 후 해당 파라미터에 바인딩해주는 전략 인터페이스
        
        ```java
        package org.springframework.web.method.support;
        
        import org.springframework.core.MethodParameter;
        import org.springframework.web.bind.WebDataBinder;
        import org.springframework.web.bind.support.WebDataBinderFactory;
        import org.springframework.web.context.request.NativeWebRequest;
        
        public interface HandlerMethodArgumentResolver {
        
          boolean supportsParameter(MethodParameter parameter);
        
          Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
                  NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception;
        
        }
        ```
        
    -   supportsParameter() : HandlerMethodArgumentResolver가 해당하는 파라미터를 지원할지 여부를 반환
        
    -   resolveArgument() : 파라미터의 인자값에 대한 정보를 바탕으로 실제 객체를 생성하여 해당 파라미터 객체를 바인딩
        
    -   UserArgumentResolver
        
        ```java
        package spring.boot.security.ex.resolver;
        
        import java.time.LocalDateTime;
        import java.util.Map;
        
        import javax.servlet.http.HttpSession;
        
        import org.springframework.core.MethodParameter;
        import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
        import org.springframework.security.core.authority.AuthorityUtils;
        import org.springframework.security.core.authority.SimpleGrantedAuthority;
        import org.springframework.security.core.context.SecurityContextHolder;
        import org.springframework.security.oauth2.provider.OAuth2Authentication;
        import org.springframework.stereotype.Component;
        import org.springframework.web.bind.support.WebDataBinderFactory;
        import org.springframework.web.context.request.NativeWebRequest;
        import org.springframework.web.context.request.RequestContextHolder;
        import org.springframework.web.context.request.ServletRequestAttributes;
        import org.springframework.web.method.support.HandlerMethodArgumentResolver;
        import org.springframework.web.method.support.ModelAndViewContainer;
        
        import lombok.RequiredArgsConstructor;
        import spring.boot.security.ex.annotation.SocialUser;
        import spring.boot.security.ex.domain.User;
        import spring.boot.security.ex.domain.UserRepository;
        import spring.boot.security.ex.domain.enums.SocialType;
        
        @RequiredArgsConstructor
        @Component
        public class UserArgumentResolver implements
              HandlerMethodArgumentResolver { // 전략패턴으로 구현되어 있으며 컨트롤러 메서드에서 특정 조건에 해당하는 파라미터가 있으면 생성한 로직을 처리한 후 해당 파라미터에 바인딩 해주는 전략 인터페이스
        
          private final UserRepository userRepository;
        
          @Override
          public boolean supportsParameter(final MethodParameter parameter) { // 해당하는 파라미터를 지원할지 여부를 반환
              // supportsParameter()의 특징은 처음 한 번 체크된 부분은 캐시되어 동일한 호출 시에는 체크되지 않고 캐시된 결과를 반환한다.
              return parameter.getParameterAnnotation(SocialUser.class) != null &&
                      parameter.getParameterType().equals(User.class); // @SocialUser 어노테이션이 있고 타입이 User인 파라미터만 true를 반환
          }
        
          // 검증이 완료된 파라미터 정보를 받는다.
          // 현재는 검증이 되어 세션에 해당하는 User가 있다면 반환하고 없다면 User를 생성한다.
          @Override
          public Object resolveArgument(final MethodParameter parameter, final ModelAndViewContainer mavContainer,
                  final NativeWebRequest webRequest,
                  final WebDataBinderFactory binderFactory) { // 파라미터의 인자값에 대한 정보를 바탕으로 실제 객체를 생성하여 해당 파라미터 객체를 바인
              final HttpSession session = ((ServletRequestAttributes)RequestContextHolder.currentRequestAttributes()).getRequest()
                      .getSession();
              final User user = (User)session.getAttribute("user");
        
              return getUser(user, session);
          }
        
          // 인증된 User 객체를 만드는 메서드
          public Object getUser(User user, final HttpSession session) {
              if (user == null) {
                  try {
                      final OAuth2Authentication authentication = (OAuth2Authentication)SecurityContextHolder.getContext()
                              .getAuthentication();
                      final Map<String, String> map = (Map<String, String>)authentication.getUserAuthentication()
                              .getDetails();
                      final User convertUser = convertUser(String.valueOf(authentication.getAuthorities().toArray()[0]), map);
        
                      user = userRepository.findByEmail(convertUser.getEmail());
        
                      if (user == null) {
                          user = userRepository.save(convertUser);
                      }
        
                      setRoleIfNotSame(user, authentication, map);
                      session.setAttribute("user", user);
        
                  } catch (final ClassCastException e) {
                      return user;
                  }
              }
              return user;
          }
        
          // 인증된 소셜 미디어 타입에 따라 User 생성
          private User convertUser(final String authority, final Map<String, String> map) {
              if (SocialType.FACEBOOK.isEqual(authority)) {
                  return getModernUser(SocialType.FACEBOOK, map);
              } else if (SocialType.GOOGLE.isEqual(authority)) {
                  return getModernUser(SocialType.GOOGLE, map);
              } else if (SocialType.KAKAO.isEqual(authority)) {
                  return getKakao(SocialType.KAKAO, map);
              }
        
              return null;
          }
        
          public User getModernUser(final SocialType socialType, final Map<String, String> map) {
              return User.builder()
                      .name(map.get("name"))
                      .email(map.get("email"))
                      .principal(map.get("id"))
                      .socialType(socialType)
                      .createdDate(LocalDateTime.now())
                      .build();
          }
        
          public User getKakao(final SocialType socialType, final Map<String, String> map) {
              final Map<String, String> propertyMap = (Map<String, String>)(Object)map.get("properties");
              return User.builder()
                      .name(propertyMap.get("nickname"))
                      .email(propertyMap.get("kaccount_email"))
                      .principal(String.valueOf(map.get("id")))
                      .socialType(socialType)
                      .createdDate(LocalDateTime.now())
                      .build();
          }
        
          // authentication이 권한을 갖고 있는지 확인하는 용도로 저장된 User 권한이 없으면 SecurityContextHolder를 사용하여 해당 소셜 미디어타입으로 권한을 저장
          private void setRoleIfNotSame(final User user, final OAuth2Authentication authentication,
                  final Map<String, String> map) {
              if (!authentication.getAuthorities().contains(new SimpleGrantedAuthority(user.getSocialType().getRoleType()))) {
                  SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(map, "N/A",
                          AuthorityUtils.createAuthorityList(user.getSocialType().getRoleType())));
              }
          }
        }
        ```
        
    -   UserArgumentResolver 등록
        
        ```java
        import java.util.List;
        
        import org.springframework.stereotype.Component;
        import org.springframework.web.method.support.HandlerMethodArgumentResolver;
        import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
        
        import lombok.RequiredArgsConstructor;
        import spring.boot.security.ex.resolver.UserArgumentResolver;
        
        @Component
        @RequiredArgsConstructor
        public class WebMvcConfig extends WebMvcConfigurerAdapter {
        
          private final UserArgumentResolver userArgumentResolver;
        
          @Override
          public void addArgumentResolvers(final List<HandlerMethodArgumentResolver> argumentResolvers) {
              argumentResolvers.add(userArgumentResolver);
              super.addArgumentResolvers(argumentResolvers);
          }
        }
        ```
        
    -   SocialUser
        
        ```java
        
        import java.lang.annotation.ElementType;
        import java.lang.annotation.Retention;
        import java.lang.annotation.RetentionPolicy;
        import java.lang.annotation.Target;
        
        @Target(ElementType.PARAMETER)
        @Retentiㅁon(RetentionPolicy.RUNTIME)
        public @interface SocialUser {
        }
        ```
        

### 5.4.4. 인증 동작 확인하기

-   login.html

```java
<!doctype html>
<html lang="ko" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>login</title>
    <link rel="stylesheet" href="@{/css/base.css}"/>
    <link rel="stylesheet" href="@{/css/bootstrap.min.css"/>
</head>
<body>
<div th:replace="layout/header::header"></div>

<div class="container" style="text-align: center">
    <br/>
    <h2>로그인</h2>
    <br/>
    <br/>

    <a href="javascript:;" class="btn_social" data-social="facebook">
        <img th:src="@{/images/facebook.png}" width="40px" height="40px"/>
    </a>
    <a href="javascript:;" class="btn_social" data-social="google">
        <img th:src="@{/images/google.png}" width="40px" height="40px"/>
    </a>
    <a href="javascript:;" class="btn_social" data-social="kakao">
        <img th:src="@{/images/kakao.png}" width="40px" height="40px"/>
    </a>
</div>

<div th:replace="layout/footer::footer"></div>

<script>
    const socialButton = document.querySelector('.btn_social')

    function onClick() {
        const socialType = socialButton.data('social');
        location.href = "/login/" + socialType;
    }

    socialButton.addEventListener("click", onClick);
</script>
</body>
</html>
```

## 5.5. 스프링 부트 2.0 기반의 OAuth2 설정하기

### 5.5.1 스프링 부트 2.0 버전으로 의존성 업그레이드

-   2.0으로 올리기

```java
implementation 'org.springframework.security.oauth:spring-security-oauth2-client'
implementation 'org.springframework.security.oauth:spring-security-oauth2-jose'
implementation 'org.springframework.boot:spring-boot-starter-security'
```

-   기본적인 OAuth2 인증 관련 객체들이 시큐리티로 이전되었으며, 2.0에서는 클라이언트 자동 인증 설정을 위해 spring-security-oauth2-client를 추가합니다.
-   2.0에서 JWT(JSON Web Tokens)와 관련된 권한을 안전하게 전송하기 위한 프레임워크인 JSOE(Javascript Object Signing and Encryption)이 추가되었다.
-   JWT에는 자신의 리소스에 접근할 수 있는 권한 정보가 들어있는데 JOSE는 JWT의 함호화/복호화 및 일정한 기능을 제공
-   1.5에는 타임리프에 spring-boot-starter-web에 대한 설정도 포함되었지만, 2.0부터는 포함되지 않았다. 또한 2.0부터는 타임리프의 java8time 설정이 임리프 스타터 설정에 포함되었기 때문에 제거

### 5.5.2. 스프링 부트 2.0 방식의 OAuth2 인증 재설정

-   CommonOAuth2Provider

```java
package org.springframework.security.config.oauth2.client;

import org.springframework.security.oauth2.client.registration.ClientRegistration;
import org.springframework.security.oauth2.client.registration.ClientRegistration.Builder;
import org.springframework.security.oauth2.core.AuthorizationGrantType;
import org.springframework.security.oauth2.core.ClientAuthenticationMethod;
import org.springframework.security.oauth2.core.oidc.IdTokenClaimNames;

public enum CommonOAuth2Provider {

    GOOGLE {

        @Override
        public Builder getBuilder(String registrationId) {
            ClientRegistration.Builder builder = getBuilder(registrationId,
                    ClientAuthenticationMethod.BASIC, DEFAULT_REDIRECT_URL);
            builder.scope("openid", "profile", "email");
            builder.authorizationUri("https://accounts.google.com/o/oauth2/v2/auth");
            builder.tokenUri("https://www.googleapis.com/oauth2/v4/token");
            builder.jwkSetUri("https://www.googleapis.com/oauth2/v3/certs");
            builder.userInfoUri("https://www.googleapis.com/oauth2/v3/userinfo");
            builder.userNameAttributeName(IdTokenClaimNames.SUB);
            builder.clientName("Google");
            return builder;
        }
    },

    GITHUB {

        @Override
        public Builder getBuilder(String registrationId) {
            ClientRegistration.Builder builder = getBuilder(registrationId,
                    ClientAuthenticationMethod.BASIC, DEFAULT_REDIRECT_URL);
            builder.scope("read:user");
            builder.authorizationUri("https://github.com/login/oauth/authorize");
            builder.tokenUri("https://github.com/login/oauth/access_token");
            builder.userInfoUri("https://api.github.com/user");
            builder.userNameAttributeName("id");
            builder.clientName("GitHub");
            return builder;
        }
    },

    FACEBOOK {

        @Override
        public Builder getBuilder(String registrationId) {
            ClientRegistration.Builder builder = getBuilder(registrationId,
                    ClientAuthenticationMethod.POST, DEFAULT_REDIRECT_URL);
            builder.scope("public_profile", "email");
            builder.authorizationUri("https://www.facebook.com/v2.8/dialog/oauth");
            builder.tokenUri("https://graph.facebook.com/v2.8/oauth/access_token");
            builder.userInfoUri("https://graph.facebook.com/me?fields=id,name,email");
            builder.userNameAttributeName("id");
            builder.clientName("Facebook");
            return builder;
        }
    },

    OKTA {

        @Override
        public Builder getBuilder(String registrationId) {
            ClientRegistration.Builder builder = getBuilder(registrationId,
                    ClientAuthenticationMethod.BASIC, DEFAULT_REDIRECT_URL);
            builder.scope("openid", "profile", "email");
            builder.userNameAttributeName(IdTokenClaimNames.SUB);
            builder.clientName("Okta");
            return builder;
        }
    };

    private static final String DEFAULT_REDIRECT_URL = "{baseUrl}/{action}/oauth2/code/{registrationId}";

    protected final ClientRegistration.Builder getBuilder(String registrationId,
                                                            ClientAuthenticationMethod method, String redirectUri) {
        ClientRegistration.Builder builder = ClientRegistration.withRegistrationId(registrationId);
        builder.clientAuthenticationMethod(method);
        builder.authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE);
        builder.redirectUriTemplate(redirectUri);
        return builder;
    }

    public abstract ClientRegistration.Builder getBuilder(String registrationId);

}
```

-   위와 같이 소셜 미디어의 기본 값을 제공해준다.
-   따라서 Id, Secret key만 등록하면 된다

```java
spirng:
    ...
    security:
        oauth2:
            client:
                registration:
                    google:
                        client-id:
                        client-secret:
                    facebook:
                        client-id:
                        client-secret:
```

-   그러면 카카와 같이 없는 소셜미디어는 어떻게 처리해야 할까요? OAuth2 API에서 제공하는 방법과 동일하게 제공
    
-   CustomOAuth2Provider
    
    -   카카오 정보만 담은 객체

```java
package springbootsecurity2.demo.web.oauth2;

import org.springframework.security.oauth2.client.registration.ClientRegistration;
import org.springframework.security.oauth2.core.AuthorizationGrantType;
import org.springframework.security.oauth2.core.ClientAuthenticationMethod;

public enum CustomOAuth2Provider {
    KAKAO {
        @Override
        public ClientRegistration.Builder getBuilder(final String registrationId) {
            final ClientRegistration.Builder builder = getBuilder(registrationId, ClientAuthenticationMethod.POST,
                    CustomOAuth2Provider.DEFAULT_LOGIN_REDIRECT_URL);
            builder.scope("profile");
            builder.authorizationUri("https://kauth.kakao.com/oauth/token");
            builder.tokenUri("https://kauth.kakao.com/oauth/token");
            builder.userInfoUri("https://kauth.kakao.com/oauth/token");
            builder.userNameAttributeName("id");
            builder.clientName("Kakao");
            return builder;
        }
    };

    private static final String DEFAULT_LOGIN_REDIRECT_URL = "{baseUrl}/login/oauth2/code/{registrationId}";

    protected final ClientRegistration.Builder getBuilder(final String registrationId, final ClientAuthenticationMethod method,
            final String redirectUri) {
        final ClientRegistration.Builder builder = ClientRegistration.withRegistrationId(registrationId);
        builder.clientAuthenticationMethod(method);
        builder.authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE);
        builder.redirectUriTemplate(redirectUri);
        return builder;
    }

    public abstract ClientRegistration.Builder getBuilder(String registrationId);
}
```

-   카카오는 클라이언트 ID 값만 필요하기 때문에 임의로 client-id의 값을 참조할 수 있도록 프로퍼티에 값을 추가한다.

```java
custom:
    oauth:
        kakao:
            client-id:
```

-   SecurityConfig

```java
package spring.security.ex.security;

import java.util.List;
import java.util.Objects;
import java.util.stream.Collectors;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.security.oauth2.client.OAuth2ClientProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.oauth2.client.CommonOAuth2Provider;
import org.springframework.security.oauth2.client.registration.ClientRegistration;
import org.springframework.security.oauth2.client.registration.ClientRegistrationRepository;
import org.springframework.security.oauth2.client.registration.InMemoryClientRegistrationRepository;
import org.springframework.security.web.authentication.LoginUrlAuthenticationEntryPoint;
import org.springframework.security.web.csrf.CsrfFilter;
import org.springframework.web.filter.CharacterEncodingFilter;

import lombok.RequiredArgsConstructor;
import spring.security.ex.client.CustomOAuth2Provider;
import spring.security.ex.domain.enums.SocialType;

@RequiredArgsConstructor
@Configuration
@EnableWebSecurity // 시큐리티 기능 사용
public class SecurityConfig extends WebSecurityConfigurerAdapter { // 요청, 권한, 기타 설정에 대해서 필수적으로 최적화한 설정을 위해 상속

    //@formatter:off
    @Override
    protected void configure(final HttpSecurity http) throws Exception { // 요청, 권한, 기타 설정에 대한 필수적 설정
        // 문자 인코딩 설정
        final CharacterEncodingFilter filter = new CharacterEncodingFilter();

        http
                .authorizeRequests()
                // 요청을 패턴 형식으로 정해 누구나 접근할 수 있도록 설정
                .antMatchers("/", "/login/**", "/css/**", "/images/**", "/js/**", "/console/**").permitAll()
                // 위에서 설정한 패턴을 제외한 나머지는 인증된 사용자만 사용 가능
                .antMatchers("/bacebook").hasAuthority(SocialType.FACEBOOK.getRoleType())
                .antMatchers("/google").hasAuthority(SocialType.GOOGLE.getRoleType())
                .antMatchers("/kakao").hasAuthority(SocialType.KAKAO.getRoleType())
                .anyRequest().authenticated()
            .and()
                // 응답 헤더
                .headers()
                // XFrameOptionsHeaderWriter의 최적화 설정을 허용하지 않음.
                .frameOptions().disable()
            .and()
                .exceptionHandling()
                // 인증되지 않은 사용자가 허용되지 않은 리소스를 접근할 경우 "/login" 경로로 이동
                .authenticationEntryPoint(new LoginUrlAuthenticationEntryPoint("/login"))
            .and()
                // 로그인 폼
                .formLogin()
                // 성공시 해당 Url로 이동
                .successForwardUrl("/board/list")
            .and()
                // 로그아웃 설정
                .logout()
                // 로그아웃 Url
                .logoutUrl("/logout")
                // 로그아웃 성공 Url
                .logoutSuccessUrl("/")
                // 로그아웃 시 해당 쿠기 삭제
                .deleteCookies("JSESSIONID")
                // 설정된 섹션의 무효화
                .invalidateHttpSession(true)
            .and()
                // 첫번째 인자보다 먼저 시작될 필터
                .addFilterBefore(filter, CsrfFilter.class)
                // csrf 사용 하지 않는다는 의미
                .csrf().disable();
    }
    //@formatter:on

    // 인증 정보를 추가한다.
    @Bean
    public ClientRegistrationRepository clientRegistrationRepository(
            final OAuth2ClientProperties oAuth2ClientProperties,
            @Value("${custom.oauth2.kakao.spring.security.ex.client-id") final String kakaClientId) {

        final List<ClientRegistration> registrations = oAuth2ClientProperties.getRegistration().keySet().stream()
                .map(client -> getRegistration(oAuth2ClientProperties, client))
                .filter(Objects::nonNull)
                .collect(Collectors.toList());

        registrations.add(CustomOAuth2Provider.KAKAO.getBuilder("kakao")
                .clientId(kakaClientId)
                .clientSecret("test") // 필요없지만 null이면 실행이 되지 않음
                .jwkSetUri("test") // 필요없지만 null이면 실행이 되지 않음
                .build());

        return new InMemoryClientRegistrationRepository(registrations);
    }

    // 구글, 페이스북의 인증 정보 빌드
    private ClientRegistration getRegistration(final OAuth2ClientProperties clientProperties, final String client) {
        if ("google".equals(client)) {
            final OAuth2ClientProperties.Registration registration = clientProperties.getRegistration().get("goolge");

            return CommonOAuth2Provider.GOOGLE.getBuilder(client)
                    .clientId(registration.getClientId())
                    .clientSecret(registration.getClientSecret())
                    .scope("email", "profile")
                    .build();
        } else if ("facebook".equals(client)) {
            final OAuth2ClientProperties.Registration registration = clientProperties.getRegistration().get("facebook");

            CommonOAuth2Provider.FACEBOOK.getBuilder(client)
                    .clientId(registration.getClientId())
                    .clientSecret(registration.getClientSecret())
                    .userInfoUri(
                            "https://graph.facebook.com/me?fields=id,name,email,link") // 페이스 북의 GraphAPI의 경우 scope()로는 필요한 필드 반환해주지 않기 때문에 직접 id, name, email, link 등을 파라미터로 넣어 요청하도록 설정
                    .scope("email")
                    .build();
        }

        return null;
    }
}
```

-   LoginController
    -   요청 성공 시 URI

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import spring.security.ex.annotation.SocialUser;
import spring.security.ex.domain.User;

@Controller
public class LoginController {

    @GetMapping("/login")
    public String login() {
        return "login";
    }

    @GetMapping
    public String loginComplete(@SocialUser final User user) {
        return "redirect:/board/list";
    }
}
```

-   login.html
    -   요청 스크립트 코드 변경

```java
"/oauth2/authorization/" + socialType; // URL을 변경한다.
```

-   UserArgumentResolver
    -   반환된 엑세스 토큰값을 사용하여 User 정보를 가져오는 로직 생성

```java
import java.time.LocalDateTime;
import java.util.Map;

import javax.servlet.http.HttpSession;

import org.springframework.core.MethodParameter;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.authority.AuthorityUtils;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import lombok.RequiredArgsConstructor;
import spring.security.ex.annotation.SocialUser;
import spring.security.ex.domain.User;
import spring.security.ex.domain.UserRepository;
import spring.security.ex.domain.enums.SocialType;

@RequiredArgsConstructor
@Component
public class UserArgumentResolver implements
        HandlerMethodArgumentResolver { // 전략패턴으로 구현되어 있으며 컨트롤러 메서드에서 특정 조건에 해당하는 파라미터가 있으면 생성한 로직을 처리한 후 해당 파라미터에 바인딩 해주는 전략 인터페이스

    private final UserRepository userRepository;

    @Override
    public boolean supportsParameter(final MethodParameter parameter) { // 해당하는 파라미터를 지원할지 여부를 반환
        // supportsParameter()의 특징은 처음 한 번 체크된 부분은 캐시되어 동일한 호출 시에는 체크되지 않고 캐시된 결과를 반환한다.
        return parameter.getParameterAnnotation(SocialUser.class) != null &&
                parameter.getParameterType().equals(User.class); // @SocialUser 어노테이션이 있고 타입이 User인 파라미터만 true를 반환
    }

    // 검증이 완료된 파라미터 정보를 받는다.
    // 현재는 검증이 되어 세션에 해당하는 User가 있다면 반환하고 없다면 User를 생성한다.
    @Override
    public Object resolveArgument(final MethodParameter parameter, final ModelAndViewContainer mavContainer,
            final NativeWebRequest webRequest,
            final WebDataBinderFactory binderFactory) { // 파라미터의 인자값에 대한 정보를 바탕으로 실제 객체를 생성하여 해당 파라미터 객체를 바인
        final HttpSession session = ((ServletRequestAttributes)RequestContextHolder.currentRequestAttributes()).getRequest()
                .getSession();
        final User user = (User)session.getAttribute("user");

        return getUser(user, session);
    }

    // 인증된 User 객체를 만드는 메서드
    public Object getUser(User user, final HttpSession session) {
        if (user == null) {
            try {
                // 2.0부터는 기존의 OAuth2Authentication이 아닌 엑세스 토큰까지 제공한다는 의미에서 OAuth2AuthenticationToken을 제공한다.
                final OAuth2AuthenticationToken authentication = (OAuth2AuthenticationToken)SecurityContextHolder.getContext()
                        .getAuthentication();
                final Map<String, Object> map = authentication.getPrincipal().getAttributes();
                // 예전에는 getAuthorities로 권한을 불러와 소셜미디어 확인을 했다면 이제는 getAuthorizedClientRegistrationId를 통해 파악이 가능하다.
                final User convertUser = convertUser(authentication.getAuthorizedClientRegistrationId(), map);

                user = userRepository.findByEmail(convertUser.getEmail());

                if (user == null) {
                    user = userRepository.save(convertUser);
                }

                setRoleIfNotSame(user, authentication, map);
                session.setAttribute("user", user);

            } catch (final ClassCastException e) {
                return user;
            }
        }
        return user;
    }

    // 인증된 소셜 미디어 타입에 따라 User 생성
    private User convertUser(final String authority, final Map<String, Object> map) {
        if (SocialType.FACEBOOK.isEqual(authority)) {
            return getModernUser(SocialType.FACEBOOK, map);
        } else if (SocialType.GOOGLE.isEqual(authority)) {
            return getModernUser(SocialType.GOOGLE, map);
        } else if (SocialType.KAKAO.isEqual(authority)) {
            return getKakao(SocialType.KAKAO, map);
        }

        return null;
    }

    public User getModernUser(final SocialType socialType, final Map<String, Object> map) {
        return User.builder()
                .name(String.valueOf(map.get("name")))
                .email(String.valueOf(map.get("email")))
                .principal(String.valueOf(map.get("id")))
                .socialType(socialType)
                .createdDate(LocalDateTime.now())
                .build();
    }

    public User getKakao(final SocialType socialType, final Map<String, Object> map) {
        final Map<String, String> propertyMap = (Map<String, String>)map.get("properties");
        return User.builder()
                .name(propertyMap.get("nickname"))
                .email(propertyMap.get("kaccount_email"))
                .principal(String.valueOf(map.get("id")))
                .socialType(socialType)
                .createdDate(LocalDateTime.now())
                .build();
    }

    // authentication이 권한을 갖고 있는지 확인하는 용도로 저장된 User 권한이 없으면 SecurityContextHolder를 사용하여 해당 소셜 미디어타입으로 권한을 저장
    private void setRoleIfNotSame(final User user, final OAuth2AuthenticationToken authentication,
            final Map<String, Object> map) {
        if (!authentication.getAuthorities().contains(new SimpleGrantedAuthority(user.getSocialType().getRoleType()))) {
            SecurityContextHolder.getContext().setAuthentication(new UsernamePasswordAuthenticationToken(map, "N/A",
                    AuthorityUtils.createAuthorityList(user.getSocialType().getRoleType())));
        }
    }
}

```

-   2.0 버전에서는 기존의 OAuth2Authentication이 아닌 액세스 토큰까지 제공한다는 의미에서 OAuth2AuthenticationToken을 지원한다. SecurityContextHolder에서 OAuth2AuthenticationToken을 가져온다.
-   개인정보를 getAttributes() 메서드를 통해 Map 타입으로 가져온다.
-   예전에는 getAuthorities() 메서드로 권한을 불러와서 인증된 소셜 미디어가 어디인지 알았다면 이제는 getAuthorizedClientRegistrationId() 메서드로 파악할 수 있다.
