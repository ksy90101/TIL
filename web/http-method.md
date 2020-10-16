# HTTP Method

## HTTP 메소드란?

> The Method token indicates the method to be performed on the
resource identified by the Request-URI. The method is case-sensitive.

- 클라이언트가 웹서버에서 사용자 요청의 목적 및 종류를 알리는 수단이다.
- 즉, 자원의 행위를 표현하는 것이다.
- 요청 메시지의 첫째줄에 위치한다.

> Request-Line = Method SP Request-URI SP HTTP-Version CRLF

## GET

- 서버에게 해당 리소스를 조회할 때 사용
- 즉, 서버의 Resource를 읽는다고 생각하면 될거 같습니다

![http-method-1](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-1.png?raw=true)

```json
GET /users/1

HTTP/1.1 200 OK
{
	"name": "rutgo",
	"age": 28
}
```

## POST

- 리소스를 생성할때 사용

![http-method-2](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-2.png?raw=true)

```json
POST /users
{
	"name": "rutgo",
	"age": 28
}

HTTP/1.1 201 CREATED
```

## PUT

- 해당 리소스를 수정할때 사용합니다.
- REST API 상으로는 해당 자원의 전체를 교체하는 의미로 사용한다.

![http-method-3](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-3.png?raw=true)

```json
PUT /users/1
{
	"name": "rutgo",
	"age": 29
}

HTTP/1.1 200 OK
{
	"name": "rutgo",
	"age": 29
}
```

## DELETE

- 리소스를 삭제할때 사용합니다.

![http-method-4](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-4.png?raw=true)

```json
DELETE users/1

HTTP/1.1 204 No Content
```

## PATCH

- REST API상 리소스의 일부를 변경한다는 의미로 사용한다.
- 그러나 이 명세는 HTTP 1.1에서는 존재하지 않습니다. 따라서 그 이후에 나온 것 이기 때문에 지원하는지 확인하고 사용해야 합니다.

```json
PATCH /users/1
{
	"age": 29
}

HTTP/1.1 200 OK
{
	"name": "rutgo",
	"age": 29
}
```

## REST API HTTP METHOD CRUD

![http-method-5](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-5.png?raw=true)

## HEAD

- GET메서드의 요청과 동일한 응답을 요구하지만, 응답 본문을 포함하지 않습니다.
    - 헤더만 확인하고 싶을때
    - 리소스를 받지 않고 오직 찾기만 원할때
    - 데이터가 존재할 경우 응답 상태 코드를 확인할 때
    - 서버의 응답 헤더를 보면서 리소스가 수정되어있는지 확인한다. (헬스 체크, 버전 확인, 최종 수정 일자 등등)

```json
HEAD /index.html

HTTP/1.1 200 OK
Content-Length: 6623
Content-Type: text/html; Charset=utf-8

```

## CONNECT

- 클라이언트가 프록시를 통해서 서버와 SSL 통신을 하고자 할 때 사용한다.

```json
CONNECT ~:443

HTTP/1.0 Connection established
```

## OPTIONS

- 웹 서버에서 지원하는 메소드의 종류를 확인할 경우 사용한다.

```json
OPTIONS * 

HTTP/1.1 200 OK
Allow: GET, POST, PUT, OPTIONS, DELETE
```

## TRACE

- 원격지 서버에 루프백 메시지 호출하기 위해 테스트용으로 사용
- 송신한 요청을 그대로 응답으로 보내줍니다.
- 즉, 클라이언트로 부터 Request Packet이 방화벽, Proxy Server, Gateway등을 거치면서 packet에 변조가 일어날 수 있는데, 서버에 도달 했을 때의 최정 패킷의 요청 패킷을 볼수 있다.
    - 즉, 원본 데이터와 서버의 데이터와 서버의 응답을 통해 비교를 할 수 있다.
- 일반적으로 해당 메소드는 사용불가하다.
- Trace메서드를 사용해 XST(Cross-Site

![http-method-6](https://github.com/ksy90101/TIL/blob/master/web/img/http-method-6.png?raw=true)

## 참고자료

[RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616#section-5.1)

[HTTP 요청 메서드](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods)

[HTTP Method](https://medium.com/@lyhlg0201/http-method-d561b77df7)

[REST API 제대로 알고 사용하기 : TOAST Meetup](https://meetup.toast.com/posts/92)
