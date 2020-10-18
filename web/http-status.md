# HTTP Status

## HTTP Status란?

- 클라이언트와 서버가 요청과 응답을 주고 받을 때 서버의 응답을 알려주는데 사용합니다.
- 결과 내용을 body에 상세하게 적어줄수도 있지만, 시간 비용이 많이 들며 또한 클라이언트 개발자와 서버 개발자 간의 메시지 포멧이 다양하기 때문에 소통에 문제가 생길 수 있습니다.
- 이러한 문제점을 해결하기 위해 숫자로 결과가 어떤지에 대해 표준약속을 정해놓았으며 이를 HTTP Status(상태코드)라고 합니다.

## Informational 1xx

- 정보를 응답하는 것이며, 요청을 받고 프로세스를 계속 진행합니다.

### 100 Continue

- 임시적인 응답으로 지금까지의 요청이 문제가 없고 클라이언트에게 계속해서 요청을 하거나 이미 요청이 완료된 경우에는 무시해도 됨을 의미합니다.
- 클라이언트는 `100 continue`의 응답을 기대한다는 의미로 Expect 요청 헤더가 필요하다.

```json
GET / HTTP/1.1
Expect: 100-continue

HTTP/1.1 100 Continue
```

- 혹시 연결이 되지 않거나 잘못된 요청이면 `417 Expectation Failed` 를 보내줘야 한다.

### 101 Switching Protocols

- 클라이언트의 `Upgrade` 헤더에 대한 응답에 사용합니다.
- `Upgrade` 헤더에 있는 프로토콜로 변경할 것을 뜻한다.

```json
GET wss://chat.jasoseol.com/app/5681f4706c765fb544dc?protocol=7&client=js&version=5.0.2&flash=false HTTP/1.1
Host: chat.jasoseol.com
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Safari/537.36
Upgrade: websocket
Origin: https://jasoseol.com
Sec-WebSocket-Version: 13
Accept-Encoding: gzip, deflate, br
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Sec-WebSocket-Key: ygVyeREzE++jSPKjcgidWQ==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits

HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: GJJs9pfRSoP3asWXu2OpwysaTus=
```

### 102 Processing

- 서버가 요청을 수신했으며 요청을 처리하고 있지만, 아직 처리되지 않은(제대로 된 응답을 알려줄수 없음) 상태를 알려줍니다.
- `WebDAV` 의 사양으로 현재는 잘 사용하지 않는 코드입니다.

```json
GET / HTTP/1.1

HTTP/1.1 102 Processing

HTTP/1.1 200 OK
Content-Type: application/json
```

### 103 Early Hints

- 이 상태 코드는 주로 `Link`헤더와 함께 사용하며 서버가 응답을 준비하는 동안 클라이언트가 사전 로딩을 시작할 수 있도록 합니다.
- 예를들어 아래와 같이 사용하여 문서를 랜더링 하기 위해 특정 리소스 파일이 필요하다는 것을 알릴 수 있습니다.

```json
GET / HTTP/1.1
Host: example.com

HTTP/1.1 103 Early Hints
Link: </style.css>; rel=preload; as=style
Link: </script.js>; rel=preload; as=script

HTTP/1.1 200 OK
Date: Fri, 26 May 2017 10:02:11 GMT
Content-Length: 1234
Content-Type: text/html; charset=utf-8
Link: </style.css>; rel=preload; as=style
Link: </script.js>; rel=preload; as=script

<!doctype html>
[... rest of the response body is omitted from the example ...]
```

## Successful 2xx

- 클라이언트의 요청이 성공했다는 의미입니다.

### 200 OK

- 요청이 정상적으로 성공해 응답을 내려주는 경우 입니다.
- 메소드에 따라 성공의 의미가 달라집니다.
- GET : 리소스를 불러와 메시지 바디에 전송되었습니다.
- HEAD: 헤더가 메시지 바디에 있습니다.
- PUT or POST : 수행 결과에 대한 리소스가 메시지 바디에 전송
- TRACE: 서버에서 수신한 요청 메시지를 포함

```
GET /hello-world.txt HTTP/1.1
Accept: text/*

HTTP/1.1 200 OK
Content-Type: text/markdown
Content-Length: 778

[200 OK][1] is the most common HTTP status code. It generally means that the
HTTP request succeeded.
```

### 201 Created

- 요청이 성공적이며 그 결과 새로운 리소스가 생성되었다는 의미입니다.
- 일반적으로 POST Or PUT에 사용됩니다.
- 응답에는 두가지가 들어가야 합니다.
    - Location 헤더 : 생성된 리소스에 대한 참조
    - 본문 : 그 리소스를 참조할 수 있는 여러 URL

