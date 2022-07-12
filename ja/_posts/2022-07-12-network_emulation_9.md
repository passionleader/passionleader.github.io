---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation8
# 영문으로 작성하는 경우 영문제목
title: 네트워크 애뮬레이션 실습9 - 지금껏 배운 것 + ACL

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [Network, Switch, VLAN, linux, GNS3, ACL, Router, DHCP]

# 섬네일 이미지
img: "https://www.gns3.com/assets/custom/gns3/images/logo-colour.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-12 18:48:51 +0900

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

[카카오 클라우드 스쿨] 지금껏 배운 IP세팅, Routing, DHCP, NAT을 설정 후, ACL을 설정해 보자

<!-- outline-end -->

# ACL 실습 (총 실습)

1. 다음과 같이 구성한다
   * ![img_317.png](img_317.png) <br> DSW: Router로 만듦, endpoint: Cloud로 만듦
   * ![img_315.png](img_315.png) & ![img_316.png](img_316.png)
   * 실수를 하나 했는데, 서버 쪽은 vmnet10이 아니라 1임 <br> <br>

2. 시나리오
   * 1
   * 우리 회사는 본사 지사간 WAN으로 연결됨
   * 2
   * 본사, 지사는 공인주소 211.183.3.0/24를 사용할 수 있다
   * 211.183.3.2은 ISP주소, 211.183.3.3은 라우터 주소
   * 211.183.3.1은 ISP와 우리 라우터간 연결된 스위치 주소
   * 본사 내부에 있는 서버의 주소 172.16.1.100은 외부 통신시 211.183.3.100과 1:1로 정적 매핑된다
   * 본사 내부 PC들은 211.183.3.101 하나를 이용하여 인터넷을 사용한다
   * 지사 내부 PC들은 211.183.3.102 하나를 이용하여 인터넷을 사용한다 (동적 PAT 구성 필요)
   * 3
   * DSW1-DSW2에 연결된 서버들은 vlan10에 할당
   * PC들은 vlan20에 할당된다 (DSW 사이는 트렁크 포트 구성 필요)
   * 4
   * 본사와 지사에 위치한 PC들은 DHCP를 통하여 IP주소를 할당받는다
   * DHCP 서버는 본사에서 통합관리한다
   * HQ 라우터가 DHCP 서버로 동작한다 <br><br>

3. 우리가 할 순서
   * IP설정 -> 라우팅 -> NAT 구성 -> 필터링, 보안 <br><br>

4. 이렇게 해야 할 듯
   * ![img_319.png](img_318.png)
   * ![img_319.png](img_319.png) <br><br>

<br>
<hr>

## 1. IP구성

1 . HQ 메인 인터페이스 구성

```commandline
HQ#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
HQ(config)#int f0/0
HQ(config-if)#ip add 211.183.3.3 255.255.255.0
HQ(config-if)#no sh

HQ(config-if)#int f1/0
HQ(config-if)#ip add 10.10.10.1 255.255.255.252
HQ(config-if)#no sh


HQ(config)#int f0/1
HQ(config-if)#no sh
```

* 10.10.10./30 -> 10.10.10.0 -> N.N.N.nnnnnnhh -> 0, 4, 8 ...
  * 실제 사용할 수 있는 주소: 2개

<br>

2 . HQ 서브 인터페이스 설정

```commandline
HQ#conf t
HQ(config)#int fa0/1.10
HQ(config-subif)#encap dot1q 10
HQ(config-subif)#ip add 172.16.1.2 255.255.255.0

HQ(config-subif)#int fa0/1.20
HQ(config-subif)#encap dot1q 20
HQ(config-subif)#ip add 172.16.2.2 255.255.255.0

HQ(config-subif)#do show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            211.183.3.3     YES manual up                    up
FastEthernet0/1            unassigned      YES unset  up                    up
FastEthernet0/1.10         172.16.1.2      YES manual up                    up
FastEthernet0/1.20         172.16.2.2      YES manual up                    up
FastEthernet1/0            10.10.10.1      YES manual up                    up
```

<br>

3 . 현재까지의 구성 요소 <br> ![img_321.png](img_321.png)

<br>

4 . BR 메인 인터페이스 구성

