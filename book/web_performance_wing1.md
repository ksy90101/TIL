# 1장. 웹 성능이란 무엇인가?

## 웹이란?
- 전 셰계 사람들이 인터넷에 연결된 컴퓨터를 통해 수 많은 정보를 공유 할 수 있는 인터넷의 대표적인 서비스
- 물리학자들 간의 공동 연구를 진행하고 결과를 공유하기 위해 팀 버너스 리 등이 개발
- 이전에도 TCP/IP 또는 UDP를 통해 클라이언트-서버 간 네트워크 통신 또는 소켓 네트워크 기술을 통해 데이터 교환이 가능했지만, 웹은 페이지 기반으로 문서 읽듯 브라우저를 통해 정보를 교환하는 방법
- 즉, 하이퍼텍스트를 바탕으로 관련 있는 문서들을 연결한 집합체
- 따라서 사용자는 서로 연관되어 있는 페이지를 연결해 웹 페이지의 하이퍼링크를 통해 다른 페이지로 이동하거나 사용자 정보를 입력할 수 있음.

## 웹의 대표적인 요소
### URL(Uniform Resource Locator)
- 인터넷 상에서 웹 자원이 어느 위치에 존재하는지 알려주는 방법
<img width="802" alt="image" src="https://user-images.githubusercontent.com/53366407/182019169-a786f947-4ca7-408f-9669-e3625aec8df1.png">

### 프로토콜(Protocol)
- 웹 자원 위치를 접근하는 방식
- 대표적으로 HTTP를 사용하며, 헤더(Header) / 바디(Body)로 구성되어 있다.
<img width="1027" alt="image" src="https://user-images.githubusercontent.com/53366407/182019184-ba326f0e-0ad2-439f-a8ea-9082a4e15e76.png">

### HTML(HpyerText Mark-up Language)
- 웹 상의 콘텐츠를 브라우저를 통해 나타낼 수 있는 방법
- 태그라는 명령어로 웹 목적에 맞는 여러 기능을 수행한다.
- 웹 페이지에 실제로 나타낼 데이터를 정의, 문서의 구조를 표현
- HTML 말고도 CSS(레이아웃과 세부 형태 정의), JavaScript(로직을 실행)를 통해 콘텐츠를 나타낼 수 있음.

## 웹 성능이 중요한 이유
- 콘텐츠가 신속하게 전달 되어 사용자가 원하는 서비스를 빠르게 전달받을 수 있도록 웹 로딩 시간을 단축 해 이탈율을 줄이기 위함.
- 평균적으로 최소 3초 이하에 로딩이 되어야 함. (3초의 법칙)
- 3초의 법칙이란?
	- 웹 사이트의 로딩이 빨라야 함.
	- 웹 사이트의 머리말(headline)이 주목 받아야 함.
	- 웹 페이지의 글이 눈에 띄어야 함.
	- 웹 페이지 내 사용자 행동이 필요한 부분을 명확히 전달
- 3초의 법칙에서 1번이 웹 성능과 관련되어 있으며, 로딩에 영향을 미치는 것은 많은 이미지와 광고 노출이며, 콘텐츠는 유지하면서 웹 성능을 향상시키는 웹 성능 최적화(Web Perforamce Optimization, WPO)에 관심을 갖게 됨,

## 웹 성능 측정 방법
- 가장 중요한건 웹 성능에 영향을 주는 요소를 파악해야 함.
- 사용자 입장에선 거주 지역, 네트워크 장비, 브라우저 등에 대한 환경에 따라 달라짐
- 웹 사이트 공급자 입장에서는 DNS 서버 응답 속도, 웹 서버 응답 속도, 웹 사이트 백엔드 처리 속도, 프런트 엔드 최적화 여부에 따라 달라짐.
- 웹 성능에 영향을 주는 대표적인 환경
	- 사용자
	- 전달
	- 공급자
	- 네트워크 / 디바이스 / 브라우저
	- 전용선 / 유선망 / 모바일망
	- 백엔드 / 프론트 엔드

### 크롬 브라우저 개발자 도구
- 개발자 도구의 네트워크 창에서 확인이 가능하며, 파일 로딩 시간이 얼마나 걸리는지 확인이 가능하다.
- Network 탭에서 **전체 HTTP 요청 수와 응답수**, 전달받은 콘텐츠 파일들의 크기, **DOMContentLoaded** 시간, **Load시간**, **로딩 완료 시간(Finish)**을 확인할 수 있음

