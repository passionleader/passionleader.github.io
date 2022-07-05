---
# 속성은 대쉬 내부에 작성하세요

# 영문으로 같은 내용 작성 시 id 같아야 함
lng_pair: id_kakao_virtualization
# 영문으로 작성하는 경우 영문제목
title: 네트워크 에뮬레이션 관련 개념정리

# 저자 설정(생략 가능)
#author: initializer

# 카테고리와 태그 설정
category: 카카오클라우드스쿨
tags: [network, linux, GNS3, Wireshark]

# 섬네일 이미지
img: "https://conceptdraw.com/a1778c3/p1/preview/640/pict--router-cisco-routers---vector-stencils-library.png--diagram-flowchart-example.png"

# 댓글 비활성화 여부
comments_disable: false

# 작성 날짜
date: 2022-07-05 10:03:22 +0900

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

[카카오 클라우드 스쿨] 네트워크 에뮬레이션 - 개념 살펴 보기

<!-- outline-end -->


# 네트워크 에뮬레이션 - 개념 미리 살펴 보기
> 적용된 개념을 살펴 보기


## 1. 네트워크 인터페이스(개념)
* NIC의 가상화 방법
  * ![img_73](https://user-images.githubusercontent.com/104918800/177329536-e7c3891b-0a41-4673-96e0-ad519a8a873b.png)
  * VM ware는 OS 커널의 허락을 받아서 Physical NIC의 자원을 조금 할당받는다 <br> <br>
* vNIC의 세 가지 종류 <br> ![img_79](https://user-images.githubusercontent.com/104918800/177329553-8b99cf50-86b8-4a82-aadc-0a5d9b751e6b.png)
  1. host-only(isolated)
     * 내부 주소 대역들 끼리만 통신이 가능함
     * 외부와는 통신이 불가능(인터넷 사용 불가)
  2. NAT(network address translation) - 원래의 TCP IP를 다른 IP로 바꾸겠다
     * 데이터베이스로 연결할 때에는 외부로 직접 연결되면 안되므로 NAT로 구성해야 함
     * 내부에서 외부로 접속할 수 있지만, 외부에서 내부로 접속하기 위해서는 포트포워딩 필요
     * NAT을 중심으로 공인 IP단과 사설 IP단이 구분된다
  3. bridge(Switch) - 동일 네트워크 환경에 있는 end-devices를 연결해 주는 역할
     * Host OS와 동일한 네트워크에 연결됨
     * 인터넷 사용 가능!

<br>

> 번외) LoadBalancer
* 외부 네트워크와 서버 사이에는 로드밸런서가 필요함
* Application Load Balancers(L7)
  * L7 네트워크 계층에서 동작
  * 경로 기반 라우팅이 지원됨(확장자)
  * 80번 포트로 들어오는 것에 대한 라우팅(해당 앱으로 라우팅)
* Network Load Balancer(L4)
  * L4 네트워크 계층에서 동작
  * 로드밸런서에 대한 고정 IP 주소 지원
  * 80번 포트로 들어오는 것에 대한 서버 분산만
* Gateway Load Balancer
* Classic Load Balancer


<br>
<hr>


## 2. 웹서버 접속 과정(개념)
![img_107](https://user-images.githubusercontent.com/104918800/177329630-1b41ae19-1570-4d03-8e19-52a89efdbad2.png)

<br>
<hr>


## 3. IP의 구분
* IP는 ISP에게 비용을 지불했는지 여부에 따라 공인IP(지불함), 사설IP(지불하지 않음, 인터넷상 사용불가)로 분류한다
* 대표적인 사설 주소 목록
  * 10.x.x.x
  * 172.16.x.x ~ 172.31.x.x
  * 192.168.x.x
* 서버를 사용해야 하는데 PC를 재부팅 할때마다 IP주소가 바뀜
* 심지어 마이그레이션 하고 같은 주소를 써야 하면 어떻게 할까
  * 고정하는 방법이 없을까? -> 정적  NAT 사용

<br>
<hr>


## 4. NAT과 PAT
* NAT(network address translateion)
  * 사설 IP 주소 하나를 특정 공인 IP 주소로 변경하는 것
  * 동적 NAT과 정적 NAT으로 구분된다
  * ![img_108](https://user-images.githubusercontent.com/104918800/177329632-b8f0e623-5322-4b3a-8e64-94cdd49f150a.png)
* PAT(port address translation)
  * 공인주소가 1개인 경우 다수의 사설 주소가 한 개의 공인 주소를 공유하여 주소 변경을 하고자 하는 경우에 사용하는 방법
  * ![img_109](https://user-images.githubusercontent.com/104918800/177329634-5c655180-5b58-42af-bee7-2115272b63e5.png)
* AWS의 경우 IP는 유동 NAT임
  * 인스턴스의 IP는 계속 바뀌므로, 서버 운영을 할 수가 없다
  * 주소 고정을 위해 Elastic IP 서비스를 사용해야 함 (1개는 무료)
  * 해당 IP를 인스턴스에게 연결하면 주소가 고정이 됨
* openstack을 통해 공인 IP를 할당하는 것 관리할 수 있다