```commandline
BR#conf t
BR(config)#int f0/0
BR(config-if)#ip add 10.10.10.2 255.255.255.252
BR(config-if)#no sh

BR(config-if)#int fa0/1
BR(config-if)#ip add 192.168.105.2 255.255.255.0
BR(config-if)#no sh
```

<br>

5 . HQ에서 동일 구간 통신 가능한 지 확인 (10.1에서 10.2로)

```commandline
HQ(config-subif)#do ping 211.183.3.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 211.183.3.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 4/44/60 ms

HQ(config-subif)#do ping 10.10.10.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.10.10.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 60/66/84 ms

```

* IP 입력 끝!

<br>
<hr>

## VLAN 구성
1 . DSW1의 vlan 생성한다

```commandline
DSW1#vlan dat
DSW1(vlan)#vlan 10 name SERVER
VLAN 10 added:
    Name: SERVER
DSW1(vlan)#vlan 20 name PC
VLAN 20 added:
    Name: PC
DSW1(vlan)#exit
APPLY completed.
Exiting....
```

<br>

2 . 인터페이스와 트렁크,액세스 구성하기

```commandline
DSW1#conf t
DSW1(config)#int f1/10
DSW1(config-if)#sw mode trunk
DSW1(config-if)#int f1/15
DSW1(config-if)#sw mode trunk

DSW1(config-if)#do show int trunk
Port      Mode         Encapsulation  Status        Native vlan
Fa1/10    on           802.1q         trunking      1
Fa1/15    on           802.1q         trunking      1
Port      Vlans allowed on trunk
Fa1/10    1-1005
Fa1/15    1-1005
Port      Vlans allowed and active in management domain
Fa1/10    1,10,20
Fa1/15    1,10,20
Port      Vlans in spanning tree forwarding state and not pruned
Fa1/10    none
Fa1/15    none

DSW1(config-if)#int f1/1
DSW1(config-if)#sw mode access
DSW1(config-if)#sw access vlan 10

DSW1(config-if)#do show vlan-sw br
VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa1/0, Fa1/2, Fa1/3, Fa1/4
                                                Fa1/5, Fa1/6, Fa1/7, Fa1/8
                                                Fa1/9, Fa1/11, Fa1/12, Fa1/13
                                                Fa1/14
10   SERVER                           active    Fa1/1
20   PC                               active


```

<br>

3 . DSW2에도 똑같이 구성함

```commandline
DSW2#vlan dat
DSW2(vlan)#vlan 10 name SERVER
VLAN 10 added:
    Name: SERVER
DSW2(vlan)#vlan 20 name PC
VLAN 20 added:
    Name: PC
DSW2(vlan)#exit
APPLY completed.
Exiting....

DSW2#conf t

DSW2(config)#int f1/15
DSW2(config-if)#sw mode trunk

DSW2(config-if)#int f1/2
DSW2(config-if)#sw mode access
DSW2(config-if)#sw access vlan 20
```

* vlan 구성 완료

<br>
<hr>

## 라우팅

* 라우터는 기본적으로 직접 연결된 네트워크에 대하여 통신이 가능하다.
* 떨어져 있는 네트워크에 대하여 해당 정보를 라우터에 입력이 되도록 해 주어야 함 (갈 수는 있는데 떨어져 있는 정보에 대해서는 바로 이해할 수가 없다)
  1. 정적 라우팅
     * 단방향
     * 관리자 또는 사용자가 원격지 네트워크 정보를 직접 입력하는 방식
     * 네트워크 정보를 유지하기 위하여 불필요한 트래픽이 발생하지 않으므로 빠르다
     * 일반적인 구성 방법
       * ip route 2.2.2.0 255.255.255.0 3.3.3.3
       * 2.2.2.0: 목적지 네트워크 주소, 목적지 IP 주소
       * 255.255.255.0: 서브넷 마스크, 1대만 지정하고 싶으면 255.255.255.255
       * 3.3.3.3: 목적지 네트워크로 찾아가기 위해 선택할 다음(붙어 있는) 라우터 주소  <br><br>

  2. 동적 라우팅
     * 라우터 간 라우팅 프르토콜을 사용하여 자신들이 알고 있는 네트워크 정보를 서로에게 광고한다
     * 동일 목적지에 대해서 여러 경로를 전달 받는다면, 경로 중 가장 빠른 경로를 선택한다
     * 그 가장 빠른 경로를 라우팅 테이블에 INSTALL 한다
     * 최신 경로를 계속해서 찾아 유지하려고 한다 (그래서 트래픽이 많이 발생할 수 있음) <br><br>

