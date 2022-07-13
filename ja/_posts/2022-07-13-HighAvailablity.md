---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_HighAvailablity
# 영문으로 작성하는 경우 영문제목
title: (네트워크)고가용성

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [Network, FHRP, HSRP, VRRP]

# 섬네일 이미지
img: "https://image.shutterstock.com/image-vector/high-availability-icon-vector-logotype-260nw-2039766092.jpg"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-13 10:48:51 +0900

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

[카카오 클라우드 스쿨] 네트워크에서 고가용성에 대해 알아보고, 실습해 보자

<!-- outline-end -->



# 고가용성(High availablity)

> 절대로 고장 나지 않음 = 고가용성

* 사용자가 원하는 서비스를 요청했을 때 얼마만큼의 응답률을 보이는가?
* 고가용성이 구축되어 있는 환경은 서비스 다운타임이 거의 없는 환경을 의미함
  * (ex 서버를 2개로 구성하면 하나가 고장나도 계속 사용 가능)

<br>

* 고가용성을 위해서는 이중화를 구축해서 서비스의 다운타임을 줄여야 함
* 고가용성을 위해서는  Active/Standby (Master/Slave, Primary/Secondary ..)와 같은 환경을 구축한다
* Standby는 두 가지로 구분함
  * Hot Standby
    * 전원이 켜저 있는 상태에서 항시대기
    * Active가 죽었을 때, 복구하는 시간이 빠르다
    * 전기세가 많이 나오는 단점
  * Cold Standby
    * 전원이 꺼져 있는 상태에서 대기
    * 전원이 꺼져 있으므로 비용 발생이 없다

<br>

* DB에서 알아두면 좋은 것들
  * 구성 방식
    * 클러스터링 : 서버들을 풀에 등록하고 작동 - 한 대처럼 운영한다
    * DB운영: Standby-Standby 방식(하나로 주로 처리하다가 고장나면 다른놈이 작동)을 많이 씀 <br><br>
  * 안정화
    * Replication: Master/Slave로 2 대로 구성하여 백업 자동화, Master에서만 Write가 가능하고, Slave에 이를 복제하는 방식
    * Galera Cluster: Replication과는 다르게 **모든 노드에서 Write가 가능**하다

<br>
<hr>

## 네트워크에서 사용하는 고가용성

