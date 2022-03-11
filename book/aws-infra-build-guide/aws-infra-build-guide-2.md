# 2장. 운영 서버 환경의 구성

## 2.1. 운영 서버 아키텍처의 이해

### 단일 서버

![image](https://user-images.githubusercontent.com/53366407/157777476-570dffb6-7bb0-48f7-8a4f-e7845d1ff977.png)

- 요청을 보내는 클라이언트와 요청을 처리하는 서버가 한 대가 있다.
- 데이터베이스와 애플리케이션이 하나의 서버에서 실행되고 있다.
- 따라서 별도의 네트워크설정을 할 필요가 없다는 장점이 있다.
- 단점
    - 전체 서비스에 장애가 생길 확률이 높다
        - 데이터베이스와 애플리케이션이 자원을 공유하기 때문에 하나라도 자원을 모두 사용하거나 장애가 발생하면 전체 서비스의 문제가 발생한다.
    - 서버 자원을 효율적으로 사용하기 어렵다
        - 애플리케이션과 데이터베이스는 각 속성에 따라 더 중요한 자원의 종류나 최적화를 위해 필요한 OS 설정이 다를 수 있다. 둘다 만족하기 위해 필요 이상으로 고사양 서버를 사용해야 한다.
    - 보안성이 떨어진다
        - 데이터베이스는 보안상 포트나 IP 등 접속 지점을 최소화 해야함
        - ***웹 애플리케이션은 특성상 다양한 IP 주소와 포트 번호에 대해 요청을 받을 수 있어야 한다.***
        - 서버 내 여러 파일들도 업로드될 수 있으모로 데이터베이스가 공격당할 가능성이 높아진다.
    - 스케일 아웃이 힘듦
        - 서버 자원 확장을 할 때 추가된 서버들의 주소를 새로 알아야 하며, 데이터도 똑같은 복제본이 여러 개 생겨 관리하기 어렵다

### 애플리케이션/데이터베이스 서버 분리

![image](https://user-images.githubusercontent.com/53366407/157777502-08f18936-3b29-4628-bef2-4288d4e34d86.png)

- 단일 서버 구성에서 데이터베이스를 별도 서버로 분리함
- 단일 서버의 3가지 단점은 해결되지만,  `스케일 아웃` 문제는 해결되지 않다.
- 이유는 클라이언트에서는 하나의 서버를 바라보고 있기 때문이다.

### 서버 단위의 로드 밸런서

![image](https://user-images.githubusercontent.com/53366407/157777514-4374e67d-66ab-4bc0-8b14-ea1ddf9f60f8.png)

- 클라이언트가 애플리케이션을 실행하는 서버와 직접 통신하는 대신 로드밸런서라는 서버와 통신하고 그 뒤에 여러 대의 애플리케이션 서버를 두는 방식
- 클라이언트는 로드 밸런서와 통신하고 로드 밸런서가 뒤에 있는 서버에게 나눠 준다
- 응답을 로드밸런서가 받아 클라이언트에게 처리한다.
- 가장 큰 이점은 스케일 아웃이 가능하다는 것이며, 하나의 서버가 문제가 발생하도 다른 서버를 이용하면 되기 때문에 장애를 최소화 할수 있다는 점이다.
- 그러나 로드 밸런서가 장애가 나면 전체 서비스에 장애가 나기 때문에 주의 해야 한다.
- 로드 밸런서는 OSI 7레이어에서 L4 스위치가 역할을 한다.

### 서버 내 앱 단위의 로드 밸런서

- 여러 애플리케이션 프로세스들에 요청을 분산시키는 앱 단위 로드밸런서를 추가한다.
- 애플리케이션을 동일한 여러 프로세스로 만들어 외부에 들어온 요청을 하나의 프로세스 중 하나로 보내주는 역할을 하는 방식
- 하나의 서버에서 여러 개의 요청을 동시 처리할 수 있고 서버 자원을 최대한으로 사용할 수 있는 만큼 프로세스를 실행해 서버 자원도 더욱 효율적으로 사용 가능하다.

## 2.2. AWS EC2를 이용한 서버 인스턴스 생성과 관리

### EC2란?

- Elastic Computer Cloud로 가상 서버라고 생각하면 된다
- 대부분의 OS를 지원하며 수십 가지 사양들을 제공한다.
- 가장 큰 이점은 필요할 때마다 켜고 끌 수 있어 사용한 시간에 대해서만 비용을 지불하면 된다.

### AMI란?

- EC2 인스턴스의 기반이 되는 이미지로 CD, ISO 파일과 동일하다고 생각하면 된다.
- 같은 OS여도 특정 버전의 자바처럼 내가 원하는 환경을 구성한 뒤 이미지로 만들어 재사용이 가능하다.

### 보안 그룹

- 보안을 위해 IP, 포트 번호를 이용해 정의해두는 서버 접속 규칙
- 특정 IP, 포트에 대해 허용할 수 있지만, 금지도 가능하다.
- 정의된 규칙을 여러 서버에 적용이 가능하다.

### 키 페어

- 서버에 접속하기 위한 열쇠로 공개 키 암호화 방식을 사용한다.
- 즉, 서버에는 공개키를, 사용자는 개인키를 들고 접속하게 된다.

### 실습

![image](https://user-images.githubusercontent.com/53366407/157777540-cadb523d-9144-4bda-9926-d66a754f397f.png)

- 인스턴스 시작을 클릭합니다.

![image](https://user-images.githubusercontent.com/53366407/157777562-717d1d33-bcc6-492b-abc8-b32685d60d92.png)

- Amazon Linux 2 AMI (HVM), SSD Volume Type을 선택합니다

![image](https://user-images.githubusercontent.com/53366407/157777586-778e8428-e1fd-4815-9613-76bddca13536.png)

- t2.micro를 선택합니다

![image](https://user-images.githubusercontent.com/53366407/157777597-7f914b98-79aa-4f7a-9263-4fa64d008c49.png)

- 특별한 설정 없이 기본값을 사용합니다

![image](https://user-images.githubusercontent.com/53366407/157777612-b11b19f4-27cc-44ea-bc60-0fd6e0812d5a.png)

- 기본값을 사용합니다

![image](https://user-images.githubusercontent.com/53366407/157777627-a88bfb0d-d448-44ba-9dbf-aa61d656739e.png)

- 수백개의 인스턴스가 있을 수도 있기 때문에 이름을 지정해 구분하기 위해 태그를 지정한다. (Name을 사용하면 이름을 지정할 수 있다.)

![image](https://user-images.githubusercontent.com/53366407/157777637-95f61301-9368-4507-9030-6a44fed3573d.png)

- 기본 보안그룹을 사용한다.
- 현재는 SSH로 접속이 가능한 포트만 허용해놓은 상태이다.

![image](https://user-images.githubusercontent.com/53366407/157777654-40df0590-fea3-4c0e-894b-296abb0876e5.png)

- 한번 검토 후 시작을 눌러 인스턴스를 생성한다.

![image](https://user-images.githubusercontent.com/53366407/157777664-05b6ce39-c855-4943-864e-48cce08407b6.png)

- SSH 접속을 위해 키를 생성한다. `새 키 페어 생성` 을 선택하고 이름을 입력 후 `키 페어 다운로드`를 눌러 키를 다운로드 받는다.
- 그 이후 인스턴스 시작을 누르면 인스턴스가 생성된다. (보통 몇 분 소요)

### 인스턴스 상태

- pending : 진행중
- running : 실행 중
- stopped : 중지
- terminated: 삭제(종료)

### 인스턴스 접속

- 인스턴스 설명에 보면 퍼블릭과 브라이빗 도메인과 IP가 각각 할당되었으며, 해당 도메인과 IP를 이용해 인스턴스에 접근할 수 있다.
- 별도 설정이 없다면 인스턴스가 종료되고 다시 시작하게 될 때 IP와 도메인이 변경되게 된다.
- 접속하기 위해 퍼블릭 IP를 이용해 SSH로 접속한다.
- 해당 환경은 맥북을 기준으로 한다.

```java
chmod 400 [key_name].pem
ssh -i [key_name].pem ec2-user@ip
```

- 위 명령어를 키가 잇는 곳에서 진행한다.

![image](https://user-images.githubusercontent.com/53366407/157777684-c6bc8dc6-ca91-41ce-b01d-dc988203ebb4.png)

- 위와 같이 나오면 정상적으로 접속이 된것이다.

### HTTP, HTTPS 접근 가능하도록 보안 그룹 추가

- 보안그룹에 들어가 보안그룹 생성을 클릭한다

![image](https://user-images.githubusercontent.com/53366407/157777698-3d886936-96ac-4faa-8ad9-d3df97aba5cc.png)

- 이와 같이 보안그룹을 생성해 HTTP, HTTPS 포트를 허용해준다
- 이후 보안그룹 생성 버튼을 눌러 보안그룹을 만들어 준다
- 생성한 보안 그룹을 기존 인스턴스에 적용하기 위해 아래 메뉴를 클릭한다

![image](https://user-images.githubusercontent.com/53366407/157777703-e17617b6-222f-4250-a153-09bc31e384eb.png)

- 방금 만든 보안 그룹을 추가한다.

![image](https://user-images.githubusercontent.com/53366407/157777719-6c6be479-4005-40d7-ab48-5b55e8a0084b.png)

- 그 이후 저장버튼을 클릭한다.

## 2.3. 소스코드 배포

- 환경은 Spring Boot를 사용한다.

### Git을 이용한 소스코드 배포

```java
sudo yum install curl-devel expat-ㅛy gettext-devel openssl-devel zlib-devel
```

- 시스템 패키지를 설치할 때는 sudo를 사용해 야 한다.
- 위 명령어를 통해 yum 패키지 매니저를 통해 필요한 패키지를 설치한다.

```java
cd /var // 폴더로 이동
sudo chown ec2-user /var // 권한 해제
git clone [github저장소] // 저장소 불러오기
```

- 배포 코드 저장소를 불러온다.
- 자바를 설치해준다.

```java
sudo amazon-linux-extras install java-openjdk11
```

- gradle을 이용해 빌드를 진행한다.

```java
./gradlew clean build
```

- 애플리케이션을 실행시킨다.

```java
nohup java -jar [jar파일명].jar &
```

## 2.4. 웹 서버와 웹 애플리케이션 서버

### 웹 서버

- 웹 서버는 클라이언트에서 HTTP, HTTPS 프로토콜로 요청을 받고 정적인 파일들을 응답으로 전달
- 대표적인걸로 nginx, Apache, IIS 등이 있다.

### 웹 애플리케이션 서버(WAS)

- 클라이언트의 요청에 대해 코드 실행을 통해 동적 응답을 만들어 준다.
- 또한 배포한 코드를 프로세스로 실행시키고, 해당 프로세스에 클라이언트의 요청을 넘겨주는 역할을 하기도 한다.
- 단순히 프로세스의 실행뿐만 아니라 서버 자원을 최적으로 사용하기 위해 프로세스의 수나 프로세스의 메모리를 조절하기도 한다.
- 대표적으로 Phusion Passenger, Apache Tomcat, JBoss등이 있다.

> 참고로 ruby-on-rails는 puma를 사용하며, django는 gunicorn를 사용한다.
> 

### 웹 서버와 웹 애플리케이션 서버의 사용

- 웹서버와 웹 애플리케이션 서버는 보통 함께 사용된다. 한 서버에서 여러 종류의 애플리케이션을 서비스하는 경우 웹 서버가 정적 파일을 처리하는 역할이나 여러 웹 애플리케이션 서버로 라우팅하는 역할을 할 수 있다.
- 웹 서버에서는 클라이언트에서 요청한 도메인 주소를 분석해 적절한 애플리케이션 서버로 전달해줄 수 있다. 또한 이미지나 CSS 같은 정적인 데이터는 굳이 애플리케이션을 실행해서 로드할 필요 없이 웹 서버에서 바로 응답으로 주면 된다.
- 두 서버의 기능을 모두 제공하는 제품도 많기 때문에 반드시 둘 다 사용해야 하는 것은 아니다. 언어 등 필요한 기능과 상황에 맞게 웹 서버와 웹 애플리케이션 서버 제품을 선택하고 사용하면 된다.

### 클라이언트 요청 이동 경로

1. 클라이언트에서는 네트워크와 ISP(인터넷 서비스 프로바이더) 등을 거쳐 서버 인스턴스로 네트워크 패킷을 보낸다.
2. 하드웨어인 서버 인스턴스에서는 해당 패킷을 읽어와 인스턴스에 설치된 서버 OS에 패킷을 넘간다.
3. 서버 OS는 해당 패킷을 읽고 패킷에 적힌 포트를 리스닝하고 있는 웹 서버 프로세스에 요청 내용을 전달한다.
4. 웹 서버에서는 해당 HTTP 요청을 분석해서 적절한 웹 애플리케이션 서버에 전달한다.
5. 웹 애플리케이션 서버에서는 애플리케이션 코드를 실행하고 있는 애플리케이션 프로세스에 HTTP 요청의 내용을 파라미터를 전달해서 실행한 뒤 그 코드의 결괏값을 다시 웹 서버에 전달한다.
6. 그 뒤로는 앞서 진행했던 단계의 반대 방향으로 클라이언트에게 응답이 전달된다.

### nginx 설치 및 서비스

```java
sudo amazon-linux-extras install nginx1
```

- nginx를 설치해준다.
- nginx 환경설정 파일로 이동한다.

```java
sudo vim /etc/nginx/nginx.conf
```

- spring boot의 WAS인 tomcat의 리버스 프록시를 할 수 잇도록 아래와 같이 설정 → server 블록 아래 넣기

```java
location / {
    proxy_pass http://localhost:8080;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
}
```

- `$remote_addr`, `$http_host`는 nginx의 Core 모듈(ngx_http_core_module)에 있는 변수입니다
    - `$remote_addr` 는 클라이언트의 IP를 가리킵니다
        - 서버(스프링부트)에서도 `request.getRemoteAddress();` 를 사용해 클라이언트의 IP를 얻을 수 있습니다
    - `$http_host` 는 `$host` 와 같은 값입니다
        - `$host` 는 요청 헤더에서 'Host' 필드를 가져오거나 서버이름 또는 호스트 이름을 가져옵니다
        - `$http_host` 는 `$host`와 같은 역할을 하나 보안상? `$http_host`를 쓰는 것 같습니다. 정확히 모르겠네요ㅠ 해석 가능하신 분 해석점 부탁드립니당 (**[Reasons to use '$http_host' instead of '$host' with 'proxy_set_header Host' in template ? nginx-proxy/nginx-proxy#763](https://github.com/nginx-proxy/nginx-proxy/issues/763)**)
- `$proxy_add_x_forwarded_for`는 nginx의 Proxy 모듈(ngx_http_proxy_module)에 있는 변수입니다
    - `X-Forwarded-For` 헤더에 `$remote_addr`을 자동으로 추가하는 역할을 합니다
    - 즉, 거치는 클라이언트마다 클라이언트의 IP를 `X-Forwarded-For` 헤더에 계속해서 추가해줍니다
- 설정을 변경한 이후 nignx를 꼭 reload 해줘야 한다.

```java
sudo service nginx restart
```

- ip주소로 들어가면 해당 화면이 나오는걸 확인할 수 있다.

![image](https://user-images.githubusercontent.com/53366407/157777730-11b3de43-176f-46e4-8247-4ed24e582261.png)

### 서비스 명령어 추가

- 서버를 재시작하거나 끄기 위해서 사용하는 명령어가 쉽지 않다.
- 커스텀 명령어를 추가하는 작업을 해보자.
- 아래 경로를 들어가 스크립트를 추가한다.

```java
cd /etc/init.d

sudo vim nginx
```

- 아래의 시작 스크립트를 복사해 붙여넣기를 진행한다.

```java
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemin
#
# chkconfig:   - 85 15 
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /opt/nginx/conf/nginx.conf
# pidfile:     /opt/nginx/logs/nginx.pid
# modified from http://articles.slicehost.com/2009/2/2/centos-adding-an-nginx-init-script

# Source function library.
. /etc/rc.d/init.d/functions

# Source networking configuration.
. /etc/sysconfig/network

# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0

nginx="/opt/nginx/sbin/nginx"
prog=$(basename $nginx)

NGINX_CONF_FILE="/opt/nginx/conf/nginx.conf"

lockfile=/var/lock/subsys/nginx

start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}

stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}

restart() {
    configtest || return $?
    stop
    start
}

reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}

force_reload() {
    restart
}

configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}

rh_status() {
    status $prog
}

rh_status_q() {
    rh_status >/dev/null 2>&1
}

case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac
```

- 실행 가능 서비스로 만들기 위해 권한을 변경한다.

```java
sudo chmod 755 nginx
```

- 아래와 같이 손쉽게 Nginx 서비스를 조작할 수 있다.

```java
$ sudo service nginx stop // 중지
Stopping nginx (via systemctl):                            [  OK  ]

$ sudo service nginx start // 시작
Starting nginx (via systemctl):                            [  OK  ] 

$ sudo service nginx restart // 재시작
Restarting nginx (via systemctl):                          [  OK  ]

$ sudo service nginx status // 상태 확인
● nginx.service - The nginx HTTP and reverse proxy server
...
```

### 시스템 시작 시 자동 시작 서비스에 등록

```java
sudo chkconfig --add nginx // 시작 관리 툴 chkconfig를 이용해 nginx 스크립트 등록
sudo ntsysv // runlevel을 GUI로 관리할 수 있도록 해주는 명령어
```

![image](https://user-images.githubusercontent.com/53366407/157777750-22815d54-778b-4705-b9f6-76bc3ca4a346.png)

- nginx를 선택해 확인을 누른다.
- 스페이스가 선택이며, 탭은 버튼 이동이다.

### 하나의 서버에서 두 개의 애플리케이션 서비스하기

- 하나의 서버는 하나의 IP주소를 갖게 되지만 그 IP주소에 여러 개의 도메인을 연결할 수 있다.
- nginx를 라우터 역할을 하도록 변경해보겠다.
- 새로운 서버를 띄울 코드를 배포한다. 아울러 nignx의 설정을 변경한다.

```bash
server {
	...
	server_name <EC2 인스턴스의 IPv4 퍼블릭 주소>;
	...
}

# 기존 설정에 새로운 server dythfmf gksk ej cnrkgksek.
server {
	listen 80;
	# 이 서버에는 도메인 주소를 입력해서 기존에 추가한 서버와 구분한다.
	server_name <퍼블릭 DNS(IPv4)>;

	root /var/www/aws-exercise-b/public;

	passenger_enabled on;
	passenger_app_type node;
	passenger_startup_file /var/wwww/aws-exercose-b/app/ks;
}
...

```

- nginx 재시작을 한다.