### [WebapgeTest 서비스](https://www.webpagetest.org/)
- 세계 여러 위치에서 웹 사이트 로딩 속도를 테스트할수 있는 무료 서비스
- 각 옵션의 기능
	- 기본 옵션
		- URL : 테스트하려는 웹 사이트 주소를 입력
		- Test Location : 실제 접속하는 테스트 장비의 위치를 선택
		- Browser: 테스트를 수행할 브라우저 종류를 선택
	- 추가옵션
		- Connection : 테스트에 사용할 네트워크 종류를 선택
		- Number of Test to Run : 동일한 조건으로 몇 회 테스트할지 선택. CDN, 브라우징 캐싱을 수행한 상태에서 웹 성능을 테스트 하려면 최소 2회 이상 필요
		- Repect View: 여러번 테스트할 때 어떤 결과화면을 볼지
		- Cpture Video: 테스트할 때 브라우저의 실제 로딩 화면을 비디오로 캡처
		- Keep Test Private: 테스트 결과 공개 결정
		- Label: 테스트 구분 이름
		- 그 외에도 자바스크립트 실행하지 않도록 하거나 인증 정보, 요청 헤더 추가 등의 옵션이 그낭
- 평가 항목
	- First Byte Time : 웹 서버에서 받은 콘텐츠가 첫 번째 바이트가 얼마 만에 도착했는가?
	- Keep-Alive Enabled: TCP 연결을 재사용하기 위한 Keep-Alive가 설정되어 있는가?
	- Compress Image: 이미지 압축해 최적화 했는가
	- Compress Transfer: 스크립트 파일이 Content-Encoding으로 압축되어 있는가?
	- Cache Static cotent: 정적 파일에 브라우저 캐시가 설정되어 있는가
	- Effective use of CDN: CDN을 효과적으로 적용했는가?

