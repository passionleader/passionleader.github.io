---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation2
# 영문으로 작성하는 경우 영문제목
title: 네트워크 애뮬레이션 실습2 - 라우터 연결 및 기본 구성

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, linux, GNS3, Wireshark, router]

# 섬네일 이미지
img: "https://conceptdraw.com/a1778c3/p1/preview/640/pict--router-cisco-routers---vector-stencils-library.png--diagram-flowchart-example.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-05 17:03:22 +0900

# image_lazy_loader_posts = false 혹은 image_viewer_posts = false인 경우에만 사용하세요
#image_viewer_on: true
#image_lazy_loader_on: true

# 블로그내 검색 혹은 검색 엔진 검색에서 예외할건가
#on_site_search_exclude: true
#search_engine_exclude: true

# to disable this page, simply set published: false or delete this file
#published: false
---

<!-- outline-start -->

[카카오 클라우드 스쿨] GNS3 및 Wireshark를 통한 네트워크 에뮬레이션

<!-- outline-end -->



# 네트워크 애뮬레이션 실습2 - 라우터 연결 및 기본 구성
* 오늘의 실습 내용
  * ![img_70.png](img_70.png)

<br>


## 1. 라우터 연결 및 패킷 캡쳐해보기 <br>
오늘 할 것 <br>
![img_75.png](img_75.png)
* 브리지(외부 네트워크라고 가정)와 VMnetwork1 사이에 라우터를 구성하고, 인터넷을 연결해 볼 것이다

<br>

1. 우선 GNS3에서 다음과 같이 구성
   * ![img_78.png](img_78.png) <br><br>
