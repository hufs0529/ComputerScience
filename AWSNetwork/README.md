# AWSNetwork

## [Defatult VPC]
![화면 캡처 2023-07-16 155653](https://github.com/hufs0529/ComputerScience/assets/81501114/1abd9cb5-6abe-43a4-8059-44c0fc729008)
##### 1. VPC는 REGION 레벨의 리소스, REGION에 1개만 생성 가능하다
##### 2. VPC 안에는 서브넷, EC2, RDS같은 가용 가능 영역의 자원 포함
##### 3. REGION 레벨에는 S3가 위치하며 VPC안에 포함되지는 않고 REGION 레벨 단에 포함된다  
![화면 캡처 2023-07-16 160622](https://github.com/hufs0529/ComputerScience/assets/81501114/f4ffa1cd-ec06-469d-b0b3-9b613c2f9e24)

#### VPC란? 물리적으로 제공하는 온 프레미즈와 유사하지만 차이점이 존재한다.
##### 1. 물리적으로 제공하는 네트워크 인프라가 아닌 *가상*의 독립적인 네트워크 제공해주는 차이점이 존재한다
#### 2. 네트워크 인프라 설계를 할수 있도록 서브넷 구성, 라우트 테이블 설정, 방화벽 설정을 통해 네트워크의 흐름을 제어하고 vpc내 배포된 자원 보호 가능하다
---
## [Default VPC 구성-라우팅 테이블]
![화면 캡처 2023-07-16 160750](https://github.com/hufs0529/ComputerScience/assets/81501114/a2eb2934-a046-46c9-976e-0cdfee79f212)
##### 1. 어떠한 리전이라도 default vpc는 1개로만 구성
##### 2. 리전마다 사용하고 있는 가용 영역은 다르다(최소 2개, 서울은 4개)
##### 3. 메인 라우트 테이블, NACL, 보안그룹 각 1개 구성
##### 4. DEFAULT VPC는 모두 인터넷 게이트를 가지고 있어서 외부 인터넷과 통신 가능
##### 5. 4개의 서브넷은 1개의 라우팅 테이블에 연결되어 있음
##### 172.31.0.0/16이라는 vpc를 destination으로 지정한다.-> local로 내부에서는 subnet끼리 서로 통신 가능
##### 0.0.0.0/0을 destination으로 지정해서 internet gateway를 통해서 통신하라고 설정되어 있다
##### 인터넷 게이트웨이르 외부와 연결이 가능한 것을 public subnet이라 하는데 default vpc는 public으로 설정되어 있다
#### AWS에서 라우트 테이블이란?
> VPC 내에는 Subnet이 있으며 각 Subnet은 각기 다른 네트워크 대역을 가지고 있다.
> 그리고 한 Subnet에서 다른 한쪽 Subnet으로 가기 위해서는 라우팅이 필요하다.
> Subnet끼리 연결해주는 것이 AWS에서 라우트 테이블의 역할
#### NACL이란?
> 보안그룹처럼 방화벽 역할 담당
> 서브넷 단위
> 인스턴스 단위로 제어 불가능
> 들어오는 트래픽과 나가는 트래픽을 구분하지 않는
#### Secirity Group이란?
> 인스턴스에 대한 인바운드/아웃바운드 트래픽을 제어하는 방화벽 역할
> NACL과 가장 큰 차이점을 본다면 NACL은 네트워크 방화벽이고, Security Group은 인스턴스 방화벽
---
## [Default VPC 구성 - NACL]
![화면 캡처 2023-07-16 161942](https://github.com/hufs0529/ComputerScience/assets/81501114/fc887ee0-46f8-4393-8d21-af8993b2365c)
##### NACL - 상태를 저장하지 않는 방화벽의 역할 수행, 라우트 테이블과 마찬가지로 서브넷 단일 설정
##### 1. 4개의 서브넷이 모두 nacl을 바라보며 inbound, outbound 모두 설정해야 한다(상태를 저장하지 않기 때문)
##### 2. Rule 숫자 작은것부터 적용된다
##### 3. 100번에 설정된 트래픽이 허용되어 있다면 1000번에 거부가 되어 있더라고 해당 트래픽은 허용되게 된다(순서 매우 중요!)
---
## [Default VPC 구성 - Security Group]
![화면 캡처 2023-07-16 162043](https://github.com/hufs0529/ComputerScience/assets/81501114/ba7ea98a-641c-4b53-86a5-8a11a9e88f88)
##### 1. 기본적으로 설정된 보안 그룹은 같은 보안 그룹에 포함되어있는 경우 서로 통신이 가능하게 설정되어 있다
##### 2. 보안그룹은 상태저장이자 방화벽이다
##### 3. default vpc에서 기본적으로 설정된 보안그룹은 같은 보안 그룹에 포함되어 있을 경우 서로 통신이 가능하다.
> 상태 저장 방화벽이기 때문에 INBOUND로 허용되어 있다면 따로 OUTBOUND 설정 안해도 된다
### * nacl과 security group 비슷하지만 nacl은 서브넷 단위로 적용이되고 sg는 인스턴스에 대해 적용이 된다(네트워크 단위 VS 인스턴스 단위)
---
## [CIDR(classless inter-domain routing)]
![화면 캡처 2023-07-16 162248](https://github.com/hufs0529/ComputerScience/assets/81501114/15f77d9a-0492-4054-9e85-16385c428937)
##### 1. IP주소대역을 유연하게 네트워크 영역을 나누는 방법
##### 2. 192.168.0.0/16-> 첫 16비트를 네트워크 라우팅을 위한 주소로 사용한다고 의미한다
> IP는 8자리 이진수 4개를 이용하여 표현한다
> 네트워크 주소 뒤는 HOST주소로 사용 가능하다(192.168.0.0~192.168.255.255 까지 호스트 주소 사용 가능)
> 2^16=65534개의 host주소
> 192.168.0.0 ~ 192.168.255.255까지 사용을 의미
---
## [VPC대역 정하기]
![화면 캡처 2023-07-16 162630](https://github.com/hufs0529/ComputerScience/assets/81501114/fca51851-7e2c-4d75-a759-d989290857d5)
##### 1. RFC 1918에 정의된 사설 IP 대역 사용 권장
> PUBLIC IP가 부족하기 때문이다
> VPC 내에 EC2, RDS등 많은 컨테이너가 배포되어 있다면 많은 IP가 필요하다
> 보안때문이다!!! vpc 안에서는 PRIVATE을 사용함으로써 내부끼리만 통신하므로 외부로부터의 침입을 원천 봉쇄한다
##### 2. VPC 한번 정하면 변경 불가 BUT 대역 추가는 가능
---
## [VPC SUBNETTING]
![화면 캡처 2023-07-16 162940](https://github.com/hufs0529/ComputerScience/assets/81501114/f7e4d900-0e32-43bc-b644-8782f74063cb)
##### 1. VPC IP 대역으로 할당된 대역을 하나의 라우팅 그룹으로 관라힌다면 라우팅에 부담이 갈 수 있다
##### 2. VPC 구조를 계층적 구조를 활용하여 네트워크를 쪼개서 관리 가능하다
> 이를 Subnetting
---
## [VPC SUBNET 모범사례]
![화면 캡처 2023-07-16 163114](https://github.com/hufs0529/ComputerScience/assets/81501114/5004efbb-58c1-4d3b-876b-6de70ce7afc0)
##### 1. 서브넷은 라우팅 테이블과 연결하여 네트워크의 흐름 제어 가능
##### 2. 외부와 통신 가능한 PUBLIC, 차단된 PRIVATE SUBNET 분리한다
##### 3. PUBLIC은 인터넷과 통신 가능하기 때문에 필요한 서버를 제외를 배치하지 않는것이 좋다
##### 4. VPC에서 배포된 서버들이 인터넷으로 노출되는 것을 최소화
##### 5. 서브넷으로 IP대역을 분리한 후 ROUTE TABLE과 연결하여 네트워크의 흐름을 제어한다
---
## [VPC Routing 전략]
![화면 캡처 2023-07-16 163129](https://github.com/hufs0529/ComputerScience/assets/81501114/65f6ac14-f23d-4297-a2a5-1998a60766f3)
##### PUBLIC SUBNET은 연결된 라우트 테이블에 VPC에서 사용하는 IP대역 이외에 모든 트래픽을 인터넷 GATEWAY( VPC와 외부 인터넷 단과 통신 가능하게)로 라우팅되게 설정
##### private subnet에 배포된 서비스가 인터넷에 접근하기 위해서는 2가지 필요
> 1. 네트워크 트래픽이 외부로 나갈수있도록 설정하기
> 2. 외부 인터넷으로 나갈때 통신할 public ip  -> 이때 NAT 게이트웨이가  필요하다. private subnet에 할당된 라우트 테이블은 로컬 ip 대역 이외에 모든 트래픽은 nat 게이트웨이로 나갈 수 있도록 설정, NAT 게이트에이가 할당된 IP 를 통해서 인터넷과 통신하며 결과가 NAT르 돌아오면 이 것을 PRIVATE으로 반환시켜 전달한다.
##### NAT 게이트웨이가 B 가용 영역에 배포된 상태에서 B에 문제가 생긴다면 가용영역 A는 외부와 통신이 불가능할 것이다
![화면 캡처 2023-07-16 163458](https://github.com/hufs0529/ComputerScience/assets/81501114/25b5bf5b-8434-48b2-ad74-b44febc6d6a8)
#### A에도 NAT 게이터웨이를 설정할 수 있겠지만 이중화시키킬 경우 비용이...
#### RDS의 경우 외부와 통신할 필요가 없기 때문에 VPC내부로만 통신이 가능하도록 설정해서 외부 트래픽과 통신 봉쇄 가능

---
## [VPC NACL 전략]
![화면 캡처 2023-07-16 163544](https://github.com/hufs0529/ComputerScience/assets/81501114/08c38812-74d8-4332-9bff-74447e4d92d0)
##### PUBLIC SUBNET에서 DDOS공격을 받을 때 DDOS IP대역을 차단하여 리소스 보호 가능
---
## [VPC SECURITY GROUP 전략]
![화면 캡처 2023-07-16 163631](https://github.com/hufs0529/ComputerScience/assets/81501114/e3744fb4-e1e3-481e-9554-62f95e37e2cb)
##### 1. NLB는 HTTP를 외하여  0.0.0.0(모든 대역)에서 트래픽 허용(불특정 다수)
##### 2. EC2(웹서버)는 SG-FMASKDF로 NLB 대역에서만 트래픽 허용 -> 보안 그룹 서로 참조 가능!!!
##### 3. RDS의 보안 그룹은 EC2가  포함된 보안 그룹만 허용

---
## [Hybrid Networking]
![화면 캡처 2023-07-16 163730](https://github.com/hufs0529/ComputerScience/assets/81501114/e35fffe7-066f-4ce5-be63-f18b833c0b8e)
##### vpc는 독립된 가상의 네트워크 환경의 서비스를 일컬음
##### vpc 내 서버들 이외에 다른 aws 서비스도 외부 인터넷과 동일하게 인터넷 트래픽 제어
##### public subnet에 있는 ec2가 s3(서브넷 외부에 있는 것)과 통신하기 위해서는 인터넷 게이트웨이를 통해서 s3로 서비스를 찾아가게 된다

---
## [엔드포인트]
---
## [하나 이상의 vpc 사용하기]