```json
POST /collection/add-member HTTP/1.1
Content-Type: application/json
Host: example.org

{ "foo": "bar" }

HTTP/1.1 201 Created
ETag: "gir-zim"
Location: /collection/546
```

### 202 Accepted

- 요청을 수신했지만 그에 응하여 행동할 수 없음을 의미합니다.
- 다른 프로세서에서 처리를 하거나 서버가 요청을 다루고 있거나 배치 프로세스를 하고 있는 경우에 의해 만들어진 Status 입니다.
- 응답 본문에 요청에 대한 상태와 요청의 처리가 언제 완료될 것인지에 대한 정보를 포함해야 합니다.

```json
POST /my-batch-process HTTP/1.1
Content-Type: application/json

...

HTTP/1.1 202 Accepted
Link: </batch-status/5545> rel="http://example.org/batch-status"
Content-Length: 0
```

### 203 Non-Authoritative Information

- 클라이언트와 서버 사이에 프록시에서 사용할 수 있는 상태코드
- 프록시가 형식을 변환하거나 HTML 본문에 무언가를 추가할 경우 프록시가 위의 상태코드로 변경하여 클라이언트에게 내려줍니다.
- 이때 기존의 상태 코드를 확인할 수 없는 문제가 발생하기 떄문에 RFC에서 Warning 헤더를 사용할 것을 권장합니다.

```json
HTTP/1.1 203 Non-Authoritative Information
Content-Type: text/plain
Content-Length: 515

...
```

```json
HTTP/1.1 200 OK
Content-Type: text/plain
Warning: 214 proxy.example.org "We censored the response. srry"
Content-Length: 515

...
```

### 204 No Content

- 요청은 성공적이였으며, 응답 본문에 보낼 컨텍츠가 없는 의미입니다.
- DELETE 요청 후 더 이상 콘텐츠가 없다는 의미로 받아드릴 수도 있지만, 응답 본문이 없는 다른 요청에서도 사용이 가능합니다.

```json
HTTP/1.1 204 No Content
```

### 205 Reset Content

- 요청이 끝난 후 문서 뷰를 리셋하라는 의미입니다.

```json
HTTP/1.1 205 Reset Content
Server: foo-bar/1.2
Connection: close
```

### 206 Partial Content

- 클라이언트에서 복수의 스트림을 분할 다운로드를 하고자 `Range` 헤더를 이용해 전송합니다.
- 그렇다면 특정 범위만 전달한다는 의미입니다.

```json
GET /video.mp4 HTTP/1.1
Range: bytes=1048576-2097152

HTTP/1.1 206 Partial Content
Content-Range: bytes 1048576-2097152/3145728
Content-Type: video/mp4

...
```

### 207 Multi-Status(WebDAV)

- 여러 리소스가 여러 상태 코드인 상황에 사용합니다.

```json
HTTP/1.1 207 Multi-Status
Content-Type: application/xml; charset="utf-8"
Content-Length: 1241

<?xml version="1.0" encoding="utf-8" ?>
<D:multistatus xmlns:D="DAV:">
  <D:response>
    <D:href>http://www.example.com/Coll/</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Loop Demo</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf8</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>http://www.example.com/Coll/Foo</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Bird Inventory</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf9</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>http://www.example.com/Coll/Bar</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Loop Demo</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf8</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 208 Already Reported</D:status>
    </D:propstat>
  </D:response>
</D:multistatus>
```

### 208 Already Reported(WebDev)

- prostat(property와 stauts의 합성어) 응답 속성으로 동일 컬렉션으로 바인드된 복수의 내부 멤버를 반복적으로 열거하는 것을 피하기 위해 사용
- 따로 응답 코드에 나타나지 않고 body에 나오게 된다.

```json
HTTP/1.1 207 Multi-Status
Content-Type: application/xml; charset="utf-8"
Content-Length: 1241

<?xml version="1.0" encoding="utf-8" ?>
<D:multistatus xmlns:D="DAV:">
  <D:response>
    <D:href>http://www.example.com/Coll/</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Loop Demo</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf8</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>http://www.example.com/Coll/Foo</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Bird Inventory</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf9</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
  </D:response>
  <D:response>
    <D:href>http://www.example.com/Coll/Bar</D:href>
    <D:propstat>
      <D:prop>
        <D:displayname>Loop Demo</D:displayname>
        <D:resource-id>
          <D:href>urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf8</D:href>
        </D:resource-id>
      </D:prop>
      <D:status>HTTP/1.1 208 Already Reported</D:status>
    </D:propstat>
  </D:response>
</D:multistatus>
```