3. 터미널 실행
   * 샵(#)은 보기만 할 수 있음, configure을 입력해서 수정해야 함
   * ?을 넣으면 도움말을 볼 수 있음
   * 축약어를 사용할 수도 있음
     * ![img_77.png](img_77.png) <br><br>
4. interface fa0/0에 주소 할당하기 **(브릿지 ~ 라우터 앞단 연결)**
   * int fa0/0 : 인터페이스 추가함
   * ip add 192.168.1.105 255.255.255.0 : 주소 추가
   * no sh: no shutdown이라는 것을 알려줌
   * do ping 192.168.1.199: 강사님께 핑 보내보기 <br><br>
5. 패킷 캡처를 한번 보자
   * ![img_80.png](img_80.png)
   * wireshark로 연결이 될 것임
   * 필터 적용하기
     * ![img_81.png](img_81.png)
   * 결과
     * ![img_83.png](img_83.png)
     * 5개 보내서 5개 왔다 (총 10개의 패킷이 캡쳐됨)
     * ![img_82.png](img_82.png)
     * 잘 안될 경우 wire를 지우고 다시 연결해 보자(에뮬레이터다보니 이런 문제가 발생하기도 함)
   * request 친구를 더블클릭하면
     * ![img_84.png](img_84.png)
     * ![img_85.png](img_85.png)
     * ![img_86.png](img_86.png) <br><br>

<br>
<hr>


## 2. ARP에 저장되었는지 확인해 보기
* ARP(address resolution resolution protocol)
  * 통신하고자 하는 목적지의 IP주소를 이용하여 해당 목적지의 물리주소인 MAC주소를 찾기 위한 프로토콜이며, 정상적으로 arp가 동작한 이후에는 arp 테이블에 목적지 IP와 MAC 주소가 등록된다. (기억함)
  * 이후 자신이 최종적으로 수행했던 통신을 하고자 한다면, ARP 테이블에 있는 정보를 참고하여 패킷을 완성하게 된다
  * ![img_116.png](img_116.png)
    * 전송 중에 Mac주소 부분은 라우터의 Mac주소로 변경되지만, IP 부분은 변경되지 않는다
    * (IP가 다르면 서로 다른 네트워크) <br> <br>
* APT 테이블을 확인해 보면 연결했던  mac주소를 담고있는것을 확인할 수 있다
  * ![img_87.png](img_87.png)
  * aging time: arp table에 mac주소가 저장되는 기간(지나면 다시 받아와야 함) <br><br>

* 핑을 보낼 때, ARP table에 해당 Mac 주소가 있으면 바로 찾아 간다
* 없다면, IP를 통해 브로드캐스트하여 Mac 주소를 받아 온다

<br>
<hr>


## 3. 라우팅 테이블에 등록하기
* 라우팅 테이블에는 현재 라우터에서 도달할 수 있는 주소가 저장된다
  * ![img_88.png](img_88.png)
* 0.0.0.0 모르는 모든 IP는 바깥쪽과 연결된 라우터(192.168.1.1)로 보내버리겠다고 추가하면
  * ![img_89.png](img_89.png)
* 라우팅 테이블에 등록된다
  * ![img_90.png](img_90.png)
* 즉, 라우팅 테이블에 등록해 두면, 해당 패킷을 다른 곳으로 보내버릴 수 있다
  * 예를 들어 DB 하나가 죽어도 라우팅 테이블을 통해 패킷을 다른 DB로 연결되게끔 해 줄수 있다 <br> <br>
* AWS에서도 마찬가지로
  * 172.16.0.0/24 target:local - 내부 IP는 172.16.0에서 시작한다
  * 0.0.0.0/0 target:igw8236781236 - 외부 IP는 IGW로 보낸다 <br> <br>

* 이제 뒷단 구성도 해 보자 **(라우터 ~ VM 뒷단 연결)**
  * int fa0/1
  * ip add 172.16.1.2 255.255.255.0
  * no sh (셧다운 하지마)

<br>
<hr>



## 4. 리눅스에서 네트워크 설정
  * 환경변수이자 전역변수
    * ![img_91.png](img_91.png)
  * env로 확인 가능
  * 변수 지정할 때에는 붙여야 함(아니면 명령어와 매개변수로 간주하여 작동하지 않는다)
    * ![img_92.png](img_92.png)
  * 변수 출력 결과
    * ![img_93.png](img_93.png)
  * 없어도 에러가 아님
    * ![img_94.png](img_94.png)
  * 오류가 있으면 0, 아니면 다른 숫자
    * ![img_95.png](img_95.png)
  * grep: 해당 행을 찝어내기
    * ![img_96.png](img_96.png)
  * gawk: 해당 열을 찝어내기
    * ![img_97.png](img_97.png)
    * ![img_98.png](img_98.png)
* 네트워크 설정 변경하기
  * ![img_99.png](img_99.png)
  * 잘 변경됨
    * ![img_100.png](img_100.png)
* ICMP 전송해 보기
  * ![img_101.png](img_101.png)
  * (ARP가 빠르게 처리되는것을 확인함)

<br>
<hr>

## 5. 중간결과
* arp 테이블에서 나한테 보낸 놈들의 맥주소 확인해 보기
  * ![img_102.png](img_102.png)
    * 통신이 잘 된다
* 반대로 라우터에서 VM으로 보내보기
  * ![img_103.png](img_103.png)
    * 보내는것도 잘 되네!

* 이제 인터넷으로 보내보기
  * 도메인으로 하면 안된다
  * ![img_104.png](img_104.png)
  * IP 주소로 접속하면 잘 된다
  * ![img_105.png](img_105.png)

<br>

(참고)

```commandline
SSK#conf t
SSK(config)#access-list 1 permit any                    ---> 변경할 주소 지정
SSK(config)#ip nat inside so list 1 int fa0/0 overload  ---> "ip nat" 주소 변경할게
                                                        ---> "inside source list 1" 주소를 변경할 출발지 주소는 list 1에 지정된 놈들이야
                                                        ---> "int fa0/0" int fa0/0(192.168.1.x) 에 할당된 공인주소로 변경할게
                                                        ---> "overload" pat 활성화 -> 다수의 사설 주소가 fa0/0의 주소를 공유
SSK(config)#int fa0/0
SSK(config-if)#ip nat outside
SSK(config-if)#int fa0/1
SSK(config-if)#ip nat inside
```

<br>

* 이제 가상 머신에서 인터넷에 연결할 수 있음! 구글 접속 패킷 캡처를 해 보면
  * ![img_110.png](img_110.png)
    * NAT를 통해 접속된 모습을 확인할 수 있다
    * NAT을 통해 접속했기 때문에 목적지 주소가 다른 것을 확인!


* 구조는 다음과 같다http://192.168.1.105 ----> router ----> 172.16.1.100:80
  * IP는 현재 하나이므로, 정적 PAT

<br>
<hr>

## 6. 정적 NAT 매핑
* 시나리오: 외부에 있는 사용자들이 192.168.1.1xx:8001 포트로 접속할 경우, 내부에 있는 사설 주소인 172.16.1.100의 80번 포트로 접속되도록 한다
  * 우리가 필요한 것: 정적인 NAT 매핑, 192.168.1.1xx:8001 => 172.16.1.100:80

<br>

1 . router command
```commandline
ip nat inside so static tcp 172.16.1.100 80 int fa0/0 8001
```
* 정적으로 매핑을 하겠다
* 내부에 있는 사설 IP 주소인 172.16.1.100의 TCP 포트번호 80번과 & 공인 IP주소가 할당된 fa0/0의 8001 포트를 정적으로 매핑하겠다!!

<br>

2 . 웹서버 실행

```bash
systemctl start httpd  # 웹서버 실행
echo "helloworld" > /var/www/html/index.html  # 출력 내용을 파일로 저장
systemctl stop firewalld  # 방화벽 종료
cat /var/www/html/index.html  # 파일 내용을 출력함
```

* http://192.168.1.199:8001 로 접속하먄 NAT장비는 이를 172 16 1 100 80번 포트(리눅스의 웹서버)로 옴.
  * 단, 웹서버 httpd는 동작중이어야 하며, 방화벽은 해제되어 있는 상태여야 함

<br>

3 . 이제 다른 사용자의 HTTPD와 내 HTTPD로 연결할 수 있다

<br>

4 . 지금까지 다음을 구현해 보았다
* ![img_111.png](img_111.png)

<br>
<hr>

## 7. 실전문제
* 퇴근한 서버 관리자는 집에서 웹서버로 ssh로 연결을 시도하고자 한다
  * 아래와 같은 방법으로 접속이 가능해야 한다

```shell
ssh -l root -p 20022 192.168 1.199
```

* 이제 20222 포트로 접속을 시도하면 내부에 있는 리눅스 웹서버로 ssh 연결이 가능해야 한다
* 화면에는 public key를 저장할 것인지를 물을 것이고, yes를 타입 후 패스워드인 test123을 입력하면 정상적으로 원격지 서버에 접속할 수 있어야 한다
* 참고로 ssh 서버의 기본 포트는 22번이다.

라우터 커맨드에서
```commandline
ip nat inside so static tcp 172.16.1.100 22 int fa0/0 20022
```

완료! <br> 다음을 통해 중간 과정을 생략 가능
```commandline
ssh -l root -p 20022 192.168.1.123 hostname
```
<br>

* 그러나 ssh 접속 시 인증 방법이 두 가지가 있는데
  1. User와 PW로 인증할 경우, 이것이 털릴 경우 매우 곤란해 짐
  2. Key를 사용해 로그인하는 것이 일반적

<br>

* 키 저장하기

```commandline
[root@localhost .ssh]# ssh-keygen -q -f ~/.ssh/id_rsa -N ""
[root@localhost .ssh]# ls
       id_rsa  id_rsa.pub  known_hosts
```

* 키를 통해 로그인하기
```commandline
ssh-keyscan 192.168.1.103 > .ssh/authorized_keys
```


<hr>
<br>

## 8. (추가)
* 라우터에서 NAT 설정을 잘못한 경우 수정 방법
```commandline
do show run
```

* 설정값 확인 후, 잘못된 설정을 드래그
* no [해당 설정(우클릭)] 으로 없앤다
* 만일 사용중이라고 표기될 경우
  * 강제종료
  * do clear ip nat trans