* 필요한 연결
  * ![img_320.png](img_320.png)
  * HQ
    * 인터넷과의 통신을 위해 Default Route
    * 지사 내부와의 통신을 위해 Static Route
  * BR
    * 본사를 향한 Default Route
  * 확인
    * HQ: do ping 192.168.1xx.2 source 172.16.1.2
    * BR: do ping 192.168.1xx.2 source 172.16.2.2 <br><br>
  * 동일 목적지에 대하여 서로 다른 방법으로 도달이 가능한 경우, 이에 대한 우선 순위가 있음
    * Connected: 0
    * Static Route: 1
    * Routing Protocol: 프로토콜 별 상이, (RIP: 120, OSFP: 110)
    * => 숫자가 작은 것이 우선적으로 선택됨! <br><br>

* 공인과 사설
  * 공인
    * ISP에게 비용을 지불하고 인터넷 서비스를 제공받을 수 있음
    * 인터넷 공간에서 unique한 주소를 사용해야 함
  * 사설
    * 별도의 제약 없이 내부에서 원하는대로 사용 가능
      * A: 10.X.X.X
      * B: 172.16.X.X ~ 172.31.X.X
      * C: 192.168.X.X <br> <br>
    * 사설 IP 주소가 그대로 ISP로 들어가면, ISP에서 차단한다
      * 이는 ISP측에서 사설 IP를 필터링 하는 것 - NAT 필요하다

<br>

1 . 본사 디폴트(인터넷으로), 정적(지사로)

```commandline
HQ#conf t
HQ(config)#ip route 0.0.0.0 0.0.0.0 211.183.3.2
HQ(config)#ip route 192.168.105.0 255.255.255.0 10.10.10.2

HQ(config)#do ping 192.168.105.2 source 172.16.2.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.105.2, timeout is 2 seconds:
Packet sent with a source address of 172.16.2.2
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/61/64 ms

HQ(config)#do ping 192.168.105.2 source 172.16.1.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.105.2, timeout is 2 seconds:
Packet sent with a source address of 172.16.1.2
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 60/60/64 ms
```

<br>

2 . 지사 정적

```commandline
BR#conf t
BR(config)#ip route 0.0.0.0 0.0.0.0 10.10.10.1
```

* 라우팅 완료

<br>
<hr>


## NAT 구성


1 . 1:1로 정적 NAT 매핑시키자 (내부:외부)
* IP에 대하여 Pool에 저장된 여러 외부 IP로 나가게 할 수도 있음

```commandline
# HQ(config)#ip nat pool HQSRV 211.183.3.100 211.183.3.100 prefix-length 24
HQ(config)#ip nat inside source static 172.16.1.100 211.183.3.100
```

<br>

2 . inside 및 outside 구성하기
* 참고로 inside는 다음 그림과 같이 세 개임
* ![img_322.png](img_322.png)

```commandline
HQ(config-if)#int fa0/0
HQ(config-if)#ip nat outside
HQ(config-if)#int fa1/0
HQ(config-if)#ip nat inside
HQ(config-if)#int fa0/1.10
HQ(config-subif)#ip nat inside
HQ(config-subif)#int fa0/1.20
HQ(config-subif)#ip nat inside
```

<br>

3 . VMware CentOS1에서 핑 확인하기

```bash
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=127 time=62.6 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=127 time=56.0 ms
```

* NAT 구성 완료

<br>

4 . 외부에서 ssh로 접속해 보기
* 아래와 같이 KEX 설정 후, 211.183.3.100 에 SSH로 접속해 보자
* ![img_332.png](img_332.png)
* ![img_331.png](img_331.png)

<br>
<hr>


## DHCP 구성
* DHCP 서비스
  * 서버-DHCP-클라이언트
  * 클라이언트는 부팅 시 DHCP 프로토콜 내에 Discover 메시지를 포함하여 목적지 MAC, FF:FF:FF:FF:FF:FF(L2 브로드캐스트 주소)로 전송한다

<br>