### 226 IM Used (HTTP Delta encoding)

- 서버가 GET 요청에 대한 의무를  다 했고, 그리고 응답이 하나 또는 그 이상의 인스턴스 조작이 현재 인스턴스에 적용되었음을 알려준다.
- IM은 Instance Manipulation
- 클라이언트는 특정 리소스에 대해 캐시하게 된다. 이 때 리소스가 서버 측에서 변경되면 전체 리소스를 클라이언트에게 보내줘야 하는데 약간의 리소스 변경에 대해 전체를 다시 보내는 것은 낭비임으로 이 상태코드를 통해 서버가 변경 사항만 다시 보낼 수 있습니다.

```json
GET /foo.html HTTP/1.1
Host: bar.example.net
If-None-Match: "123xyz"
A-IM: vcdiff, diffe, gzip

HTTP/1.1 226 IM Used
ETag: "489uhw"
IM: vcdiff
Date: Tue, 25 Nov 1997 18:30:05 GMT
Cache-Control: no-store, im, max-age=30

...
```

## Redirection 3xx

- 클라이언트에게 리소스에 대해 다른 위치를 사용하라고 알려주거나, 리소스 내용 대신 대안을 알려주는 응답을 보낼때 사용합니다.

### 300 Multiple Choices

- 클라이언트가 동시에 여러 리소스를 가리키는 URL을 요청한 경우 그 리소스의 목록과 함께 반환한다.
- Link 헤더를 통해 선택할 리소스를 줍니다.
- 서버는 Location 헤더에 선호하는 URL을 포함시킨다

```json
HTTP/1.1 300 Multiple Choices
Server: curveball/0.3.1
Access-Control-Allow-Headers: Content-Type,User-Agent
Access-Control-Allow-Origin: *
Link: </foo> rel="alternate"
Link: </bar> rel="alternate"
Content-Type: text/html
Location: /foo
```

### 301 Moved Permanently

- 요청된 리소스가 Location 헤더가 지정한 URL로 확실하게 이동되었음을 나타냅니다.
- 브라우저는 이 페이지로 리다렉션 되고 검색 엔진은 그들의 자원 링크를 업데이트 합니다.
- 따라서 GET, HEAD 방법에 대한 응답만 사용하며 POST는 308을 사용하는 것이 좋다.

```json
HTTP/1.1 301 Moved Permanently
Server: Curveball/0.4.2
Location: https://evertpot.com/http/301-moved-permanently
```

### 302 Found

- 요청된 리소스가 Location 헤더가 지정한 URL로 일시적으로 이동되었음을 나타낸다.
- 브라우저는 이 페이지로 리다렉션 된다.
- 따라서 GET, HEAD 방법에 대한 응답만 설정하는 것이 좋다.

```json
GET / HTTP/1.1
Host: www.example.com
```

```json
HTTP/1.1 302 Found
Location: http://www.iana.org/index.html
```

### 303 See Other

- 클라이언트에게 리소스를 다른 URL에서 가져와야 한다고 말할때 사용한다.
- Location 헤더를 이용해 리소스 위치를 알려준다.
- POST Or PUT의 응답으로 클라이언트에게 리소스 위치를 알려준다.

```json
HTTP/1.1 303 See Other
Server: Curveball/0.4.0
Location: /confirmation
```

### 304 Not Modified

- 캐시된 자원의 응답으로 요청한 리소스가 최근에 수정된 일이 없다면 리소스가 수정되지 않았음을 의미한다.
- If-Modified-Since 헤더를 전송한다.

```json
GET /foo HTTP/1.1
Accept: text/html
```

```json
HTTP/1.1 200 Ok
Content-Type: text/html
ETag: "some-string"
```

- 처음에는 해당 리소스를 보내주고 ETag를 전송한다.

```json
GET /foo HTTP/1.1
Accept: text/html
If-None-Match: "some-string"
```

- 다음요청에서 ETag를 포함해 보내게 되면 아래와 같이 304를 보내준다.

```json
HTTP/1.1 304 Not Modified
ETag: "some-string"
```

### 305 Use Proxy

- 서버는 클라이언트에게 요청한 응답은 반드시 프록시를 통해 접속해야 함을 의미합니다.
- 현재는 보안 문제로 사용하지 않습니다.

```json
HTTP/1.1 305 Use Proxy
Location: https://proxy.example.org:8080/
```

### 306 Switch Proxy

- 클라이언트가 이미 프록시를 사용한 경우 새로운 프록시를 통해 접속함을 의미합니다.
- 현재는 보안상 이유로 사용하지 않습니다.

