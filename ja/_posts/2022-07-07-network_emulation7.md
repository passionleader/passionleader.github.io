---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_emulation7
# 영문으로 작성하는 경우 영문제목
title: [네트워크]애뮬레이션 실습7 - DHCP

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, GNS3, router]

# 섬네일 이미지
img: "https://www.gns3.com/assets/custom/gns3/images/logo-colour.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-07 17:13:25 +0900

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

[카카오 클라우드 스쿨] DHCP 서버 및 할당 실습

<!-- outline-end -->




# 네트워크 애뮬레이션 실습7 - DHCP

* 자세한 내용은 동기님의 블로그를 참고하세요
  * https://velog.io/@ptah0414/22-07-08-TIL

<br>

* DHCP 서버 구성해 보기
  * 시나리오
    1. 우리 회사는 HQ에서 DHCP 서비스를 제공한다
    2. HQ는 모든 부서(지점 포함)에 동적으로 IP를 부여한다
    3. IP 부여시 해당 네트워크 전체에서 주소를 부여하고, 주소와 함께 서브넷마스크, 게이트웨이, DNS(8.8.8.8), IP 임대 기간(2시간)

<br>

1 . 개발팀의 DHCP 구성하기

```commandline
ip dhcp pool DEV
network 192.168.2.0/25
default-router 192.168.2.1
dns 8.8.8.8
lease 0 2
```

* lease: 임대기간을 0일 2시간으로 설정

<br>

2 . 운영팀의 DHCP 구성하기

```commandline
ip dhcp pool OPS
network 192.168.2.128/27
default-router 192.168.2.129
dns 8.8.8.8
lease 0 2
```

<br>

3 . 마케팅팀의 DHCP 구성하기

```commandline
ip dhcp pool SALES
network 192.168.2.160/27
default-router 192.168.2.161
dns 8.8.8.8
lease 0 2
```

<br>

4 . 서버의 PC에서 dhcp 할당요청

```commandline
ip dhcp
```
* DDORA 라고 표시됨
  * Discover,Discover Request, Ack.. 이런 것들이 뜨는거임.
  * 이제 IP를 받아 오기 완료함

<br>

5 . 연결 테스트

```commandline
show ip
  ping 192.168.2.2
```

* 잘 간다
* 하지만 DHCP 서버는 본사에 있기 때문에 지사 PC에서는 아직 DHCP 할당이 잘 되지 않는다


<br>

DHCP 클라이언트는 아래와 같은 Discover를 발생시킨다. <br>
* 목적지 주소가 255.255.255.255로 IPv4의 브로드캐스트 주소이다.
* 라우터는 브로드캐스트 주소를 다른 네트워크로 전달하지 않는다
* 즉, 라우터는 L3 브로드캐스트를 다른 네트워크로 넘어가지 않도록 차단시킨다
* 따라서 다른 네트워크에 있는 DHCP 서버로 Discover 메시지가 전달되지 않아 IP 주소를 할당 받을 수 없게 된다
  * => 중개 역할을 하는 Relay-agent가 필요하다 <br>

따라서 BR(지사의 라우터)가 이 역할을 수행하면 됨

```commandline
BR1(config)#int fa0/1
BR1(config-if)#ip help
BR1(config-if)#ip helper-address 192.168.2.193
```

* 이제 지사의 PC에서 DHCP를 찾아서 발급받을 수 있게 되었다