* ![img_323.png](img_323.png)
* DHCP 시나리오
  1. 본사 내부는 172.16.2.0/24 에서 주소를 할당받는다. 지사내부는 192.168.1XX.0/24 할당받는다.
  2. 각 네트워크에서 1~100 까지는 내부적으로 사용이 예약되어 있어 실제로 클라이언트들이 제공받는 주소는 172.16.1.101~ 과 192.168.1XX.101~ 가 된다
  3. 임대기간은 2시간이고
  4. dns 서버는 8.8.8.8로 한다


<br>

1 . 먼저 제외할 주소 지정
* 클라이언트가 요청하더라도 제공하지 않는 주소
* 이 범위 내에는 프린터 등의 정적 할당에 쓸 것임

```commandline
HQ#conf t
HQ(config)#ip dhcp excluded-address 172.16.2.1 172.16.2.100
HQ(config)#ip dhcp excluded-address 192.168.105.1 192.168.105.100
```

<br>

2 . 본사를 위한 DHCP 서버 구성 (centOS2 용)

```commandline
HQ(config)#ip dhcp pool HQ
디폴트 게이트웨이
HQ(dhcp-config)#default-router 172.16.2.2
임대기간 2시간
HQ(dhcp-config)#lease 0 2
DNS 설정
HQ(dhcp-config)#dns 8.8.8.8
```

<br>

3 . CentOS2를 full Clone하여 CentOS3을 생성
* CentOS3에서 vmnet2 인터페이스를 vmnet3으로 변경한다
* CentOS2,3을 모두 실행하고 interface설정 창에서 정적 구성을 DHCP로 변경하여 IP주소를 받아 오는지 여부 확인
    * ![img_324.png](img_324.png)
    * ![img_325.png](img_325.png)

<br>

4 . 지사를 위한 DHCP 서버 구성 (centOS3 용)

```commandline
HQ(config)#ip dhcp pool BR
HQ(dhcp-config)#network 192.168.105.0 /24
HQ(dhcp-config)#default-router 192.168.105.2
HQ(dhcp-config)#lease 0 2
HQ(dhcp-config)#dns 8.8.8.8
```

<br>

5 . 지사의 fa0/1 에서 DHCP Rely agent 설정

```commandline
BR(config)#int fa0/1
BR(config-if)#ip helper-address 10.10.10.1
```

* CentOS3에서 동적 할당된 것이 확인됨

<br>
<hr>



## PC들을 위한 동적 PAT 구성
* 즉, 다음 조건을 위한 구성
  * 본사 내부 PC들은 211.183.3.101 하나를 이용하여 인터넷을 사용한다
  * 지사 내부 PC들은 211.183.3.102 하나를 이용하여 인터넷을 사용한다
  * ![img_327.png](img_327.png)

<br>

* 각각의 사설 주소를 ACL을 이용하여 분류하기

<br>

1 . 각 사설 네트워크에서 사용할 공인주소를 Pool로 구성

```commandline
HQ#conf t
HQ(config)#do show run | in ip nat pool
ip nat pool HQSRV 211.183.3.100 211.183.3.100 prefix-length 24

HQ(config)#ip nat pool HQPC 211.183.3.101 211.183.3.101 pre 24
HQ(config)#ip nat pool BRPC 211.183.3.102 211.183.3.102 pre 24
HQ(config)#do show run | in ip nat pool
ip nat pool HQSRV 211.183.3.100 211.183.3.100 prefix-length 24
ip nat pool HQPC 211.183.3.101 211.183.3.101 prefix-length 24  -> 본사 공인주소!
ip nat pool BRPC 211.183.3.102 211.183.3.102 prefix-length 24  -> 지사 공인주소!
```

<br>

2 . 액세스 리스트 생성, 본사/지사의 주소 포함

```commandline
HQ(config)#ip access-list standard hqadd
HQ(config-std-nacl)#permit 172.16.2.0 0.0.0.255  -> 본사 사설주소!

HQ(config)#ip access-list standard bradd
HQ(config-std-nacl)#permit 192.168.105.0 0.0.0.255  -> 지사 사설주소!
```
* 본사, 지사 내부의 주소를 분류함

<br>

3 . 매핑

* 본사 사설 주소를 hqadd에, 본사 공인 주소를 HQPC에
* Overload: 한 개의 공인주소를 공유해서 사용하도록 하겠다는 뜻