```json
HTTP/1.1 306 Switch Proxy
Set-Proxy: SET; proxyURI="https://proxy.example.org:8080/" scope="http://", seconds=100
```

### 307 Temporary Redirect

- 클라이언트가 요청한 리소스가 다른 URI에 있으며, 이전 요청과 동일한 메소드를 사용하여 요청해야함을 의미한다.
- 302와 동일하지만 307은 사용자가 반드시 사용된 HTTP 메서드를 변경하면 안된다.
- 예를 들어 첫 요청에 POST를 사용했다면 다음 요청도 POST를 사용해야 한다.

```json
HTTP/1.1 307 Temporary Redirect
Server: gws
Location: https://evertpot.com/http/307-temporary-redirect
```

### 308 Permanent Redirect

- 클라이언트가 요청한 리소스가 영구적으로 다른 URI에 있으며 이전 요청과 동일한 메소드를 사용해야 한다.
- 301과 똑같지만 사용자가 반드시 HTTP 메서드를 변경하지 말아야 한다.
- 예를 들어 첫 요청에 POST를 사용했다면 다음 요청도 POST를 사용해야 한다.

```json
HTTP/1.1 308 Permanent Redirect
Location: https://evertpot.com/http/308-permanent-redirect
Server: Apache/2.4.29
```

## Client Error 4xx

- 클라이언트의 요청이 잘못된 경우에 보내줍니다.

### 400 Bad Request

- 클라이언트가 잘못된 요청을 보낸 경우 사용합니다.
- 본문에 어떤 잘못된 요청에 대한 이유를 같이 적어줍니다.

```json
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json

{
  "type": "https://example.org/impolite",
  "title": "Request was not polite enough",
  "detail": "HTTP requests must be made using a 'Please' HTTP header.
}
```

### 401 Unauthorized

- 리소스를 얻기 전에 인증이 필요하다는 의미입니다.
- `www-Authenticate` 헤더를 통해 인정 체계를 알려줘야 합니다.

```json
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer
```

### 402 Payment Required

- 미래의 사용을 위해 예약되어 있습니다.
- 원래의 의미는 상업용 웹 사이트와 API가 사용자가 서비스 비용을 지불 한 후 HTTP 요청이 반복 될 수 있음을 알리는 것이였다.
- 그러나 현재는 사용하고 있지 않습니다.

### 403 Forbidden

- 요청이 서버에 의해 거부되었음을 알려준다.
- 본문에 요청이 왜 거부되었는지 적어줍니다.
- 그러나 필수는 아니다. 보통 서버가 거절 이유를 숨기고 싶을 떄 사용합니다.

```json
HTTP/1.1 403 Forbidden
Content-Type: text/html
Content-Length: 32

<h1>403: You can't do that!</h1>
```

### 404 Not Found

- 존재하지 않는 URL을 사용할때 사용합니다.

```json
HTTP/1.1 404 Not Found
Content-Type: text/html

<h1>404 Not found</h1>
```

### 405 Method Not Allowed

- 특정 HTTP 메서드가 리소스에서 지원되지 않는 경우 사용합니다.
- `Allow` 헤더를 이용해 허용하는 메서드를 알려줍니다.

```json
HTTP/1.1 405 Method Not Allowed
Content-Type: text/html
Allow: GET, HEAD, OPTIONS, PUT

<h1>405 Try another method!</h1
```

### 406 Not Acceptable

- 클라이언트는 어떤 종류의 엔티티를 응답받을지를 헤더를 통해 알려줍니다.
    - `Accept`
    - `Accept-Charset`
    - `Accept-Encoding`
    - `Accept-Language`
- 이때 URL의 응답 엔티티가 클라이언트가 받아들일 수 없는 리소스일 경우에 사용합니다.
- 아래와 같이 프랑스어로 수신하는 것을 선어하는데, 프랑스어가 아니라 한국어 일때 이와 같은 상태코드를 내려줍니다.

```json
GET /foo HTTP/1.1
Accept: application/json
Accept-Language: fr-CA; q=1, fr; q=0.8
```

```json
HTTP/1.1 406 Not Acceptable
Server: curveball/0.4
Content-Type: text/html

<h1>Je ne support pas application/json</h1>
```

### 407 Proxy Authentication Required

- 401 상태 코드와 같으나 인증을 요구하는 프록시 서버를 사용하라는 의미입니다.

```json
HTTP/1.1 407 Proxy Authentication Required
Proxy-Authenticate: Basic; realm="Secured area"
```

