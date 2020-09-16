# Spring Boot Base logback

## LogBack이란?

- Log를 효율적으로 관리하기 위해서 사용하는 것입니다.
- 현재 core, classic, access의 세가지 모듈로 나눠져 있다.
- 코어 모듈은 다른 두 개의 모듈을 위한 기반이며, classic 모듈은 코어를 확장하는데 사용하며 access는 서블릿 컨테이너와 통합되어 HTTP-access 로그 기능을 제공한다.
- 스프링에서는 기본적으로 logback을 사용한다.

## base.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Base logback configuration provided for compatibility with Spring Boot 1.1
-->

<included>
	<include resource="org/springframework/boot/logging/logback/defaults.xml" />
	<property name="LOG_FILE" value="${LOG_FILE:-${LOG_PATH:-${LOG_TEMP:-${java.io.tmpdir:-/tmp}}}/spring.log}"/>
	<include resource="org/springframework/boot/logging/logback/console-appender.xml" />
	<include resource="org/springframework/boot/logging/logback/file-appender.xml" />
	<root level="INFO">
		<appender-ref ref="CONSOLE" />
		<appender-ref ref="FILE" />
	</root>
</included>
```

- include는 다른 로그 관련 설정 파일을 불러오기 위해 사용한다.
- property는 값을 변수처럼 사용하기 위해 사용한다.
- 현재 이 파일을 보면 defaults.xml과 console.appender.xml, file-appender.xm의 파일을 불러오고 있으며, 로그레벨 INFO로 설정하여 콘솔과 파일 둘다 사용하게 된다.
- 아울러 파일로 로그를 관리할때 어느 위치에 저장하는지와 spirng.log라는 이름으로 저장되는걸 확인할 수 있다.

## defaults.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Default logback configuration provided for import
-->

<included>
	<conversionRule conversionWord="clr" converterClass="org.springframework.boot.logging.logback.ColorConverter" />
	<conversionRule conversionWord="wex" converterClass="org.springframework.boot.logging.logback.WhitespaceThrowableProxyConverter" />
	<conversionRule conversionWord="wEx" converterClass="org.springframework.boot.logging.logback.ExtendedWhitespaceThrowableProxyConverter" />
	<property name="CONSOLE_LOG_PATTERN" value="${CONSOLE_LOG_PATTERN:-%clr(%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}}){faint} %clr(${LOG_LEVEL_PATTERN:-%5p}) %clr(${PID:- }){magenta} %clr(---){faint} %clr([%15.15t]){faint} %clr(%-40.40logger{39}){cyan} %clr(:){faint} %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>
	<property name="FILE_LOG_PATTERN" value="${FILE_LOG_PATTERN:-%d{${LOG_DATEFORMAT_PATTERN:-yyyy-MM-dd HH:mm:ss.SSS}} ${LOG_LEVEL_PATTERN:-%5p} ${PID:- } --- [%t] %-40.40logger{39} : %m%n${LOG_EXCEPTION_CONVERSION_WORD:-%wEx}}"/>

	<logger name="org.apache.catalina.startup.DigesterFactory" level="ERROR"/>
	<logger name="org.apache.catalina.util.LifecycleBase" level="ERROR"/>
	<logger name="org.apache.coyote.http11.Http11NioProtocol" level="WARN"/>
	<logger name="org.apache.sshd.common.util.SecurityUtils" level="WARN"/>
	<logger name="org.apache.tomcat.util.net.NioSelectorPool" level="WARN"/>
	<logger name="org.eclipse.jetty.util.component.AbstractLifeCycle" level="ERROR"/>
	<logger name="org.hibernate.validator.internal.util.Version" level="WARN"/>
	<logger name="org.springframework.boot.actuate.endpoint.jmx" level="WARN"/>
</included>
```

- 여기서는 콘솔 로그 패턴과 파일 로그 패턴을 정의하고 있으며, 몇몇의 로그들에 대해서 어느 레벨까지 출력을 할 것인지 정하고 있다.
- 위와 같이 정의를 한다면 org.apache.catalina.startup.DigesterFactory의 대한 로그는 에러만 나오게 될것 이다.

## console-appender.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
Console appender logback configuration provided for import, equivalent to the programmatic
initialization performed by Boot
-->

<included>
	<appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
		<encoder>
			<pattern>${CONSOLE_LOG_PATTERN}</pattern>
		</encoder>
	</appender>
</included>
```

- 아주 간단하게 패턴만 입력하여 사용한다.
- 이미 defaults.xml에서 패턴을 정의했기 때문에 우리가 기본적으로 스프링부트를 실행했을 때 보는 로그들은 이걸로 인해 출력되게 된다.

## file-appender.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<!--
File appender logback configuration provided for import, equivalent to the programmatic
initialization performed by Boot
-->

<included>
	<appender name="FILE"
		class="ch.qos.logback.core.rolling.RollingFileAppender">
		<encoder>
			<pattern>${FILE_LOG_PATTERN}</pattern>
		</encoder>
		<file>${LOG_FILE}</file>
		<rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
			<cleanHistoryOnStart>${LOG_FILE_CLEAN_HISTORY_ON_START:-false}</cleanHistoryOnStart>
			<fileNamePattern>${ROLLING_FILE_NAME_PATTERN:-${LOG_FILE}.%d{yyyy-MM-dd}.%i.gz}</fileNamePattern>
			<maxFileSize>${LOG_FILE_MAX_SIZE:-10MB}</maxFileSize>
			<maxHistory>${LOG_FILE_MAX_HISTORY:-7}</maxHistory>
			<totalSizeCap>${LOG_FILE_TOTAL_SIZE_CAP:-0}</totalSizeCap>
		</rollingPolicy>
	</appender>
</included>
```

- 로그 파일에 대한 기본적인 설정들이 들어가 있다.
- application.yml 또는 properties에서 정의하지 않으면 옆에 있는 기본값이 들어가게 된다.

```yaml
logging.file.name:
logging.file.max-history:
...
```

- 위와 같은 key로 설정하면 본인이 설정한 값으로 설정이 가능하다.

## 커스텀 logback에서 사용하는 방법

- 파일과 콘솔 둘다 사용하고 싶다면 아래와 같이 include 해주면 된다.

```xml
<include resource="org/springframework/boot/logging/logback/base.xml" />
```

- 콘솔만 사용하고 싶다면 아래와 같이 include 해주면된다.
- 이때 주의할 점은 아래와 같이 defaults.xml을 먼저 선언해준 다음에 console-appender.xml을 선언해줘야 한다는 것이다.
- defaults.xml에 `CONSOLE_LOG_PATTERN` 이 값이 있다 보니 꼭 필요하다.

```xml
<include resource="org/springframework/boot/logging/logback/defaults.xml" />
<include resource="org/springframework/boot/logging/logback/console-appender.xml" />
```