```commandline
HQ(config)#ip nat inside source list hqadd pool HQPC overload
HQ(config)#ip nat inside source list bradd pool BRPC overload
```

<br>

4 . 확인해 보기
* CentOS 2&3에서 naver.com 접속 시
```commandline
HQ#show ip nat tr | in 172.16.2.101  (본사에서 접속)
HQ#show ip nat tr | in 192.168.105.101 (지사에서 접속)

udp 211.183.3.101:33250 172.16.2.101:33250 8.8.8.8:53        8.8.8.8:53
udp 211.183.3.101:33865 172.16.2.101:33865 8.8.8.8:53        8.8.8.8:53
udp 211.183.3.101:35104 172.16.2.101:35104 8.8.8.8:53        8.8.8.8:53
tcp 211.183.3.101:37600 172.16.2.101:37600 142.250.196.138:443 142.250.196.138:443
tcp 211.183.3.101:37620 172.16.2.101:37620 172.217.174.100:443 172.217.174.100:443
tcp 211.183.3.101:37622 172.16.2.101:37622 172.217.174.100:443 172.217.174.100:443
tcp 211.183.3.101:37626 172.16.2.101:37626 172.217.174.100:443 172.217.174.100:443
tcp 211.183.3.101:37632 172.16.2.101:37632 172.217.174.100:443 172.217.174.100:443
tcp 211.183.3.101:37640 172.16.2.101:37640 172.217.174.100:443 172.217.174.100:443
tcp 211.183.3.101:38656 172.16.2.101:38656 104.18.32.68:80   104.18.32.68:80
udp 211.183.3.101:39573 172.16.2.101:39573 8.8.8.8:53        8.8.8.8:53
tcp 211.183.3.101:40258 172.16.2.101:40258 142.250.196.131:80 142.250.196.131:80
tcp 211.183.3.101:40268 172.16.2.101:40268 142.250.196.131:80 142.250.196.131:80
tcp 211.183.3.101:40270 172.16.2.101:40270 142.250.196.131:80 142.250.196.131:80
tcp 211.183.3.101:40276 172.16.2.101:40276 142.250.196.131:80 142.250.196.131:80
tcp 211.183.3.101:40278 172.16.2.101:40278 142.250.196.131:80 142.250.196.131:80
tcp 211.183.3.101:40646 172.16.2.101:40646 54.149.98.7:443   54.149.98.7:443
```

<br>
<hr>

## ACL 적용하기 (필터링, 보안)
* 실제로는 Standard 를 쓰는 경우는 없음

> AWS와 같은 클라우드 환경에서의 기본 보안 사항
1. NACL (매우 범용적으로 씀)
   * VPC 내에 여러개의 서브넷을 생성하고, 각 서브넷에서 다른 서브넷으로의 통신 시 필터링이 필요한 경우 NACL을 적용한다
   * 주된 목적은 서브넷 단위에서의 패킷 필터링이며, 아웃바운드 적용을 주로 적용한다 <br> <br>
2. Security-Group (NACL 말고 이것만 적용하는 경우가 많음)
   * 서브넷 단위가 아닌 인스턴스 단위로 보안 사항을 적용하고자 하는 경우 사용
   * 여러 인스턴스에 하나의 Security 그룹을 적용할 수도 있다
   * 주로 inbound에 대해 적용하고, outbound는 기본적으로 permit any(모두 허용) <br> <br>
3. IAM(계정관리)
   * root 사용자
     * aws 모든 서비스에 대해 접근이 가능
     * 계정 추가, 삭제, 계정에 대한 권한부여 등을 진행
     * 즉, linux의 superuser인 root와 동일하다고 보면 됨 <br> <br>
   * IAM 사용자
     * 특정 aws root 계정 하에서 생성된 일반 사용자 (db 관리, 개발, 운영자 구분 가능)
     * 각 계정별로 접근할 수 있는 서비스에 대해 지정할 수 있고, 해당 사용자가 로그인 하는 방법도 선택할 수 있다
     * access key ID, access key를 사용하여 자신의 개발환경(혹은 linux와 같은 환경)에서 자신의 로컬 환경과 aws 환경을 연결하여 사용할 수 있음
     * ![img_328.png](img_328.png) <br> <br>