```json
GET / HTTP/1.1
Proxy-Authorization: Basic d2VsbCBkb25lOllvdSBmb3VuZCB0aGUgc2Vjb25kIGVhc3RlciBlZ2cK
```

### 408 Request Timeout

- 요청을 끝내기에 시간이 많이 걸리는 경우 사용합니다.
- 이것은 서버가 연결을 끊기로 결정했기 때문에 응답에 닫기 연결 헤더 필드를 전송해야 합니다.

```json
HTTP/1.1 408 Request Timeout
Connection: close
Content-Type: text/plain

Too slow! Try again
```

### 409 Conflict

- 요청이 충돌이 일으킬 염려가 있다고 생각할 때 이 요청을 보낼 수 있다.
- 응답은 충돌에 대한 설명을 ㅈ거어줘야 한다.
- PUT 요청에 대해 발생할 가능성이 높다.
- 예를 들어 서버에 이미 있는 파일보다 오래된 파일을 업로드 할 때 버전 제어 충돌이 발생하는 경우 발생한다.

```json
HTTP/1.1 409 Conflict
Content-Type: text/plain

You tried to upload a file to a folder that doesn't exist. Create the folder
and try again!
```

### 410 Gone

- 404와 비슷하나, 이미 서버가 리소스를 갖고 있다는 점과 다르다.
- 주로 웹사이트를 유지보수 하면서 서버 관리자가 클라이언트에게 리소스가 제거된 경우 이를 알려주기 위해 사용

```json
HTTP/1.1 410 Gone
Content-Type: text/plain
Server: curveball/0.6.0

I deleted it and it's never coming back!
```

### 411 Length Requried

- 서버 요청 메시지에 ``Content-Length` 헤더를 요구할 때 사용한다.

```json
HTTP/1.1 411 Length Required
Content-Type: text/html
Server: curveball/0.6.0

<h1>This server requires a Content-Length</h1>
```

### 412 Precondition Failed

- 클라이언트가 조건부 요청을 했는데 그 중 하나가 실패했을 떄 사용한다.
- 이는 If-Unmodified-from 또는 If-None-Match 헤더에 의해 정의된 조건이 충족되지 않을 때 GET 또는 HEAD 이외의 방법에 대한 조건부 요청에서 발생한다.

```
PUT /foo/new-article.md HTTP/1.1
Content-Type: text/markdown
If-None-Match: *
```

```
PUT /foo/old-article.md HTTP/1.1
If-Match: "1345-12315"
Content-Type: text/markdown
```

```
PUT /foo/old-article.md HTTP/1.1
If-Match: "1345-12315"
Content-Type: text/markdown
Prefer: return=representation

### Article version 2.1
```

```
HTTP/1.1 412 Precondition Failed
Content-Type: text/markdown
Etag: "4444-12345"
Vary: Prefer

### Article version 3.0
```

### 413 Payload Too Large

- Request Entity Too Large
- 서버가 처리할 수 있는 혹은 처리하고자 하는 한계를 넘은 크기의 요청을 클라가 보냈을 때 사용한다.
- 서버가 연결을 닫거나 `Retry-After` 헤더를 반환해 특정 시간 후에 시도해야 함을 반환할 수 있다.
- 이때 디스크 공간이 부족해 에러를 발생시키는 경우 `507 Insufficient Storage` 를 대신 사용해야 합니다.

```json
HTTP/1.1 413 Payload Too Large
Retry-After: 3600
Content-Type: text/html

<p>You exceeded your quota. Try again in an hour</p>
```

### 414 URI Too Long

- 서버가 처리할 수 있는 혹은 처리하고자 하는 한계를 넘은 길이의 요청 URL이 포함된 요청을 클라가 보냈을 때 사용한다.
- HTTP 요청의 URI의 길이는 제한이 없지만, 브라우저에서는 한계가 있다. 따라서 URI 길이를 제한하는 것이 좋습니다. (브라우저에 맞춰 2000byte를 안넘는 것이 좋습니다.)

```
HTTP/1.1 414 URI Too Long
Content-Type: text/html

<p>Insufficient level of conciseness in request</p>
```

### 415 Unsupported Media Type

- 서버가 이해하거나 지원하지 못하는 내용 유형의 엔터티를 클라가 보냈을 때 사용한다.
- ``Content-Type` 헤더를 검사를 해 지원하지 않는 경우에 많이 사용합니다.

```
POST /new-article HTTP/1.1
Content-Type: text/html

<h1>Another day, another blog post</h1>

HTTP/1.1 415 Unsupported Media Type
Content-Type: application/json

{"error": "This endpoint only supports text/markdown for new articles"}
```

### 416 Range Not Satisfiabe

