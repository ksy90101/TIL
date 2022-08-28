# 1장. 운영 서버와 AWS 소개

## 1.1. 운영 서버

- 실세 사용자들을 대상으로 서비스하는 서버
- 트래픽 대응, 빠른 응답 속도, 높은 가용성 보장이 중요

## 1.2. 운영 서버 관리의 세 단계

- 환경 구성
    - 서비스할 코드를 구동시킬수 있는 서버 인프라 구축
- 코드 배포
    - 구성환 환경에 최신 버전 코드를 빠르고 안전하게 배표
- 모니터링
    - 안정적인 서비스 운영을 위해 서버와 코드에 어떤 이상이 없는지 바로 파악하고 대응할 수 있게 도와주는 것

## 1.3. AWS

- Amazon Web Service는 2006년 미국 기업 아마존에서 만든 클라우드 서비스 플랫폼이다.

### 클라우드 서비스 플랫폼을 써야 하는 이유

- 과거에는 아래와 같이 인프라를 운영했다
    - 서버를 직접 구매한 후 회사나 IDC에 설치 관리
    - 서버뿐만 아니라 수많은 인프라(DB, 캐시, 보안 등)에 대한 전문 인력 필요
    - 유연하게 서버를 늘리거나 줄이기 힘들다
    - 서버를 넉넉하게 구매해놓고 사용하지 않는 비 효율적인 자원 낭비 많음
- 클라우스 서비스 플랫폼을 사용하면
    - 필요한 사양의 서버를 몇 번의 클릭만으로 추가, 제거 가능
    - 사용한 시간만큼만 비용 지불
    - 클라우스 서비스 제공 업체의 전문 인력이 있어 개개인의 안정성이나 성능 등에 대해서도 고민이 줄어듬
    - 즉, 적은 비용, 시간, 인력으로 큰 규모의 서비스를 운영이 가능함

### 전 세계에서 사용할 수 있는 AWS

- 전 세계에 인프라를 구축하고 있으며, 지리 영역을 리전(region)이라고 함
- 서비스하려는 지역에 가까운 리전을 선택해야 네트워크 지연 시간을 최소화 할 수 있음
- 한국에도 서울 리전이 있음
- 대부분의 서비스는 모든 리전에 제공되지만, 새로운 서비스는 오픈날짜가 상이하기도 함
- 하나의 리전 안에도 여러 격리된 위치에 데이터 센터들을 운영하고 있는데, 이걸 가용 영역이라고 함
- 장애 대응을 위해 가용 영역을 두는데, A 가용 영역에 지진이 발생하여 서버의 문제가 발생해도 B 가용영역은 문제없이 서비스를 제공하기 위해서다.

> AWS는 12개월 동안 프리티어를 지원한다.
[사이트](https://aws.amazon.com/ko/free/?all-free-tier.sort-by=item.additionalFields.SortRank&all-free-tier.sort-order=asc)를 참고하자
> 

### 특이사항

> We can't create a 'bucket' with an existing name, because AWS considers S3 as Global
> 
- S3는 글로벌로 관리되고 있기 때문에 이미 존재하고 있는 버킷을 만들순 없다.
- 그러나 버킷을 생성할때 리전을 선택할 수 있다.
- 버전관리를 이용해 여러 리전에 동일한 버킷(이름은 다른)을 만들수 있다.