![img_339](https://user-images.githubusercontent.com/104918800/178779027-fa1e2eff-3dde-4591-a5a1-664fac753f60.png)

* FHRP: First Hop Redundancy Protocol
  1. Hot Standby Router(Routing 아님) Protocol - Cisco 전용, 벤더 의존적인 프로토콜
  2. Virtual Router Redundancy Protocol - 표준 프로토콜
  3. 사실 두개 기능 똑같이 쓰임

<br>

* FHRP 작동 방식
  * ![img_340](https://user-images.githubusercontent.com/104918800/178779029-66489db0-41d2-4aff-aed9-f6d56086f3c2.png)

<br>

* HSRP
  * 일반적인 FHRP은 로드밸런싱을 지원하지 않음(한쪽이 문제있으면 다른쪽으로 보내기만 함)
  * HSRP에서는 Vlan별 별도의 Active를 두고 이중화를 구현한다
  * 안정적으로 24시간 OnLine(인터넷 연결 가능)을 유지시키기 위해 FHRP를 사용한다

<br>

* HSRP 작동 예시
  * ![img_341](https://user-images.githubusercontent.com/104918800/178779033-a367eeed-9721-4d91-8c5a-28130c19c38c.png)

<br>
<hr>

## FHRP 실습

1 . 다음과 같이 구성한다
* ![img_334](https://user-images.githubusercontent.com/104918800/178779036-9a993c00-595b-4cf0-b6c4-dec6799485ee.png)

<br>

2 . 외부 IP 구성
* KT는 실제 상황을 가정한 네이밍임

```commandline
R1#conf t
R1(config)#hostname KT
KT(config)#int f0/0
KT(config-if)#ip add 192.168.1.105 255.255.255.0
KT(config-if)#no sh

R2#conf t
R2(config)#hostname SK
SK(config)#int fa0/0
SK(config-if)#ip add 192.168.1.205 255.255.255.0
SK(config-if)#no sh
```

<br>

3 . 내부 IP 구성

```commandline
KT(config-if)#int f0/1
KT(config-if)#ip add 172.16.1.101 255.255.255.0
KT(config-if)#no sh
KT(config)#do ping 192.168.1.205

SK(config-if)#int f0/1
SK(config-if)#ip add 172.16.1.102 255.255.255.0
SK(config-if)#no sh
SK(config)#do ping 192.168.1.105
```

<br>

4 . 기본 라우트 구성

```commandline
KT(config-if)#ip route 0.0.0.0 0.0.0.0 192.168.1.1
KT(config)#do ping 8.8.8.8

SK(config)#ip route 0.0.0.0 0.0.0.0 192.168.1.1
SK(config)#do ping 8.8.8.8
```

<br>

5 . NAT 구성 (우리는 Dynamic PAT 구성할 것)
* 내부PC와 접속하기 위함
* 양쪽에 둘다 집어넣어야 함

```commandline
KT(config)#access-list 1 permit any
KT(config)#ip nat inside source list 1 int fa0/0 overload
KT(config)#int fa0/0
KT(config-if)#ip nat outside
KT(config-if)#int fa0/1
KT(config-if)#ip nat inside

SK(config)#access-list 1 permit any
SK(config)#ip nat inside source list 1 int fa0/0 overload
SK(config)#int fa0/0
SK(config-if)#ip nat outside
SK(config-if)#int fa0/1
SK(config-if)#ip nat inside
```

<br>

6 . centOS 1번 실행
* gateway 주소 변경 <br> ![img_335](https://user-images.githubusercontent.com/104918800/178779040-bb9b5d8e-9131-4fcd-86aa-8b2737dde826.png)
* 라우팅 테이블 확인  <br> ![img_337](https://user-images.githubusercontent.com/104918800/178779047-cafbb633-a0e0-4c0a-b28f-d3da89b3e5bb.png)

<br>

7 . 기본 게이트웨이의 STANDBY / ACTIVE 설정!

```commandline
KT(config-if)#int fa0/1
KT(config-if)#standby 10 ip 172.16.1.254

SK(config-if)#int fa0/1
SK(config-if)#standby 10 ip 172.16.1.254
```

<br>

8 . 위 불안정적 상태를 안정적으로 Active하게 만들자
* 우선순위 값을 지정한다 (클수록 우선)
* 우선순위 권한을 언제든 넘길 수 있도록 설정한다
  * KT로 권한이 넘어오게 된다
* track으로 줄일 우선 순위 값을 설정 할 수 있음

```commandline
KT(config-if)#standby 10 priority 110

SK(config-if)#standby 10 preempt
KT(config-if)#standby 10 preempt

KT(config-if)#standby 10 track f0/0 20
```

<br>

9 . KTISP를 통하는 라우터를 종료시켜 보면
* KT는 Standby가 되며, SK는 Active가 된다
* 다시 KT를 켜면, Active를 되찾아 온다
* ![img_336](https://user-images.githubusercontent.com/104918800/178779044-40042afe-f0e9-4842-b95b-9ccf5a3a13cc.png)

```commandline
KT(config)#int f0/0
KT(config-if)#sh

KT(config-if)#no sh
```

<br>
<hr>

## Dynamic Routing 실습


* Dynamic Routing
  * 라우터 사이에서 라우팅 프로토콜을 이용해서 네트워크 정보를 교환한다
  * 목적지로 찾아갈 수 있는 최적 경로를 선택한 다음, 그 최적 경로를 라우팅 테이블에 Install함
  * OSPF(Open Shortest Path First) : 표준프로토콜

* <br>

1 . 다음과 같이 구성한다
* ![img_338](https://user-images.githubusercontent.com/104918800/178779022-d611ab24-e7a8-4d84-89a3-44c491dc47c7.png)

<br>

2 . Router1

```commandline
router ospf 1
network 192.168.1.0 0.0.0.255 area 0
network 10.10.10.0 0.0.0.3 area 0
```

<br>

3 . Router2

```commandline
router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 10.10.10.0 0.0.0.3 area 0
```
* 구성 완료

<br>