- 요청 메세지가 리소스의 특정 범위를 요청했는데,그 범위가 잘못되었거나 맞지 않을 때 사용한다.

```
GET /foo.md HTTP/1.1
Range: bytes=1-2000
```

```
HTTP/1.1 416 Range Not Satisfiable
Content-Range: bytes */1000
```

### 417 Expectation Failed

- 요청에 포함된 Expect 요청 헤더에 서버가 지원하지 않는 것이 담겨있는 경우 사용한다.

```
POST /foo/bar HTTP/1.1
Content-Type: application/gzip
Content-Length: 12345678765
Expect: 100-continue

...

HTTP/1.1 417 Expectation Failed
Content-Type: text/plain

We don't support 100-continue
```

### 418 I'm a teapot

- 서버가 찻주전자이기 때문에 커피 내리기를 거절했다는 것을 의미합니다.
- 이 오류는 1998년 만우절 농담이었던 하이퍼 텍스트 커피 포트 제어 규약(Hyper Text Coffee Pot Control Protocol)의 레퍼런스입니다.

### 420 Enhance your calm

- 트위터에서 사용하는 비공식적인 에러코드입니다.
- 속도가 제한되고 있음을 알리는 상태코드입니다.

### 421 Misdirected Request

- 클라이언트에게 잘못된 서버에 연결되었음을 의미합니다.
- 에를들어 HTTP/1.1 이후 클라이언트는 Host 헤더를 포함해야 하지만, 서버와 다르다면 잘못된 서버와 연결되었다는 것을 알려줄 수 있습니다.

```
GET /contact.html HTTP/1.1
Host: foo.example.org

HTTP/1.1 421 Misdirected Request
Content-Type: text/html

<h1>Switchboard operator error</h1>
```

### 422 Unprocessable Entity

- 요청은 정상이지만, 문법 오류로 인해 요청을 처리할 수 없을때 사용합니다.
- 예를들어 아래와 같은 요청이 들어왔다고 가정해봅시다.

```
POST /new-article HTTP/1.1
Content-Type: application/json

{ "title": "Hello world!"}
```

- 이때 json-schema에 의해 문법이 오류(유효성 검사)를 통과하지 못했을 경우 이 상태코드를 사용합니다.

```
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/problem+json

{
  "type" : "https://example/errors/missing-property",
  "status": 422,
  "title": "Missing property: body"
}
```

### 423 Locked

- 리소스에 접근을 잠가놓았는 의미입니다.
- 예를들어 사용자가 특정 파일과 디렉토리를 잠금해 다른 사람이 편집을 못하도록 해놨을 때 요청을 하면 아래와 같은 에러를 발생시킵니다.

```
LOCK /article/5 HTTP/1.1
Content-Type: application/xml

<?xml version="1.0"?>
<d:lockinfo xmlns:d='DAV:'>
 <d:lockscope><d:exclusive/></d:lockscope>
 <d:locktype><d:write/></d:locktype>
 <d:owner>Evert</d:owner>
</d:lockinfo>
```

```
HTTP/1.1 200 OK
Content-Type: application/xml
Lock-Token: <urn:uuid:e71d4fae-5dec-22d6-fea5-00a0c91e6be4>

<?xml version="1.0"?>
...
```

```
PUT /article/5 HTTP/1.1
Content-Type: text/plain

New content
```

```
HTTP/1.1 423 Locked
Content-Type: application/xml

<?xml version="1.0" encoding="utf-8" ?>
<d:error xmlns:d="DAV:">
  <d:lock-token-submitted>
  <d:href>/article/5</d:href>
  </d:lock-token-submitted>
</d:error>
```

- 그러나 Ìf` 헤더를 이용해 lock-toekn을 포함한다면 수정이 가능합니다.

```
PUT /article/5 HTTP/1.1
Content-Type: text/plain
If: <urn:uuid:e71d4fae-5dec-22d6-fea5-00a0c91e6be4>

New content

HTTP/1.1 204 No Content
```

### 424 Failed Dependency

- PROPPATCH 메서드를 사용해 단일 요청으로 많은 항목을 업데이트를 할 수 있습니다. 이때 하나라도 실패한다면 전부 실패로 돌아갈때 사용합니다.

```
PROPPATCH /folder HTTP/1.1
Host: www.example.org
Content-Type: application/xml

<?xml version="1.0"?>
<d:propertyupdate xmlns:d="DAV:">
 <d:set>
   <d:prop>
      <d:getcontentlength>1</d:getcontentlength>
      <d:displayname>Evert</d:displayname>
   </d:prop>
 </d:set>