<br>

**첫 번째 임무** <br>
* 회사 내에 있는 IP 주소들이 NAT 되지 않은 상태에서 외부로 빠져나가려고 한다면, 이는 모두 차단해야 한다


1 . NAT 적용이 되지 않은 사설 네트워크 주소들을 차단하겠다

```commandline
HQ#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
HQ(config)#acc
HQ(config)#access-list 101 deny ip 172.16.1.0 0.0.0.255 any
HQ(config)#access-list 101 deny ip 172.16.2.0 0.0.0.255 any
HQ(config)#access-list 101 deny ip 192.168.105.0 0.0.0.255 any
HQ(config)#access-list 101 deny ip 10.0.0.0 0.255.255.255 any

HQ(config)#int fa0/0
HQ(config-if)#ip access-group 101 out

HQ(config)#access-list 101 permit ip any any

```

<br>

2 . 리눅스에서 구글 접속시 성공적으로 필터링 되는 것이 확인된다

```bash
[root@localhost ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
From 172.16.1.2 icmp_seq=1 Packet filtered
From 172.16.1.2 icmp_seq=2 Packet filtered
From 172.16.1.2 icmp_seq=3 Packet filtered
```

<br>

3 . BR도 Unreachable 로 차단된 모습을 볼 수 있음

```commandline
BR(config-if)#do ping 8.8.8.8

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
UUUUU
Success rate is 0 percent (0/5)
```

<br>
<br>

**두 번째 상황** <br>
* fa0/1.10 아래에는 우리회사 본사에 있는 웹서버가 위치하고 있다
* 이 웹서버는 원격 관리를 위해 ssh를 사용중이고, 아무나 접속해서는 안된다
* 오직 외부(인터넷)에 있는 211.183.3.1(퇴근한 서버관리자의 집 IP 주소)와 본사 내부에 있는 172.16.2.100에서는 ssh 연결이 가능하다
* 다른 곳에서의 ssh 연결은 모두 차단된다
* ssh를 제외한 나머지 모든 접근 (172.16.1.0/24)는 허용된다
* 이를 fa0/1.10에 적용해 봐라
* (참고 예시)
  * access-list 112 permit tcp any host 2.2.2.2 eq 80) 외부 모든 IP로부터 포트 80으로 들어온 TCP 허용
  * access-list 112 permit tcp host 3.3.3.3 host 4.4.4.4 eq 22)
  * access-list 112 permit ip any any
* (확인 방법)
  * 윈도우에서 211.1.83.3.100으로 ssh http ping 연결 가능함 <br> <br>
  * 172.16.1.101,172.16.2.101 에서는 211.183.3.100 으로 ssh 연결불가. 단, http/ping 은 가능
  * 172.16.1.101 을 정적으로 172.16.1.100 으로 IP 주소를 변경한 다음 ssh 연결 시도하면 가능!

1 . acl 설정

```commandline
HQ(config)#access-list 102 permit tcp host 211.183.3.1 host 172.16.1.100 eq 22
HQ(config)#access-list 102 permit tcp host 172.16.2.100 host 172.16.1.100 eq 22
HQ(config)#access-list 102 deny tcp any host 172.16.1.100 eq 22
access-list 102 permit ip any any
```

<br>


2 . 적용

```commandline
HQ(config)#int fa0/1.10
HQ(config-subif)#ip access-g
HQ(config-subif)#ip access-group 102 out
```

<br>

3 . CentOS2,3 에서 확인하기
* ssh 차단, ICMP 등 허용된 상태
```bash
[root@localhost ~]# ping 172.16.1.100
PING 172.16.1.100 (172.16.1.100) 56(84) bytes of data.
64 bytes from 172.16.1.100: icmp_seq=1 ttl=62 time=50.6 ms
64 bytes from 172.16.1.100: icmp_seq=2 ttl=62 time=51.7 ms

--- 172.16.1.100 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 50.672/51.235/51.799/0.607 ms

[root@localhost ~]# ssh -l root 172.16.1.100
ssh: connect to host 172.16.1.100 port 22: No route to host
```

<br>

4 . 외부에서 접속하기
* putty 로 ssh 접속 시도(내 PC)
* ![img_329.png](img_329.png)

<br>

5 . 정리
* ![img_330.png](img_330.png)