### [google PageSpeed](https://pagespeed.web.dev/)
- 테스트 지역이나 세세한 옵션 선택은 불가하지만 PC 모바일 환경의 웹 성능 테스트 결과를 심플하게 보여줌
- FCP(First Contentful Paint)와 DCL(DOM Content Loaded) 두개 메트릭스를 사용해 특정 웹 페이지 성능을 알려주는데,  FCP는 웹 페이지가 사용자에게 시각적 응답을 보인 시간이며, DCL은 브라우저가 HTML 문서를 로딩 및 해석하는 시간을 측정한 값
- 최적화에 따라 어떻게 개선할 수 있는지 알려줌
- [github action](https://github.com/marketplace/actions/page-speed-insights)을 통해 자동화도 가능

### google analytics의 speed 평가 항목
- 백엔드의 성능을 빠르게 알 수 있는 방법으로 특정 기간 동안 접속한 사용자의 평균 로딩 시간을 알 수 있음.
- 재전송(rediection), 도메인 조회(domain lookup), 서버 연결(sever connection), 서버 응답(server response)등에 소요된 시간을 확인 할 수 있음.


## 웹 성능을 만드는 지표
- 스티브 사우더시의 High Performance Web Sites는 아래와 같이 정의함

### 백엔드 최적화
- Expries 헤더를 추가한다.
- gzip으로 압축한다
- 페이지 재전송을 피한다
- ETag를 설정한다
- 캐시를 지원하는 AJAX를 만든다

### 프런트 엔드
- HTTP 요청을 줄인다
- 스타일 시트는 상단에 넣는다
- 스크립트는 하단에 넣는다
- CSS 표현식은 피한다
- 자바스크립트와 CSS는 외부 파일에 넣는다
- 자바스크립트는 작게 한다
- 중복 스크립트는 제거한다
### 네트워크
- CDN을 사용한다
- DNS 조회를 줄인다

### Web Performance Best Practices and Rules
1. [Minimize HTTP Requests](http://developer.yahoo.com/performance/rules.html#num_http)
2. [Use a Content Delivery Network](http://developer.yahoo.com/performance/rules.html#cdn)
3. [Avoid empty src or href](http://developer.yahoo.com/performance/rules.html#emptysrc)
4. [Add an Expires or a Cache-Control Header](http://developer.yahoo.com/performance/rules.html#expires)
5. [Gzip Components](http://developer.yahoo.com/performance/rules.html#gzip)
6. [Put StyleSheets at the Top](http://developer.yahoo.com/performance/rules.html#css_top)
7. [Put Scripts at the Bottom](http://developer.yahoo.com/performance/rules.html#js_bottom)
8. [Avoid CSS Expressions](http://developer.yahoo.com/performance/rules.html#css_expressions)
9. [Make JavaScript and CSS External](http://developer.yahoo.com/performance/rules.html#external)
10. [Reduce DNS Lookups](http://developer.yahoo.com/performance/rules.html#dns_lookups)
11. [Minify JavaScript and CSS](http://developer.yahoo.com/performance/rules.html#minify)
12. [Avoid Redirects](http://developer.yahoo.com/performance/rules.html#redirects)
13. [Remove Duplicate Scripts](http://developer.yahoo.com/performance/rules.html#js_dupes)
14. [Configure ETags](http://developer.yahoo.com/performance/rules.html#etags)
15. [Make AJAX Cacheable](http://developer.yahoo.com/performance/rules.html#cacheajax)
16. [Use GET for AJAX Requests](http://developer.yahoo.com/performance/rules.html#ajax_get)
17. [Reduce the Number of DOM Elements](http://developer.yahoo.com/performance/rules.html#min_dom)
18. [No 404s](http://developer.yahoo.com/performance/rules.html#no404)
19. [Reduce Cookie Size](http://developer.yahoo.com/performance/rules.html#cookie_size)
20. [Use Cookie-Free Domains for Components](http://developer.yahoo.com/performance/rules.html#cookie_free)
21. [Avoid Filters](http://developer.yahoo.com/performance/rules.html#no_filters)
22. [Do Not Scale Images in HTML](http://developer.yahoo.com/performance/rules.html#no_scale)
23. [Make favicon.ico Small and Cacheable](http://developer.yahoo.com/performance/rules.html#favicon)


## 웹 성능과 프런트엔드
- 프론트 엔드가 페이지 로딩 시간 중 대부분을 차이하는 이유는 웹 서버가 아닌 사용자 관점에서 원하는 콘텐츠를 전달 받았는지가 기준이기 떄문.
- 개발자 도구의 Lighthouse 기능을 이요하면 현재 접속한 웹 사이트 성능 정보를 볼 수 있음.

### 브라우저 랜더링 성능 지표
- FCP(First Contentful Paint): 첫 번째 텍스트 또는 이미지가 표시되는 데 걸린 시간
- SI(Speed Index): 페이지 콘텐츠가 얼마나 빨리 표시되는지에 대한 정보
- LCP(Largest Contentful Paint): 가장 큰 텍스트 또는 이미지가 표시된 시간
- TTI(Time to Interactive): 사용자와 페이지가 상호 작용할 수 있게 된 시간
- TBT(Total Blocking Time): FCP와 TTI 사이 모든 시간의 합으로 작업 지속 시간이 50ms를 초과하면 밀리초 단위로 표현
- CLS(Cumulative Layout Shift): 표시 영역 안에 보이는 요소들이 얼마나 이동하는지에 대한 정보


## 웹 성능 예산
- 성능 예산이란 웹 성능에 영향을 미치는 다양한 요소를 제어하는 한계값을 의미
- 성능 예산을 웹 개발팀의 성능 목표로 만든 대표적인 예
	- 메인 페이지의 모든 오브젝트 파일 크기는 10MB를 넘지 않도록 한다.
	- 모든 웹 페이지의 각 페이지 내 포함된 자바스크립트 크기가 1MB를 넘지 않아야 한다.
	- LTE 환경에서 모바일 기기의 Time to Interactive는 5초 미만이여야 한다.

### 정량 기반 지표
- 이미지, 스크립트, 폰트 등 웹 페이지 제작에 필요한 요소들에 대한 한계값
	- 이미지 파일 최대 크기
	- 최대 웹 폰트 파일 개수
	- 자바스크립트 파일 크기 합
	- 타사 스크립트 개수 합
- 파일 크기만이 아니라 어떤 순서로 요소를 호출할지, 페이지 레이아웃은 어떻게 설계되었는지에 대한 많은 고민이 필요합니다.


### 시간 기반 지표
- FCP(First Contentful Paint): 첫 번째 텍스트 또는 이밎가 표시되는 데 걸린 시간
- TTI(Time to Interactive): 사용자와 페이지가 상호 작용할 수 있게 된 시간입니다.

### 규칙 기반 지표
- WEbpageTest의 성능 점수
- 구글 Lighthouse의 성능 점수

### 성능 예산 활용
- 직관적이고 단순하게 성능 예산 목표치를 설정하고 웹 사이트 설계와 개발을 시작
- 구글 Lighthouse의 측정 값을 빌드의 CI 단계의 테스트 케이스로 사