</d:propertyupdate>
```

```
HTTP/1.1 207 Multi-Status
Content-Type: application/xml
Content-Length: xxxx

<?xml version="1.0"?>
<d:multistatus xmlns:d="DAV:">
 <d:response>
   <d:href>/folders</d:href>
   <d:propstat>
     <d:prop><d:displayname/></d:prop>
     <d:status>HTTP/1.1 424 Failed Dependency</d:status>
   </d:propstat>
   <d:propstat>
     <d:prop><d:getcontentlength /></d:prop>
     <d:status>HTTP/1.1 403 Forbidden</d:status>
   </d:propstat>
 </d:response>
</d:multistatus>
```

### 425 Too Early

- 서버가 재생될 수 있는 요청을 처리하는 것을 원치 않음을 나타내며, 이는 재생 공격의 가능성을 만든다.

### 426 Upgrade Required

- 지금은 프로토콜 사용이 불가하지만, 프로토콜을 업그레이드 하라는 의미입니다.

```
HTTP/1.1 426 Upgrade Required
Upgrade: HTTP/3
Connection: Upgrade

To use this service, you must use HTTP version 3.
```

### 428 Precondition Required

- 여러 사용자가 같은 자원을 작성해 변경 내용을 덮어 쓰는걸 방지하기 위해 사용한다.
- If-Match, If-None-Match, If-Modified-Since및 If-Unmodified-Since 헤더를 사용해야 합니다.

### 429 Too Many Requests

- 사용자가 지정된 시간 동안 너무 많은 요청을 보냈음을 나타낸다.
- 재시도 후 헤더가 이 응답에 포함될 수 있으며, 이는 새 요청을 하기 전에 대기할 시간을 나타낼 수 있다.
- `Retry-After` 헤더를 이용해 재 요청 시간을 명시할수 있습니다.

```
HTTP/1.1 429 Too Many Requests
Content-Type: text/plain
Retry-After: 3600

You exceeded the limit. Try again in an hour
```

### 431 Request Header Fields Too Large

- 서버가 헤더 필드가 너무 커서 요청을 처리하지 않음을 나타낸다.요청 헤더 필드의 크기를 줄인 후 요청을 다시 제출할 수 있다.
- 총 요청 헤더 필드 수가 너무 많은 경우 또는 단일 헤더 필드가 너무 큰 경우 사용할 수 있다.
- 이 오류는 잘 테스트된 프로덕션 시스템에서 발생해서는 안 되지만,새로운 시스템을 테스트하는 동안 더 자주 발견될 수 있다.

```
HTTP/1.1 431 Request Headers Too Large
Content-Type text/html

<h1>Too many cookies! Try to reduce your cookie footprint.</h1>
```

### 451 Unavailable For Legal Reasons

- 법적 이유로 사용할 수 없는 클라이언트 오류 응답 코드는 사용자가 법적 조치가 발행된 웹 페이지와 같이법적 이유로 사용할 수 없는 리소스를 요청했음을 나타낸다.

```
HTTP/1.1 451 Unavailable For Legal Reasons
Link: <https://proxy.example.org/legal>; rel="blocked-by"
Content-Type text/html

<h1>Government policy prohibits you from reading this information.</h1>
```

## Server Error 5xx

- 클라이언트가 올바른 요청을 보냈음에도 서버 자체에서 에러가 발생하는 경우가 있다.
- 클라이언트가 서버의 제한에 걸린 것일 수도 있고,혹은 게이트웨이 리소스와 같은 서버의 보조 구성요소에서 발생한 에러일 수도 있다.

### 500 Internal Server Error

- 서버가 요청을 처리할 수 없게 만드는 에러를 만났을 때 사용한다.
- 일반적인 오류로 구체적인 오류가 없을 때 사용합니다.

```
HTTP/1.1 500 Internal Server Error
Content-Type text/plain

We broke it, sorry!
```

### 501 Not Implemented

- 클라이언트가 서버가 처리하지 못한 요청을 하는 경우 사용합니다. 예는 지원하지 않는 메서드를 요청할때 입니다.
- 405와 유사하지만, 클라이언트의 잘못이라고 한다면 501은 서버가 지원해야 하는 메서드를 구현하지 않음을 의미합니다.

```
HTTP/1.1 501 Not Implemented
Content-Type text/html

<img src="http://geocities.example/work-in-progress.gif" />
```

### 502 Bad Gateway

- 서버와 클라이언트 사이에 프록시는 정상적으로 동작하나, 서버에 문제가 잇음을 나타냅니다.

```
HTTP/1.1 502 Bad Gateway
Content-Type text/plain

