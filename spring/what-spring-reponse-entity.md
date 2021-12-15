# Spring의 ResponseEntity에 대해 알아보자

> 해당 글은 Java 11과 SpringBoot 2.6.1 버전으로 작성되었습니다.
> 

## ResponseEntity란?

- Spring 3.0.2 부터 등장한 HttpStatus 상태 코드를 추가하는 HttpEntity의 확장으로 RestTemplate과 @Controller 메서드에서 사용됩니다.
- RestTemplate에서는 `getForEntity()` 와 `exchange()` 에 의해 반환됩니다.
- 빌더 형식과 객체 생성 둘다 사용이 가능합니다.

```java
return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED);

return ResponseEntity.created(location)
	.header("MyResponseHeader", "MyValue")
	.body("Hello World");

```

- 기본적으로 Body, Headers, Status 코드를 기준으로 작성을 한다.
- 객체로 생성 시 Status 코드는 필수 값이다.
- 제네릭을 사용해서 Body의 타입을 지정할 수 있습니다.

## 몇가지 메서드를 살펴보자.

### toString()

```java
public String toString() {
		StringBuilder builder = new StringBuilder("<");
		builder.append(this.status);
		if (this.status instanceof HttpStatus) {
			builder.append(' ');
			builder.append(((HttpStatus) this.status).getReasonPhrase());
		}
		builder.append(',');
		T body = getBody();
		HttpHeaders headers = getHeaders();
		if (body != null) {
			builder.append(body);
			builder.append(',');
		}
		builder.append(headers);
		builder.append('>');
		return builder.toString();
	}
```

status, body, headers 순으로 출력합니다.

```java
public static void main(String[] args) {
	MultiValueMap<String, String> headers = new LinkedMultiValueMap<>();
	headers.add("location", "1");
	ResponseEntity<String> body = new ResponseEntity<String>("body", headers, 200);
	System.out.println(body); // <200,body,[location:"1"]>
}
```

### ok()

```java
public static BodyBuilder ok() {
		return status(HttpStatus.OK);
}

public static <T> ResponseEntity<T> ok(@Nullable T body) {
		return ok().body(body);
}
```

- 200 OK status 코드를 반환하는 빌더 메서드로 body를 넣을수도 있고 안넣을수도 있다.
- body를 넣을때에는 Null을 사용하면 안된다.

### of()

```java
public static <T> ResponseEntity<T> of(Optional<T> body) {
	Assert.notNull(body, "Body must not be null");
	return body.map(ResponseEntity::ok).orElseGet(() -> notFound().build());
}
```

- 주어진 본인을 옵셔널로 받아 비어있는 경우에는 NotFound 상태로 반환하며, body가 있을 경우 OK status로 내려줍니다.

### 기타 status

```java
public static BodyBuilder created(URI location) {
		return status(HttpStatus.CREATED).location(location);
}

public static BodyBuilder accepted() {
	return status(HttpStatus.ACCEPTED);
}

public static HeadersBuilder<?> noContent() {
	return status(HttpStatus.NO_CONTENT);
}

public static BodyBuilder badRequest() {
	return status(HttpStatus.BAD_REQUEST);
}

public static HeadersBuilder<?> notFound() {
	return status(HttpStatus.NOT_FOUND);
}

public static BodyBuilder unprocessableEntity() {
	return status(HttpStatus.UNPROCESSABLE_ENTITY);
}

public static BodyBuilder internalServerError() {
	return status(HttpStatus.INTERNAL_SERVER_ERROR);
}
```

자주 사용하는 stauts에 대해서는 빌더로 생성이 가능합니다.

## Headers값은 빌더로 했을때 어떻게 넣을 수 있는가?

HeaderBuilder도 지원해주게 됩니다.

```java
public interface HeadersBuilder<B extends HeadersBuilder<B>> {

		B header(String headerName, String... headerValues);

		B headers(@Nullable HttpHeaders headers);

		B headers(Consumer<HttpHeaders> headersConsumer);

		B allow(HttpMethod... allowedMethods);

		B eTag(String etag);

		B lastModified(ZonedDateTime lastModified);

		B lastModified(Instant lastModified);

		B lastModified(long lastModified);

		B location(URI location);

		B cacheControl(CacheControl cacheControl);

		B varyBy(String... requestHeaders);

		<T> ResponseEntity<T> build();
	}
```

지원하는 Header들은 위의 코드를 보면 바로 이해가 가능합니다.

## 바디의 정보는 어떻게 넣을 수 있는가?

```java
public interface BodyBuilder extends HeadersBuilder<BodyBuilder> {

		BodyBuilder contentLength(long contentLength);

		BodyBuilder contentType(MediaType contentType);

		<T> ResponseEntity<T> body(@Nullable T body);
	}
```

기본적으로 BodyBuilder를 통해 contentLength와 contentType의 값을 넣을 수 있습니다.

## 결론

REST API를 Spring으로 구현 할 때 응답에 대해 쉽게 작성할 수 있고 직관적으로 작성할 수 있는 ResponseEntity를 제공해줌으로써, 편하게 구현할 수 있다는걸 느끼게 되었습니다.