We made a HTTP request to an origin server, but we got a Gopher response back.
```

### 503 Service Unavailable

- 서버가 요청을 처리해 줄 수 없지만, 나중에 가능함을 의미하고자 할대 사용한다.
- 서버가 언제 그 리소스를 사용할 수 있게 될지 알고 있다면, 서버는 Retry-After 헤더를 응답에 포함시킬 수 있다.

```
HTTP/1.1 503 Service Unavailable
Content-Type text/plain
Retry-After: 1800

System overload! Give us some time to increase capacity.
```

### 504 Gateway Timeout

- 408과 비슷하지만,다른 서버에게 요청을 보내고 응답을 타임아웃이 발생한 게이트웨이나 프락시에서 온 응답이라는 점이 다르다.

```
HTTP/1.1 504 Gateway Timeout
Content-Type text/html

<h1>Timeout</h1>

<p>We did not get a timely response from our upstream application server :(</p>
```

### 505 HTTP Version Not Supported

- 서버가 지원할 수 없거나, 지원하지 않으려고 하는 버전의 프로토콜로 된 요청을 받았을 때 사용한다
- 몇몇 서버 어플리케이션들은 오래된 버전의 프로토콜을 지원하지 않는 것을 택한다.

```
GET / HTTP/4.0
```

```
HTTP/1.1 505 HTTP Version Not Supported
Server: cloudflare
Date: Tue, 30 Jul 2019 15:23:35 GMT
Content-Type: text/html
Content-Length: 201
Connection: close
CF-RAY: -

<html>
<head><title>505 HTTP Version Not Supported</title></head>
<body bgcolor="white">
<center><h1>505 HTTP Version Not Supported</h1></center>
<hr><center>cloudflare</center>
</body>
</html>
```

### 507 Insufficient Storage

- 서버에 디스크 공간이 부족할때 사용합니다.

```
HTTP/1.1 507 Insufficient Storage
Content-Type: text/plain

Wow, that's a big file. Can you store it somewhere else? We're pretty cramped
here.
```

### 508 Loop Detected

- 서버가 요청을 처리하는 동안 무한루프에 빠짐을 의미합니다.

```
HTTP/1.1 508 Loop Detected 
Content-Type: text/plain

There was a loop detected in the directory tree, which means that we're not
able to return a full directory tree.
```

### 510 Not Extended

- 요청에 대한 추가 확장이 필요하다는 의미로 400 에러에 적합하다는 이유로 현재는 사용하지 않습니다.

### 511 Network Authentication Required

- 클라이언트가 네트워크 액세스를 얻으려면 **인증이 필요하다는 것**을 나타낸다.
- 이 상태는 원 서버에서 생성되는 것이 아니라네트워크에 대한 액세스를 제어하는 프록시를 가로채서 생성된다.
- 네트워크 운영자들은 때때로 접근을 허가하기 전에약간의 인증, 용어의 수락 또는 다른 사용자 상호 작용을 요구한다(예: 인터넷 카페나 공항에서).
- 그들은 종종 자신의 MAC(Media Access Control) 주소를 사용하여 그렇게 하지 않은 클라이언트를 식별한다.

```
HTTP/1.1 511 Network Authentication Required
Content-Type: text/html

<html>
  <head>
     <title>Network Authentication Required</title>
     <meta http-equiv="refresh"
           content="0; url=https://login.example.net/">
  </head>
  <body>
     <p>You need to <a href="https://login.example.net/">
     authenticate with the local network</a> in order to gain
     access.</p>
  </body>
</html>
```

## 결론

- 제대로된 코드를 서버에서 내려주면서 클라이언트간의 소통을 효율적으로 하기 위한 방법입니다.
- 현재 사용하지 않는 상태코드도 있으니, 서버 입장에서는 올바른 코드를 내려줘야 합니다.
- 장황하게 정리한것 같아 나중에 다시 필요한 상태코드만 정리하는 시간을 또 가져야겠습니다.

## 참고자료

[[WEB] HTTP 상태코드를 알아보자](https://ssungkang.tistory.com/entry/WEB-HTTP-%EC%83%81%ED%83%9C%EC%BD%94%EB%93%9C%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90)

[RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616#section-10)

[HTTP 상태 코드](https://developer.mozilla.org/ko/docs/Web/HTTP/Status)

[RFC 2518 - HTTP Extensions for Distributed Authoring -- WEBDAV](https://tools.ietf.org/html/rfc2518#page-59)

[RFC 8297 - An HTTP Status Code for Indicating Hints](https://tools.ietf.org/html/rfc8297)

[102 Processing](https://evertpot.com/http/102-processing)
